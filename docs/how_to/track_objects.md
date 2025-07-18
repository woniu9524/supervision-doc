---
comments: true
status: new
---

# 跟踪对象

利用 Supervision 的高级功能，通过无缝跟踪物体检测、分割（segmentation）和关键点模型识别的物体，来增强您的视频分析能力。本综合指南将引导您完成使用 YOLOv8 模型进行推理的步骤，您可以通过 [Inference](https://github.com/roboflow/inference) 或 [Ultralytics](https://github.com/ultralytics/ultralytics) 包来完成。在此之后，您将学习如何高效地跟踪这些对象并为您的视频内容添加注释，以进行更深入的分析。

## 对象检测与分割

为了方便您跟随我们的教程，请下载我们将用作示例的视频。您可以使用 [`supervision[assets]`](/latest/assets/) 扩展来完成此操作。

```python
from supervision.assets import download_assets, VideoAssets

download_assets(VideoAssets.PEOPLE_WALKING)
```

<video controls>
    <source src="https://media.roboflow.com/supervision/video-examples/people-walking.mp4" type="video/mp4">
</video>

### 运行推理

首先，您需要从您的对象检测或分割模型中获取预测结果。在本教程中，我们将使用 YOLOv8 模型作为示例。但是，Supervision 非常灵活，并且与各种模型兼容。请参阅此 [链接](/latest/how_to/detect_and_annotate/#load-predictions-into-supervision) 以获取有关如何集成其他模型的指导。

我们将定义一个 `callback` 函数，该函数将处理视频的每一帧，获取模型预测结果，然后根据这些预测对帧进行注释。此 `callback` 函数将在教程的后续步骤中至关重要，因为它将被修改为包含跟踪、标签和轨迹注释。

!!! tip

    支持对象检测和分割模型。可以尝试使用 `yolov8n.pt` 或 `yolov8n-640-seg`！

=== "Ultralytics"

    ```{ .py }
    import numpy as np
    import supervision as sv
    from ultralytics import YOLO

    model = YOLO("yolov8n.pt")
    box_annotator = sv.BoxAnnotator()

    def callback(frame: np.ndarray, _: int) -> np.ndarray:
        results = model(frame)[0]
        detections = sv.Detections.from_ultralytics(results)
        return box_annotator.annotate(frame.copy(), detections=detections)

    sv.process_video(
        source_path="people-walking.mp4",
        target_path="result.mp4",
        callback=callback
    )
    ```

=== "Inference"

    ```{ .py }
    import numpy as np
    import supervision as sv
    from inference.models.utils import get_roboflow_model

    model = get_roboflow_model(model_id="yolov8n-640", api_key=<ROBOFLOW API KEY>)
    box_annotator = sv.BoxAnnotator()

    def callback(frame: np.ndarray, _: int) -> np.ndarray:
        results = model.infer(frame)[0]
        detections = sv.Detections.from_inference(results)
        return box_annotator.annotate(frame.copy(), detections=detections)

    sv.process_video(
        source_path="people-walking.mp4",
        target_path="result.mp4",
        callback=callback
    )
    ```

<video controls>
    <source src="https://media.roboflow.com/supervision/video-examples/how-to/track-objects/run-inference.mp4" type="video/mp4">
</video>

### 跟踪

在运行推理并获取预测结果后，下一步是跟踪视频中检测到的对象。利用 Supervision 的 [`sv.ByteTrack`](/latest/trackers/#supervision.tracker.byte_tracker.core.ByteTrack) 功能，为每个检测到的对象分配一个唯一的跟踪 ID，从而能够持续跟踪对象在不同帧中的运动路径。

=== "Ultralytics"

    ```{ .py hl_lines="6 12" }
    import numpy as np
    import supervision as sv
    from ultralytics import YOLO

    model = YOLO("yolov8n.pt")
    tracker = sv.ByteTrack()
    box_annotator = sv.BoxAnnotator()

    def callback(frame: np.ndarray, _: int) -> np.ndarray:
        results = model(frame)[0]
        detections = sv.Detections.from_ultralytics(results)
        detections = tracker.update_with_detections(detections)
        return box_annotator.annotate(frame.copy(), detections=detections)

    sv.process_video(
        source_path="people-walking.mp4",
        target_path="result.mp4",
        callback=callback
    )
    ```

=== "Inference"

    ```{ .py hl_lines="6 12" }
    import numpy as np
    import supervision as sv
    from inference.models.utils import get_roboflow_model

    model = get_roboflow_model(model_id="yolov8n-640", api_key=<ROBOFLOW API KEY>)
    tracker = sv.ByteTrack()
    box_annotator = sv.BoxAnnotator()

    def callback(frame: np.ndarray, _: int) -> np.ndarray:
        results = model.infer(frame)[0]
        detections = sv.Detections.from_inference(results)
        detections = tracker.update_with_detections(detections)
        return box_annotator.annotate(frame.copy(), detections=detections)

    sv.process_video(
        source_path="people-walking.mp4",
        target_path="result.mp4",
        callback=callback
    )
    ```

### 使用跟踪 ID 注释视频

使用跟踪 ID 注释视频有助于区分和跟踪每个对象。借助 Supervision 的 [`sv.LabelAnnotator`](/latest/detection/annotators/#supervision.annotators.core.LabelAnnotator)，我们可以将跟踪 ID 和类别标签叠加到检测到的对象上，从而清晰地显示每个对象的类别和唯一标识符。

=== "Ultralytics"

    ```{ .py hl_lines="8 15-19 23-24" }
    import numpy as np
    import supervision as sv
    from ultralytics import YOLO

    model = YOLO("yolov8n.pt")
    tracker = sv.ByteTrack()
    box_annotator = sv.BoxAnnotator()
    label_annotator = sv.LabelAnnotator()

    def callback(frame: np.ndarray, _: int) -> np.ndarray:
        results = model(frame)[0]
        detections = sv.Detections.from_ultralytics(results)
        detections = tracker.update_with_detections(detections)

        labels = [
            f"#{tracker_id} {class_name}"
            for class_name, tracker_id
            in zip(detections.data["class_name"], detections.tracker_id)
        ]

        annotated_frame = box_annotator.annotate(
            frame.copy(), detections=detections)
        return label_annotator.annotate(
            annotated_frame, detections=detections, labels=labels)

    sv.process_video(
        source_path="people-walking.mp4",
        target_path="result.mp4",
        callback=callback
    )
    ```

=== "Inference"

    ```{ .py hl_lines="8 15-19 23-24" }
    import numpy as np
    import supervision as sv
    from inference.models.utils import get_roboflow_model

    model = get_roboflow_model(model_id="yolov8n-640", api_key=<ROBOFLOW API KEY>)
    tracker = sv.ByteTrack()
    box_annotator = sv.BoxAnnotator()
    label_annotator = sv.LabelAnnotator()

    def callback(frame: np.ndarray, _: int) -> np.ndarray:
        results = model.infer(frame)[0]
        detections = sv.Detections.from_inference(results)
        detections = tracker.update_with_detections(detections)

        labels = [
            f"#{tracker_id} {class_name}"
            for class_name, tracker_id
            in zip(detections.data["class_name"], detections.tracker_id)
        ]

        annotated_frame = box_annotator.annotate(
            frame.copy(), detections=detections)
        return label_annotator.annotate(
            annotated_frame, detections=detections, labels=labels)

    sv.process_video(
        source_path="people-walking.mp4",
        target_path="result.mp4",
        callback=callback
    )
    ```

<video controls>
    <source src="https://media.roboflow.com/supervision/video-examples/how-to/track-objects/annotate-video-with-tracking-ids.mp4" type="video/mp4">
</video>

### 使用轨迹注释视频

向视频添加轨迹涉及叠加检测对象的历史路径。此功能由 [`sv.TraceAnnotator`](/latest/detection/annotators/#supervision.annotators.core.TraceAnnotator) 提供，允许可视化对象的轨迹，有助于理解视频中对象之间的运动模式和交互。

=== "Ultralytics"

    ```{ .py hl_lines="9 26-27" }
    import numpy as np
    import supervision as sv
    from ultralytics import YOLO

    model = YOLO("yolov8n.pt")
    tracker = sv.ByteTrack()
    box_annotator = sv.BoxAnnotator()
    label_annotator = sv.LabelAnnotator()
    trace_annotator = sv.TraceAnnotator()

    def callback(frame: np.ndarray, _: int) -> np.ndarray:
        results = model(frame)[0]
        detections = sv.Detections.from_ultralytics(results)
        detections = tracker.update_with_detections(detections)

        labels = [
            f"#{tracker_id} {class_name}"
            for class_name, tracker_id
            in zip(detections.data["class_name"], detections.tracker_id)
        ]

        annotated_frame = box_annotator.annotate(
            frame.copy(), detections=detections)
        annotated_frame = label_annotator.annotate(
            annotated_frame, detections=detections, labels=labels)
        return trace_annotator.annotate(
            annotated_frame, detections=detections)

    sv.process_video(
        source_path="people-walking.mp4",
        target_path="result.mp4",
        callback=callback
    )
    ```

=== "Inference"

    ```{ .py hl_lines="9 26-27" }
    import numpy as np
    import supervision as sv
    from inference.models.utils import get_roboflow_model

    model = get_roboflow_model(model_id="yolov8n-640", api_key=<ROBOFLOW API KEY>)
    tracker = sv.ByteTrack()
    box_annotator = sv.BoxAnnotator()
    label_annotator = sv.LabelAnnotator()
    trace_annotator = sv.TraceAnnotator()

    def callback(frame: np.ndarray, _: int) -> np.ndarray:
        results = model.infer(frame)[0]
        detections = sv.Detections.from_inference(results)
        detections = tracker.update_with_detections(detections)

        labels = [
            f"#{tracker_id} {class_name}"
            for class_name, tracker_id
            in zip(detections.data["class_name"], detections.tracker_id)
        ]

        annotated_frame = box_annotator.annotate(
            frame.copy(), detections=detections)
        annotated_frame = label_annotator.annotate(
            annotated_frame, detections=detections, labels=labels)
        return trace_annotator.annotate(
            annotated_frame, detections=detections)

    sv.process_video(
        source_path="people-walking.mp4",
        target_path="result.mp4",
        callback=callback
    )
    ```

<video controls>
    <source src="https://media.roboflow.com/supervision/video-examples/how-to/track-objects/annotate-video-with-traces.mp4" type="video/mp4">
</video>

## 关键点

模型不仅限于对象检测和分割。关键点检测允许对身体关节和连接进行详细分析，这对于姿态估计等应用尤其有价值。本节介绍关键点跟踪。我们将引导您完成注释关键点、将其转换为与 `ByteTrack` 兼容的边界框检测以及应用检测平滑以增强稳定性的步骤。

为了方便您跟随我们的教程，让我们下载我们将使用的示例视频。您可以使用 [`supervision[assets]`](/latest/assets/) 扩展来完成此操作。

```python
from supervision.assets import download_assets, VideoAssets

download_assets(VideoAssets.SKIING)
```

<video controls muted>
    <source src="https://media.roboflow.com/supervision/video-examples/how-to/track-objects/skiing-hd.mp4" type="video/mp4">
</video>

### 关键点检测

首先，您需要从关键点检测模型中获取预测结果。在本教程中，我们将使用 YOLOv8 模型作为示例。但是，Supervision 非常灵活，并且与各种模型兼容。请参阅此 [链接](/latest/keypoint/core/) 以获取有关如何集成其他模型的指导。

我们将定义一个 `callback` 函数，该函数将处理视频的每一帧，获取模型预测结果，然后根据这些预测对帧进行注释。

让我们立即使用我们的 [`EdgeAnnotator`](/latest/keypoint/annotators/#supervision.keypoint.annotators.EdgeAnnotator) 和 [`VertexAnnotator`](https://supervision.roboflow.com/latest/keypoint/annotators/#supervision.keypoint.annotators.VertexAnnotator) 来可视化结果。

=== "Ultralytics"

    ```{ .py hl_lines="5 10-11" }
    import numpy as np
    import supervision as sv
    from ultralytics import YOLO

    model = YOLO("yolov8m-pose.pt")
    edge_annotator = sv.EdgeAnnotator()
    vertex_annotator = sv.VertexAnnotator()

    def callback(frame: np.ndarray, _: int) -> np.ndarray:
        results = model(frame)[0]
        key_points = sv.KeyPoints.from_ultralytics(results)

        annotated_frame = edge_annotator.annotate(
            frame.copy(), key_points=key_points)
        return vertex_annotator.annotate(
            annotated_frame, key_points=key_points)

    sv.process_video(
        source_path="skiing.mp4",
        target_path="result.mp4",
        callback=callback
    )
    ```

=== "Inference"

    ```{ .py hl_lines="5-6 11-12" }
    import numpy as np
    import supervision as sv
    from inference.models.utils import get_roboflow_model

    model = get_roboflow_model(
        model_id="yolov8m-pose-640", api_key=<ROBOFLOW API KEY>)
    edge_annotator = sv.EdgeAnnotator()
    vertex_annotator = sv.VertexAnnotator()

    def callback(frame: np.ndarray, _: int) -> np.ndarray:
        results = model.infer(frame)[0]
        key_points = sv.KeyPoints.from_inference(results)

        annotated_frame = edge_annotator.annotate(
            frame.copy(), key_points=key_points)
        return vertex_annotator.annotate(
            annotated_frame, key_points=key_points)

    sv.process_video(
        source_path="skiing.mp4",
        target_path="result.mp4",
        callback=callback
    )
    ```

<video controls>
    <source src="https://media.roboflow.com/supervision/video-examples/how-to/track-objects/track-keypoints-only-keypoints.mp4" type="video/mp4">
</video>

### 转换为检测结果

关键点跟踪目前通过将 `KeyPoints` 转换为 `Detections` 来支持。这是使用 [`KeyPoints.as_detections()`](/latest/keypoint/core/#supervision.keypoint.core.KeyPoints.as_detections) 函数实现的。

让我们将其转换为检测结果，并使用我们的 [`BoxAnnotator`](/latest/detection/annotators/#supervision.annotators.core.BoxAnnotator) 来可视化结果。

!!! tip

    您可以使用 `selected_keypoint_indices` 参数来指定要转换的子集。当某些关键点可能被遮挡时，这非常有用。例如：一个人可能会摆动手臂，导致肘部有时被躯干遮挡。

=== "Ultralytics"

    ```{ .py hl_lines="8 13 19-20" }
    import numpy as np
    import supervision as sv
    from ultralytics import YOLO

    model = YOLO("yolov8m-pose.pt")
    edge_annotator = sv.EdgeAnnotator()
    vertex_annotator = sv.VertexAnnotator()
    box_annotator = sv.BoxAnnotator()

    def callback(frame: np.ndarray, _: int) -> np.ndarray:
        results = model(frame)[0]
        key_points = sv.KeyPoints.from_ultralytics(results)
        detections = key_points.as_detections()

        annotated_frame = edge_annotator.annotate(
            frame.copy(), key_points=key_points)
        annotated_frame = vertex_annotator.annotate(
            annotated_frame, key_points=key_points)
        return box_annotator.annotate(
            annotated_frame, detections=detections)

    sv.process_video(
        source_path="skiing.mp4",
        target_path="result.mp4",
        callback=callback
    )
    ```

=== "Inference"

    ```{ .py hl_lines="9 14 20-21" }
    import numpy as np
    import supervision as sv
    from inference.models.utils import get_roboflow_model

    model = get_roboflow_model(
        model_id="yolov8m-pose-640", api_key=<ROBOFLOW API KEY>)
    edge_annotator = sv.EdgeAnnotator()
    vertex_annotator = sv.VertexAnnotator()
    box_annotator = sv.BoxAnnotator()

    def callback(frame: np.ndarray, _: int) -> np.ndarray:
        results = model.infer(frame)[0]
        key_points = sv.KeyPoints.from_inference(results)
        detections = key_points.as_detections()

        annotated_frame = edge_annotator.annotate(
            frame.copy(), key_points=key_points)
        annotated_frame = vertex_annotator.annotate(
            annotated_frame, key_points=key_points)
        return box_annotator.annotate(
            annotated_frame, detections=detections)

    sv.process_video(
        source_path="skiing.mp4",
        target_path="result.mp4",
        callback=callback
    )
    ```

<video controls>
    <source src="https://media.roboflow.com/supervision/video-examples/how-to/track-objects/track-keypoints-converted-to-detections.mp4" type="video/mp4">
</video>

### 关键点跟踪

现在我们有了一个 `Detections` 对象，我们可以在视频中跟踪它。利用 Supervision 的 [`sv.ByteTrack`](/latest/trackers/#supervision.tracker.byte_tracker.core.ByteTrack) 功能，为每个检测到的对象分配一个唯一的跟踪 ID，从而能够持续跟踪对象在不同帧中的运动路径。我们将使用 `TraceAnnotator` 来可视化结果。

=== "Ultralytics"

    ```{ .py hl_lines="10-11 17 25-26" }
    import numpy as np
    import supervision as sv
    from ultralytics import YOLO

    model = YOLO("yolov8m-pose.pt")
    edge_annotator = sv.EdgeAnnotator()
    vertex_annotator = sv.VertexAnnotator()
    box_annotator = sv.BoxAnnotator()

    tracker = sv.ByteTrack()
    trace_annotator = sv.TraceAnnotator()

    def callback(frame: np.ndarray, _: int) -> np.ndarray:
        results = model(frame)[0]
        key_points = sv.KeyPoints.from_ultralytics(results)
        detections = key_points.as_detections()
        detections = tracker.update_with_detections(detections)

        annotated_frame = edge_annotator.annotate(
            frame.copy(), key_points=key_points)
        annotated_frame = vertex_annotator.annotate(
            annotated_frame, key_points=key_points)
        annotated_frame = box_annotator.annotate(
            annotated_frame, detections=detections)
        return trace_annotator.annotate(
            annotated_frame, detections=detections)

    sv.process_video(
        source_path="skiing.mp4",
        target_path="result.mp4",
        callback=callback
    )
    ```

=== "Inference"

    ```{ .py hl_lines="11-12 18 26-27" }
    import numpy as np
    import supervision as sv
    from inference.models.utils import get_roboflow_model

    model = get_roboflow_model(
        model_id="yolov8m-pose-640", api_key=<ROBOFLOW API KEY>)
    edge_annotator = sv.EdgeAnnotator()
    vertex_annotator = sv.VertexAnnotator()
    box_annotator = sv.BoxAnnotator()

    tracker = sv.ByteTrack()
    trace_annotator = sv.TraceAnnotator()

    def callback(frame: np.ndarray, _: int) -> np.ndarray:
        results = model.infer(frame)[0]
        key_points = sv.KeyPoints.from_inference(results)
        detections = key_points.as_detections()
        detections = tracker.update_with_detections(detections)

        annotated_frame = edge_annotator.annotate(
            frame.copy(), key_points=key_points)
        annotated_frame = vertex_annotator.annotate(
            annotated_frame, key_points=key_points)
        annotated_frame = box_annotator.annotate(
            annotated_frame, detections=detections)
        return trace_annotator.annotate(
            annotated_frame, detections=detections)

    sv.process_video(
        source_path="skiing.mp4",
        target_path="result.mp4",
        callback=callback
    )
    ```

<video controls>
    <source src="https://media.roboflow.com/supervision/video-examples/how-to/track-objects/track-keypoints-with-tracking.mp4" type="video/mp4">
</video>

### 奖励：平滑处理

至此，我们已成功跟踪了由关键点模型检测到的对象，可以就此打住了。然而，我们可以通过应用 [`DetectionsSmoother`](/latest/detection/tools/smoother/) 来进一步提高边界框的稳定性。此工具通过在帧之间平滑边界框坐标来帮助稳定边界框。它非常易于使用：

=== "Ultralytics"

    ```{ .py hl_lines="11 19" }
    import numpy as np
    import supervision as sv
    from ultralytics import YOLO

    model = YOLO("yolov8m-pose.pt")
    edge_annotator = sv.EdgeAnnotator()
    vertex_annotator = sv.VertexAnnotator()
    box_annotator = sv.BoxAnnotator()

    tracker = sv.ByteTrack()
    smoother = sv.DetectionsSmoother()
    trace_annotator = sv.TraceAnnotator()

    def callback(frame: np.ndarray, _: int) -> np.ndarray:
        results = model(frame)[0]
        key_points = sv.KeyPoints.from_ultralytics(results)
        detections = key_points.as_detections()
        detections = tracker.update_with_detections(detections)
        detections = smoother.update_with_detections(detections)

        annotated_frame = edge_annotator.annotate(
            frame.copy(), key_points=key_points)
        annotated_frame = vertex_annotator.annotate(
            annotated_frame, key_points=key_points)
        annotated_frame = box_annotator.annotate(
            annotated_frame, detections=detections)
        return trace_annotator.annotate(
            annotated_frame, detections=detections)

    sv.process_video(
        source_path="skiing.mp4",
        target_path="result.mp4",
        callback=callback
    )
    ```

=== "Inference"

    ```{ .py hl_lines="12 20" }
    import numpy as np
    import supervision as sv
    from inference.models.utils import get_roboflow_model

    model = get_roboflow_model(
        model_id="yolov8m-pose-640", api_key=<ROBOFLOW API KEY>)
    edge_annotator = sv.EdgeAnnotator()
    vertex_annotator = sv.VertexAnnotator()
    box_annotator = sv.BoxAnnotator()

    tracker = sv.ByteTrack()
    smoother = sv.DetectionsSmoother()
    trace_annotator = sv.TraceAnnotator()

    def callback(frame: np.ndarray, _: int) -> np.ndarray:
        results = model.infer(frame)[0]
        key_points = sv.KeyPoints.from_inference(results)
        detections = key_points.as_detections()
        detections = tracker.update_with_detections(detections)
        detections = smoother.update_with_detections(detections)

        annotated_frame = edge_annotator.annotate(
            frame.copy(), key_points=key_points)
        annotated_frame = vertex_annotator.annotate(
            annotated_frame, key_points=key_points)
        annotated_frame = box_annotator.annotate(
            annotated_frame, detections=detections)
        return trace_annotator.annotate(
            annotated_frame, detections=detections)

    sv.process_video(
        source_path="skiing.mp4",
        target_path="result.mp4",
        callback=callback
    )
    ```

<video controls>
    <source src="https://media.roboflow.com/supervision/video-examples/how-to/track-objects/track-keypoints-with-smoothing.mp4" type="video/mp4">
</video>

这个结构化的演练应该为您提供了使用 Supervision 的各种功能（包括对象跟踪和轨迹注释）有效注释视频的详细路径。