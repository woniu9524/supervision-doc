---
comments: true
---

# 保存检测结果

Supervision 提供了便捷的方式将检测结果保存为 .CSV 和 .JSON 文件，以便进行离线处理。本指南将演示如何使用
[Inference](https://github.com/roboflow/inference)、
[Ultralytics](https://github.com/ultralytics/ultralytics) 或
[Transformers](https://github.com/huggingface/transformers)
包执行视频推理，并使用 [`sv.CSVSink`](/latest/detection/tools/save_detections/#supervision.detection.tools.csv_sink.CSVSink) 和
[`sv.JSONSink`](/latest/detection/tools/save_detections/#supervision.detection.tools.csv_sink.JSONSink)
保存其结果。

## 执行检测

首先，您需要从目标检测或分割模型中获取预测结果。您可以在我们的
[如何检测和标注](/latest/how_to/detect_and_annotate.md)
指南中了解更多相关信息。

=== "Inference"
    ```python
    import supervision as sv
    from inference import get_model

    model = get_model(model_id="yolov8n-640")
    frames_generator = sv.get_video_frames_generator(<SOURCE_VIDEO_PATH>)

    for frame in frames_generator:

        results = model.infer(image)[0]
        detections = sv.Detections.from_inference(results)
    ```

=== "Ultralytics"
    ```python
    import supervision as sv
    from ultralytics import YOLO

    model = YOLO("yolov8n.pt")
    frames_generator = sv.get_video_frames_generator(<SOURCE_VIDEO_PATH>)

    for frame in frames_generator:

        results = model(frame)[0]
        detections = sv.Detections.from_ultralytics(results)
    ```

=== "Transformers"
    ```python
    import torch
    import supervision as sv
    from transformers import DetrImageProcessor, DetrForObjectDetection

    processor = DetrImageProcessor.from_pretrained("facebook/detr-resnet-50")
    model = DetrForObjectDetection.from_pretrained("facebook/detr-resnet-50")
    frames_generator = sv.get_video_frames_generator(<SOURCE_VIDEO_PATH>)

    for frame in frames_generator:

        frame = sv.cv2_to_pillow(frame)
        inputs = processor(images=frame, return_tensors="pt")

        with torch.no_grad():
            outputs = model(**inputs)

        width, height = frame.size
        target_size = torch.tensor([[height, width]])
        results = processor.post_process_object_detection(
            outputs=outputs, target_sizes=target_size)[0]
        detections = sv.Detections.from_transformers(results)
    ```

## 以 CSV 格式保存检测结果

要将检测结果保存到 `.CSV` 文件，请打开我们的
[`sv.CSVSink`](/latest/detection/tools/save_detections/#supervision.detection.tools.csv_sink.CSVSink)，
然后将推理产生的
[`sv.Detections`](/latest/detection/core/#supervision.detection.core.Detections)
对象传递给它。其字段将被解析并保存到磁盘。

=== "Inference"
    ```{ .py hl_lines="7 12" }
    import supervision as sv
    from inference import get_model

    model = get_model(model_id="yolov8n-640")
    frames_generator = sv.get_video_frames_generator(<SOURCE_VIDEO_PATH>)

    with sv.CSVSink(<TARGET_CSV_PATH>) as sink:
        for frame in frames_generator:

            results = model.infer(image)[0]
            detections = sv.Detections.from_inference(results)
            sink.append(detections, {})
    ```

=== "Ultralytics"
    ```{ .py hl_lines="7 12" }
    import supervision as sv
    from ultralytics import YOLO

    model = YOLO("yolov8n.pt")
    frames_generator = sv.get_video_frames_generator(<SOURCE_VIDEO_PATH>)

    with sv.CSVSink(<TARGET_CSV_PATH>) as sink:
        for frame in frames_generator:

            results = model(frame)[0]
            detections = sv.Detections.from_ultralytics(results)
            sink.append(detections, {})
    ```

=== "Transformers"
    ```{ .py hl_lines="9 23" }
    import torch
    import supervision as sv
    from transformers import DetrImageProcessor, DetrForObjectDetection

    processor = DetrImageProcessor.from_pretrained("facebook/detr-resnet-50")
    model = DetrForObjectDetection.from_pretrained("facebook/detr-resnet-50")
    frames_generator = sv.get_video_frames_generator(<SOURCE_VIDEO_PATH>)

    with sv.CSVSink(<TARGET_CSV_PATH>) as sink:
        for frame in frames_generator:

            frame = sv.cv2_to_pillow(frame)
            inputs = processor(images=frame, return_tensors="pt")

            with torch.no_grad():
                outputs = model(**inputs)

            width, height = frame.size
            target_size = torch.tensor([[height, width]])
            results = processor.post_process_object_detection(
                outputs=outputs, target_sizes=target_size)[0]
            detections = sv.Detections.from_transformers(results)
            sink.append(detections, {})
    ```

| x_min   | y_min   | x_max   | y_max   | class_id | confidence | tracker_id | class_name |
| ------- | ------- | ------- | ------- | -------- | ---------- | ---------- | ---------- |
| 2941.14 | 1269.31 | 3220.77 | 1500.67 | 2        | 0.8517     |            | car        |
| 944.889 | 899.641 | 1235.42 | 1308.80 | 7        | 0.6752     |            | truck      |
| 1439.78 | 1077.79 | 1621.27 | 1231.40 | 2        | 0.6450     |            | car        |

## 自定义字段

除了 [`sv.Detections`](/latest/detection/core/#supervision.detection.core.Detections) 中的常规字段外，
[`sv.CSVSink`](/latest/detection/tools/save_detections/#supervision.detection.tools.csv_sink.CSVSink)
还允许您向每行添加自定义信息，这些信息可以通过 `custom_data` 字典传递。让我们利用此功能来保存检测来源的帧索引信息。

=== "Inference"
    ```{ .py hl_lines="8 12" }
    import supervision as sv
    from inference import get_model

    model = get_model(model_id="yolov8n-640")
    frames_generator = sv.get_video_frames_generator(<SOURCE_VIDEO_PATH>)

    with sv.CSVSink(<TARGET_CSV_PATH>) as sink:
        for frame_index, frame in enumerate(frames_generator):

            results = model.infer(image)[0]
            detections = sv.Detections.from_inference(results)
            sink.append(detections, {"frame_index": frame_index})
    ```

=== "Ultralytics"
    ```{ .py hl_lines="8 12" }
    import supervision as sv
    from ultralytics import YOLO

    model = YOLO("yolov8n.pt")
    frames_generator = sv.get_video_frames_generator(<SOURCE_VIDEO_PATH>)

    with sv.CSVSink(<TARGET_CSV_PATH>) as sink:
        for frame_index, frame in enumerate(frames_generator):

            results = model(frame)[0]
            detections = sv.Detections.from_ultralytics(results)
            sink.append(detections, {"frame_index": frame_index})
    ```

=== "Transformers"
    ```{ .py hl_lines="10 23" }
    import torch
    import supervision as sv
    from transformers import DetrImageProcessor, DetrForObjectDetection

    processor = DetrImageProcessor.from_pretrained("facebook/detr-resnet-50")
    model = DetrForObjectDetection.from_pretrained("facebook/detr-resnet-50")
    frames_generator = sv.get_video_frames_generator(<SOURCE_VIDEO_PATH>)

    with sv.CSVSink(<TARGET_CSV_PATH>) as sink:
        for frame_index, frame in enumerate(frames_generator):

            frame = sv.cv2_to_pillow(frame)
            inputs = processor(images=frame, return_tensors="pt")

            with torch.no_grad():
                outputs = model(**inputs)

            width, height = frame.size
            target_size = torch.tensor([[height, width]])
            results = processor.post_process_object_detection(
                outputs=outputs, target_sizes=target_size)[0]
            detections = sv.Detections.from_transformers(results)
            sink.append(detections, {"frame_index": frame_index})
    ```

| x_min   | y_min   | x_max   | y_max   | class_id | confidence | tracker_id | class_name | frame_index |
| ------- | ------- | ------- | ------- | -------- | ---------- | ---------- | ---------- | ----------- |
| 2941.14 | 1269.31 | 3220.77 | 1500.67 | 2        | 0.8517     |            | car        | 0           |
| 944.889 | 899.641 | 1235.42 | 1308.80 | 7        | 0.6752     |            | truck      | 0           |
| 1439.78 | 1077.79 | 1621.27 | 1231.40 | 2        | 0.6450     |            | car        | 0           |

## 以 JSON 格式保存检测结果

如果您倾向于将结果保存为 `.JSON` 文件而不是 `.CSV` 文件，您只需将
[`sv.CSVSink`](/latest/detection/tools/save_detections/#supervision.detection.tools.csv_sink.CSVSink)
替换为
[`sv.JSONSink`](/latest/detection/tools/save_detections/#supervision.detection.tools.csv_sink.JSONSink)。

=== "Inference"
    ```{ .py hl_lines="7" }
    import supervision as sv
    from inference import get_model

    model = get_model(model_id="yolov8n-640")
    frames_generator = sv.get_video_frames_generator(<SOURCE_VIDEO_PATH>)

    with sv.JSONSink(<TARGET_CSV_PATH>) as sink:
        for frame_index, frame in enumerate(frames_generator):

            results = model.infer(image)[0]
            detections = sv.Detections.from_inference(results)
            sink.append(detections, {"frame_index": frame_index})
    ```

=== "Ultralytics"
    ```{ .py hl_lines="7" }
    import supervision as sv
    from ultralytics import YOLO

    model = YOLO("yolov8n.pt")
    frames_generator = sv.get_video_frames_generator(<SOURCE_VIDEO_PATH>)

    with sv.JSONSink(<TARGET_CSV_PATH>) as sink:
        for frame_index, frame in enumerate(frames_generator):

            results = model(frame)[0]
            detections = sv.Detections.from_ultralytics(results)
            sink.append(detections, {"frame_index": frame_index})
    ```

=== "Transformers"
    ```{ .py hl_lines="9" }
    import torch
    import supervision as sv
    from transformers import DetrImageProcessor, DetrForObjectDetection

    processor = DetrImageProcessor.from_pretrained("facebook/detr-resnet-50")
    model = DetrForObjectDetection.from_pretrained("facebook/detr-resnet-50")
    frames_generator = sv.get_video_frames_generator(<SOURCE_VIDEO_PATH>)

    with sv.JSONSink(<TARGET_CSV_PATH>) as sink:
        for frame_index, frame in enumerate(frames_generator):

            frame = sv.cv2_to_pillow(frame)
            inputs = processor(images=frame, return_tensors="pt")

            with torch.no_grad():
                outputs = model(**inputs)

            width, height = frame.size
            target_size = torch.tensor([[height, width]])
            results = processor.post_process_object_detection(
                outputs=outputs, target_sizes=target_size)[0]
            detections = sv.Detections.from_transformers(results)
            sink.append(detections, {"frame_index": frame_index})
    ```