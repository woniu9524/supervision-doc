# Changelog

### 0.26.0 <small>Jul 16, 2025</small>

!!! failure "Removed"
    `supervision-0.26.0` 放弃了对 `python3.8` 的支持，并将所有代码升级为 `python3.9` 语法风格。

!!! info "提示"
    Supervision 的文档主题现已焕然一新，符合所有 Roboflow 开源项目的文档风格。([#1858](https://github.com/roboflow/supervision/pull/1858))

- Added [#1774](https://github.com/roboflow/supervision/pull/1774): 支持 IOS (Intersection over Smallest) 重叠度量，该度量用于衡量在 [`sv.Detections.with_nms`](https://supervision.roboflow.com/0.26.0/detection/core/#supervision.detection.core.Detections.with_nms), [`sv.Detections.with_nmm`](https://supervision.roboflow.com/0.26.0/detection/core/#supervision.detection.core.Detections.with_nmm), [`sv.box_iou_batch`](https://supervision.roboflow.com/0.26.0/detection/utils/iou_and_nms/#supervision.detection.utils.iou_and_nms.box_iou_batch) 和 [`sv.mask_iou_batch`](https://supervision.roboflow.com/0.26.0/detection/utils/iou_and_nms/#supervision.detection.utils.iou_and_nms.mask_iou_batch) 中，较大对象覆盖了多少较小对象。

    ```python
    import numpy as np
    import supervision as sv

    boxes_true = np.array([
        [100, 100, 200, 200],
        [300, 300, 400, 400]
    ])
    boxes_detection = np.array([
        [150, 150, 250, 250],
        [320, 320, 420, 420]
    ])

    sv.box_iou_batch(
        boxes_true=boxes_true,
        boxes_detection=boxes_detection,
        overlap_metric=sv.OverlapMetric.IOU
    )

    # array([[0.14285714, 0.        ],
    #        [0.        , 0.47058824]])

    sv.box_iou_batch(
        boxes_true=boxes_true,
        boxes_detection=boxes_detection,
        overlap_metric=sv.OverlapMetric.IOS
    )

    # array([[0.25, 0.  ],
    #        [0.  , 0.64]])
    ```

- Added [#1874](https://github.com/roboflow/supervision/pull/1874): [`sv.box_iou`](https://supervision.roboflow.com/0.26.0/detection/utils/iou_and_nms/#supervision.detection.utils.iou_and_nms.box_iou)，可高效计算两个独立边界框之间的 IoU (Intersection over Union)。

- Added [#1816](https://github.com/roboflow/supervision/pull/1816): [`sv.process_video`](https://supervision.roboflow.com/0.26.0/utils/video/#supervision.utils.video.process_video) 支持帧限制和进度条。

- Added [#1788](https://github.com/roboflow/supervision/pull/1788): 通过 [`sv.KeyPoints.from_transformers`](https://supervision.roboflow.com/0.26.0/keypoint/core/#supervision.keypoint.core.KeyPoints.from_transformers) 支持从 [ViTPose](https://huggingface.co/docs/transformers/en/model_doc/vitpose) 和 [ViTPose++](https://huggingface.co/docs/transformers/en/model_doc/vitpose#vitpose-models) 推理结果创建 [`sv.KeyPoints`](https://supervision.roboflow.com/0.26.0/keypoint/core/#supervision.keypoint.core.KeyPoints) 对象。

- Added [#1823](https://github.com/roboflow/supervision/pull/1823): [`sv.xyxy_to_xcycarh`](https://supervision.roboflow.com/0.26.0/detection/utils/converters/#supervision.detection.utils.converters.xyxy_to_xcycarh) 函数，用于将边界框坐标从 `(x_min, y_min, x_max, y_max)` 转换为测量空间格式 `(center x, center y, aspect ratio, height)`，其中宽高比为 `width / height`。

- Added [#1788](https://github.com/roboflow/supervision/pull/1788): [`sv.xyxy_to_xywh`](https://supervision.roboflow.com/0.26.0/detection/utils/converters/#supervision.detection.utils.converters.xyxy_to_xywh) 函数，用于将边界框坐标从 `(x_min, y_min, x_max, y_max)` 格式转换为 `(x, y, width, height)` 格式。

- Changed [#1820](https://github.com/roboflow/supervision/pull/1820): [`sv.LabelAnnotator`](https://supervision.roboflow.com/0.26.0/detection/annotators/#supervision.annotators.core.LabelAnnotator) 现在支持 `smart_position` 参数，可以自动将标签保持在帧边界内，以及 `max_line_length` 参数来控制长标签或多行标签的文本换行。

- Changed [#1825](https://github.com/roboflow/supervision/pull/1825): [`sv.LabelAnnotator`](https://supervision.roboflow.com/0.26.0/detection/annotators/#supervision.annotators.core.LabelAnnotator) 现在支持非字符串标签。

- Changed [#1792](https://github.com/roboflow/supervision/pull/1792): [`sv.Detections.from_vlm`](https://supervision.roboflow.com/0.26.0/detection/core/#supervision.detection.core.Detections.from_vlm) 现在支持解析 [Google Gemini 模型](https://ai.google.dev/gemini-api/docs/vision) 生成的响应中的边界框和分割掩码。

    ```python
    import supervision as sv

    gemini_response_text = """```json
        [
            {"box_2d": [543, 40, 728, 200], "label": "cat", "id": 1},
            {"box_2d": [653, 352, 820, 522], "label": "dog", "id": 2}
        ]
    ```"""

    detections = sv.Detections.from_vlm(
        sv.VLM.GOOGLE_GEMINI_2_5,
        gemini_response_text,
        resolution_wh=(1000, 1000),
        classes=['cat', 'dog'],
    )

    detections.xyxy
    # array([[543., 40., 728., 200.], [653., 352., 820., 522.]])

    detections.data
    # {'class_name': array(['cat', 'dog'], dtype='<U26')}

    detections.class_id
    # array([0, 1])
    ```

- Changed [#1878](https://github.com/roboflow/supervision/pull/1878): [`sv.Detections.from_vlm`](https://supervision.roboflow.com/0.26.0/detection/core/#supervision.detection.core.Detections.from_vlm) 现在支持解析 [Moondream](https://github.com/vikhyat/moondream) 生成的响应中的边界框。

    ```python
    import supervision as sv

    moondream_result = {
        'objects': [
            {
                'x_min': 0.5704046934843063,
                'y_min': 0.20069346576929092,
                'x_max': 0.7049859315156937,
                'y_max': 0.3012596592307091
            },
            {
                'x_min': 0.6210969910025597,
                'y_min': 0.3300672620534897,
                'x_max': 0.8417936339974403,
                'y_max': 0.4961046129465103
            }
        ]
    }

    detections = sv.Detections.from_vlm(
        sv.VLM.MOONDREAM,
        moondream_result,
        resolution_wh=(1000, 1000),
    )

    detections.xyxy
    # array([[1752.28,  818.82, 2165.72, 1229.14],
    #        [1908.01, 1346.67, 2585.99, 2024.11]])
    ```

- Changed [#1709](https://github.com/roboflow/supervision/pull/1790): [`sv.Detections.from_vlm`](https://supervision.roboflow.com/0.26.0/detection/core/#supervision.detection.core.Detections.from_vlm) 现在支持解析 [Qwen-2.5 VL](https://github.com/QwenLM/Qwen2.5-VL) 生成的响应中的边界框。

    ```python
    import supervision as sv

    qwen_2_5_vl_result = """```json
    [
        {"bbox_2d": [139, 768, 315, 954], "label": "cat"},
        {"bbox_2d": [366, 679, 536, 849], "label": "dog"}
    ]
    ```"""

    detections = sv.Detections.from_vlm(
        sv.VLM.QWEN_2_5_VL,
        qwen_2_5_vl_result,
        input_wh=(1000, 1000),
        resolution_wh=(1000, 1000),
        classes=['cat', 'dog'],
    )

    detections.xyxy
    # array([[139., 768., 315., 954.], [366., 679., 536., 849.]])

    detections.class_id
    # array([0, 1])

    detections.data
    # {'class_name': array(['cat', 'dog'], dtype='<U10')}

    detections.class_id
    # array([0, 1])
    ```

- Changed [#1786](https://github.com/roboflow/supervision/pull/1786): 显著提高了 [`sv.HeatMapAnnotator`](https://supervision.roboflow.com/0.26.0/detection/annotators/#supervision.annotators.core.HeatMapAnnotator) 中 HSV 颜色映射的速度，在 1920x1080 帧上实现了约 28 倍的性能提升。

- Fix [#1834](https://github.com/roboflow/supervision/pull/1834): Supervision 的 [`sv.MeanAveragePrecision`](https://supervision.roboflow.com/0.26.0/metrics/mean_average_precision/#supervision.metrics.mean_average_precision.MeanAveragePrecision)现已与官方 COCO 评估工具 [pycocotools](https://github.com/ppwwyyxx/cocoapi) 完全对齐，确保了准确性和标准化指标。此更新使我们能够启动新版本的 [Computer Vision Model Leaderboard](https://leaderboard.roboflow.com/)。

    ```python
    import supervision as sv
    from supervision.metrics import MeanAveragePrecision

    predictions = sv.Detections(...)
    targets = sv.Detections(...)

    map_metric = MeanAveragePrecision()
    map_metric.update(predictions, targets).compute()

    # Average Precision (AP) @[ IoU=0.50:0.95 | area=   all | maxDets=100 ] = 0.464
    # Average Precision (AP) @[ IoU=0.50      | area=   all | maxDets=100 ] = 0.637
    # Average Precision (AP) @[ IoU=0.75      | area=   all | maxDets=100 ] = 0.203
    # Average Precision (AP) @[ IoU=0.50:0.95 | area= small | maxDets=100 ] = 0.284
    # Average Precision (AP) @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] = 0.497
    # Average Precision (AP) @[ IoU=0.50:0.95 | area= large | maxDets=100 ] = 0.629
    ```

- Fix [#1767](https://github.com/roboflow/supervision/pull/1767): 修复了在过滤検出时丢失 `sv.Detections.data` 的问题。

### 0.25.0 <small>Nov 12, 2024</small>

- 本次发布没有移除或弃用任何内容！

- 对 [`LineZone`](https://supervision.roboflow.com/0.25.0/detection/tools/line_zone/) 的重要更新：在计算线交叉时，可能抖动的检测会重复计算（或多次计算）。现在可以通过 `minimum_crossing_threshold` 参数解决此问题。如果将其设置为 2 或更高，将使用额外的帧来确认交叉，从而显著提高准确性。([#1540](https://github.com/roboflow/supervision/pull/1540))

- 现在可以跟踪被检测为 [`KeyPoints`](https://supervision.roboflow.com/0.25.0/keypoint/core/#supervision.keypoint.core.KeyPoints) 的对象。请参阅 [Object Tracking Guide](https://supervision.roboflow.com/latest/how_to/track_objects/#keypoints) 中的完整分步指南。([#1658](https://github.com/roboflow/supervision/pull/1658))

```python
import numpy as np
import supervision as sv
from ultralytics import YOLO

model = YOLO("yolov8m-pose.pt")
tracker = sv.ByteTrack()
trace_annotator = sv.TraceAnnotator()

def callback(frame: np.ndarray, _: int) -> np.ndarray:
    results = model(frame)[0]
    key_points = sv.KeyPoints.from_ultralytics(results)

    detections = key_points.as_detections()
    detections = tracker.update_with_detections(detections)

    annotated_image = trace_annotator.annotate(frame.copy(), detections)
    return annotated_image

sv.process_video(
    source_path="input_video.mp4",
    target_path="output_video.mp4",
    callback=callback
)
```

- 向 [`KeyPoints`](https://supervision.roboflow.com/0.25.0/keypoint/core/#supervision.keypoint.core.KeyPoints) 添加了 `is_empty` 方法，用于检查对象中是否包含任何关键点。([#1658](https://github.com/roboflow/supervision/pull/1658))

- 向 [`KeyPoints`](https://supervision.roboflow.com/0.25.0/keypoint/core/#supervision.keypoint.core.KeyPoints) 添加了 `as_detections` 方法，该方法将 `KeyPoints` 转换为 `Detections`。([#1658](https://github.com/roboflow/supervision/pull/1658))

- 向 `supervision[assets]` 添加了一个新视频。([#1657](https://github.com/roboflow/supervision/pull/1657))

```python
from supervision.assets import download_assets, VideoAssets

path_to_video = download_assets(VideoAssets.SKIING)
```

- Supervision 现在可以与 [`Python 3.13`](https://docs.python.org/3/whatsnew/3.13.html) 一起使用。最值得称道的更新是能够 [在没有全局解释器锁 (GIL) 的情况下](https://docs.python.org/3/whatsnew/3.13.html#whatsnew313-free-threaded-cpython) 运行 Python。我们预计依赖项对它的支持将不一致，但如果您尝试了——请告诉我们结果！([#1595](https://github.com/roboflow/supervision/pull/1595))

- 添加了 [`Mean Average Recall`](https://supervision.roboflow.com/latest/metrics/mean_average_recall/) mAR 指标，该指标返回一个召回率分数，平均了 IoU 阈值、检测目标类别以及对最多考虑检测次数的限制。([#1661](https://github.com/roboflow/supervision/pull/1661))

```python
import supervision as sv
from supervision.metrics import MeanAverageRecall

predictions = sv.Detections(...)
targets = sv.Detections(...)

map_metric = MeanAverageRecall()
map_result = map_metric.update(predictions, targets).compute()

map_result.plot()
```

- 添加了 [`Precision`](https://supervision.roboflow.com/latest/metrics/precision/) 和 [`Recall`](https://supervision.roboflow.com/latest/metrics/recall/) 指标，为比较模型输出与地面真实值或另一个模型提供了基准 ([#1609](https://github.com/roboflow/supervision/pull/1609))

```python
import supervision as sv
from supervision.metrics import Recall

predictions = sv.Detections(...)
targets = sv.Detections(...)

recall_metric = Recall()
recall_result = recall_metric.update(predictions, targets).compute()

recall_result.plot()
```

- 所有 Metrics 现在都支持定向边界框 (OBB) ([#1593](https://github.com/roboflow/supervision/pull/1593))

```python
import supervision as sv
from supervision.metrics import F1_Score

predictions = sv.Detections(...)
targets = sv.Detections(...)

f1_metric = MeanAverageRecall(metric_target=sv.MetricTarget.ORIENTED_BOUNDING_BOXES)
f1_result = f1_metric.update(predictions, targets).compute()
```

- 引入智能标签！当为 [`LabelAnnotator`](https://supervision.roboflow.com/0.25.0/detection/annotators/#supervision.annotators.core.LabelAnnotator), [`RichLabelAnnotator`](https://supervision.roboflow.com/0.25.0/detection/annotators/#supervision.annotators.core.RichLabelAnnotator) 或 [`VertexLabelAnnotator`](https://supervision.roboflow.com/0.25.0/detection/annotators/#supervision.annotators.core.RichLabelAnnotator) 设置 `smart_position` 时，标签会移动以避免与其他标签重叠。([#1625](https://github.com/roboflow/supervision/pull/1625))

```python
import supervision as sv
from ultralytics import YOLO

image = cv2.imread("image.jpg")

label_annotator = sv.LabelAnnotator(smart_position=True)

model = YOLO("yolo11m.pt")
results = model(image)[0]
detections = sv.Detections.from_ultralytics(results)

annotated_frame = label_annotator.annotate(first_frame.copy(), detections)
sv.plot_image(annotated_frame)
```

- 向 [`Detections`](https://supervision.roboflow.com/0.25.0/detection/core/#supervision.detection.core.Detections) 添加了 `metadata` 变量。它允许您为每个图像存储自定义数据，而不是像使用 `data` 变量那样为每个检测对象存储。例如，`metadata` 可用于存储源视频路径、摄像机型号或摄像机参数。([#1589](https://github.com/roboflow/supervision/pull/1589))

```python
import supervision as sv
from ultralytics import YOLO

model = YOLO("yolov8m")

result = model("image.png")[0]
detections = sv.Detections.from_ultralytics(result)

# `data` 中的项必须与检测的长度匹配
object_ids = [num for num in range(len(detections))]
detections.data["object_number"] = object_ids

# `metadata` 中的项可以为任何长度。
detections.metadata["camera_model"] = "Luxonis OAK-D"
```

- 添加了 `py.typed` 类型提示元文件。它应该为类型注解器和 IDE 提供更强的信号，表明类型支持可用。([#1586](https://github.com/roboflow/supervision/pull/1586))

- `ByteTrack` 不再需要 `detections` 具有 `class_id` ([#1637](https://github.com/roboflow/supervision/pull/1637))
- `draw_line`, `draw_rectangle`, `draw_filled_rectangle`, `draw_polygon`, `draw_filled_polygon` 和 `PolygonZoneAnnotator` 现在带有默认颜色 ([#1591](https://github.com/roboflow/supervision/pull/1591))
- 在合并多个数据集时，将数据集类视为区分大小写。 ([#1643](https://github.com/roboflow/supervision/pull/1643))
- 扩展了 [Metrics 文档](https://supervision.roboflow.com/0.25.0/metrics/f1_score/)，包含示例图表和打印结果 ([#1660](https://github.com/roboflow/supervision/pull/1660))
- 为多边形区域添加了用法示例 ([#1608](https://github.com/roboflow/supervision/pull/1608))
- 对多边形错误处理进行了小型改进：([#1602](https://github.com/roboflow/supervision/pull/1602))

- 更新了 [`ByteTrack`](https://supervision.roboflow.com/0.25.0/trackers/#supervision.tracker.byte_tracker.core.ByteTrack)，移除了共享变量。以前，多个 `ByteTrack` 实例会共享一些数据，需要大量使用 `tracker.reset()`。([#1603](https://github.com/roboflow/supervision/pull/1603)), ([#1528](https://github.com/roboflow/supervision/pull/1528))
- 修复了 `MeanAveragePrecision` 中 `class_agnostic` 设置无效的错误。([#1577](https://github.com/roboflow/supervision/pull/1577)) hacktoberfest
- 从我们的 CI 系统中移除了欢迎工作流。([#1596](https://github.com/roboflow/supervision/pull/1596))

- 对 `ByteTrack` 进行了大量重构：将 STrack 移至单独的类，移除了多余的 `BaseTrack` 类，移除了未使用的变量 ([#1603](https://github.com/roboflow/supervision/pull/1603))
- 对 `RichLabelAnnotator` 进行了大量重构，将其内容与 `LabelAnnotator` 匹配。([#1625](https://github.com/roboflow/supervision/pull/1625))

### 0.24.0 <small>Oct 4, 2024</small>

- 为检测和分割添加了 [F1 score](https://supervision.roboflow.com/0.24.0/metrics/f1_score/#supervision.metrics.f1_score.F1Score) 作为新指标。 [#1521](https://github.com/roboflow/supervision/pull/1521)

```python
import supervision as sv
from supervision.metrics import F1Score

predictions = sv.Detections(...)
targets = sv.Detections(...)

f1_metric = F1Score()
f1_result = f1_metric.update(predictions, targets).compute()

print(f1_result)
print(f1_result.f1_50)
print(f1_result.small_objects.f1_50)
```

- 添加了新的 cookbook：[Small Object Detection with SAHI](https://supervision.roboflow.com/0.24.0/notebooks/small-object-detection-with-sahi/)。此 cookbook 提供了使用 [`InferenceSlicer`](https://supervision.roboflow.com/0.24.0/detection/tools/inference_slicer/) 进行小目标检测的详细指南。 [#1483](https://github.com/roboflow/supervision/pull/1483)

- 添加了 [Embedded Workflow](https://roboflow.com/workflows)，允许您 [预览 Annotator](https://supervision.roboflow.com/0.24.0/detection/annotators/)。 [#1533](https://github.com/roboflow/supervision/pull/1533)

- 增强了 [`LineZoneAnnotator`](https://supervision.roboflow.com/0.24.0/detection/tools/line_zone/#supervision.detection.line_zone.LineZoneAnnotator)，允许标签与线条对齐，即使线条不是水平的。此外，您还可以禁用文本背景，并选择将标签绘制在中心之外，从而最大限度地减少多个 [`LineZone`](https://supervision.roboflow.com/0.24.0/detection/tools/line_zone/#supervision.detection.line_zone.LineZone) 标签的重叠。 [#854](https://github.com/roboflow/supervision/pull/854)

```python
import supervision as sv
import cv2

image = cv2.imread("<SOURCE_IMAGE_PATH>")

line_zone = sv.LineZone(
    start=sv.Point(0, 100),
    end=sv.Point(50, 200)
)
line_zone_annotator = sv.LineZoneAnnotator(
    text_orient_to_line=True,
    display_text_box=False,
    text_centered=False
)

annotated_frame = line_zone_annotator.annotate(
    frame=image.copy(), line_counter=line_zone
)

sv.plot_image(annotated_frame)
```

- 为 [`LineZone`](https://supervision.roboflow.com/0.24.0/detection/tools/line_zone/#supervision.detection.line_zone.LineZone) 添加了每类计数功能，并为 [`LineZoneAnnotatorMulticlass`](https://supervision.roboflow.com/0.24.0/detection/tools/line_zone/#supervision.detection.line_zone.LineZoneAnnotatorMulticlass) 引入了用于可视化每类计数的Annotator。此功能允许跟踪穿过线条的单个类别，增强了交通监控或人群分析等用例的灵活性。 [#1555](https://github.com/roboflow/supervision/pull/1555)

```python
import supervision as sv
import cv2

image = cv2.imread("<SOURCE_IMAGE_PATH>")

line_zone = sv.LineZone(
    start=sv.Point(0, 100),
    end=sv.Point(50, 200)
)
line_zone_annotator = sv.LineZoneAnnotatorMulticlass()

frame = line_zone_annotator.annotate(
    frame=frame, line_zones=[line_zone]
)

sv.plot_image(frame)
```

- 添加了 [`from_easyocr`](https://supervision.roboflow.com/0.24.0/detection/core/#supervision.detection.core.Detections.from_easyocr)，允许将 OCR 结果集成到 supervision 框架中。[EasyOCR](https://github.com/JaidedAI/EasyOCR) 是一个开源光学字符识别 (OCR) 库，可以从图像中读取文本。 [#1515](https://github.com/roboflow/supervision/pull/1515)

```python
import supervision as sv
import easyocr
import cv2

image = cv2.imread("<SOURCE_IMAGE_PATH>")

reader = easyocr.Reader(["en"])
result = reader.readtext("<SOURCE_IMAGE_PATH>", paragraph=True)
detections = sv.Detections.from_easyocr(result)

box_annotator = sv.BoxAnnotator(color_lookup=sv.ColorLookup.INDEX)
label_annotator = sv.LabelAnnotator(color_lookup=sv.ColorLookup.INDEX)

annotated_image = image.copy()
annotated_image = box_annotator.annotate(scene=annotated_image, detections=detections)
annotated_image = label_annotator.annotate(scene=annotated_image, detections=detections)

sv.plot_image(annotated_image)
```

- 添加了 [`oriented_box_iou_batch`](https://supervision.roboflow.com/0.24.0/detection/utils/#supervision.detection.utils.oriented_box_iou_batch) 函数到 `detection.utils`。此函数计算定向或旋转边界框 (OBB) 的 IoU。 [#1502](https://github.com/roboflow/supervision/pull/1502)

```python
import numpy as np

boxes_true = np.array([[[1, 0], [0, 1], [3, 4], [4, 3]]])
boxes_detection = np.array([[[1, 1], [2, 0], [4, 2], [3, 3]]])
ious = sv.oriented_box_iou_batch(boxes_true, boxes_detection)
print("IoU between true and detected boxes:", ious)
```

- 扩展了 [`PolygonZoneAnnotator`](https://supervision.roboflow.com/0.24.0/detection/tools/polygon_zone/#supervision.detection.tools.polygon_zone.PolygonZoneAnnotator) 以允许在绘制区域时设置不透明度，通过可调透明度填充区域来增强可视化。 [#1527](https://github.com/roboflow/supervision/pull/1527)

```python
import cv2
from ncnn.model_zoo import get_model
import supervision as sv

image = cv2.imread("<SOURCE_IMAGE_PATH>")
model = get_model(
    "yolov8s",
    target_size=640,
    prob_threshold=0.5,
    nms_threshold=0.45,
    num_threads=4,
    use_gpu=True,
)
result = model(image)
detections = sv.Detections.from_ncnn(result)
```

!!! failure "Removed"

    已移除 [`PolygonZone`](https://supervision.roboflow.com/0.24.0/detection/tools/polygon_zone/#supervision.detection.tools.polygon_zone.PolygonZone) 中的 `frame_resolution_wh` 参数。

!!! failure "Removed"

    移除了 Supervision 安装方法 `"headless"` 和 `"desktop"`，因为它们不再需要。`pip install supervision[headless]` 将安装基础库并无害地警告不存在的附加组件。

- Supervision 现在依赖 `opencv-python` 而不是 `opencv-python-headless`。 [#1530](https://github.com/roboflow/supervision/pull/1530)

- 修正了 COCO 101 点平均精度算法，以正确插值精度，从而无需平均掉中间值即可更精确地计算平均精度。 [#1500](https://github.com/roboflow/supervision/pull/1500)

- 解决了构建文档时突出显示的其他问题。主要包括空格调整和类型不一致。更新了文档以提高清晰度并修复了格式问题。为 `mkdocstrings-python` 添加了显式版本。 [#1549](https://github.com/roboflow/supervision/pull/1549)

- 为代码格式化启用了 Ruff 规则并进行了修复，包括避免不必要的迭代器分配和为默认可变参数使用 Optional 等更改。 [#1526](https://github.com/roboflow/supervision/pull/1526)

### 0.23.0 <small>Aug 28, 2024</small>

- Added [#930](https://github.com/roboflow/supervision/pull/930): `IconAnnotator`，一个 [新 Annotator](https://supervision.roboflow.com/0.23.0/detection/annotators/#supervision.annotators.core.IconAnnotator)，允许在每个检测上绘制图标。如果你想为每个类别绘制特定的图标，这个功能会很有用。

```python
import supervision as sv
from inference import get_model

image = <SOURCE_IMAGE_PATH>
icon_dog = <DOG_PNG_PATH>
icon_cat = <CAT_PNG_PATH>

model = get_model(model_id="yolov8n-640")
results = model.infer(image)[0]
detections = sv.Detections.from_inference(results)

icon_paths = []
for class_name in detections.data["class_name"]:
    if class_name == "dog":
        icon_paths.append(icon_dog)
    elif class_name == "cat":
        icon_paths.append(icon_cat)
    else:
        icon_paths.append("")

icon_annotator = sv.IconAnnotator()
annotated_frame = icon_annotator.annotate(
    scene=image.copy(),
    detections=detections,
    icon_path=icon_paths
)
```

- Added [#1385](https://github.com/roboflow/supervision/pull/1385): [`BackgroundColorAnnotator`](https://supervision.roboflow.com/0.23.0/detection/annotators/#supervision.annotators.core.BackgroundColorAnnotator)，它在检测的背景图像上绘制一个叠加层。

```python
import supervision as sv
from inference import get_model

image = <SOURCE_IMAGE_PATH>

model = get_model(model_id="yolov8n-640")
results = model.infer(image)[0]
detections = sv.Detections.from_inference(results)

background_overlay_annotator = sv.BackgroundOverlayAnnotator()
annotated_frame = background_overlay_annotator.annotate(
    scene=image.copy(),
    detections=detections
)
```

- Added [#1386](https://github.com/roboflow/supervision/pull/1386): 支持 [`sv.Detections.from_transformers`](https://supervision.roboflow.com/0.23.0/detection/core/#supervision.detection.core.Detections.from_transformers) 中的 Transformers v5 函数。这包括 `DetrImageProcessor` 的方法 `post_process_object_detection`、`post_process_panoptic_segmentation`、`post_process_semantic_segmentation` 和 `post_process_instance_segmentation`。

```python
import torch
import supervision as sv
from PIL import Image
from transformers import DetrImageProcessor, DetrForObjectDetection

processor = DetrImageProcessor.from_pretrained("facebook/detr-resnet-50")
model = DetrForObjectDetection.from_pretrained("facebook/detr-resnet-50")

image = Image.open(<SOURCE_IMAGE_PATH>)
inputs = processor(images=image, return_tensors="pt")

with torch.no_grad():
    outputs = model(**inputs)

width, height = image.size
target_size = torch.tensor([[height, width]])
results = processor.post_process_object_detection(
    outputs=outputs, target_sizes=target_size)[0]
detections = sv.Detections.from_transformers(
    transformers_results=results,
    id2label=model.config.id2label)
```

- Added [#1354](https://github.com/roboflow/supervision/pull/1354): Ultralytics SAM (Segment Anything Model) 支持 [`sv.Detections.from_ultralytics`](https://supervision.roboflow.com/0.23.0/detection/core/#supervision.detection.core.Detections.from_ultralytics)。[SAM2](https://sam2.metademolab.com/) 在此更新期间发布，并且已通过 [`sv.Detections.from_sam`](https://supervision.roboflow.com/0.23.0/detection/core/#supervision.detection.core.Detections.from_sam) 支持。

```python
import supervision as sv
from segment_anything import (
    sam_model_registry,
    SamAutomaticMaskGenerator
)
sam_model_reg = sam_model_registry[MODEL_TYPE]
sam = sam_model_reg(checkpoint=CHECKPOINT_PATH).to(device=DEVICE)
mask_generator = SamAutomaticMaskGenerator(sam)
sam_result = mask_generator.generate(IMAGE)
detections = sv.Detections.from_sam(sam_result=sam_result)
```

- Added [#1458](https://github.com/roboflow/supervision/pull/1458): 为 [`TriangleAnnotator`](https://supervision.roboflow.com/0.23.0/detection/annotators/#supervision.annotators.core.TriangleAnnotator) 和 [`DotAnnotator`](https://supervision.roboflow.com/0.23.0/detection/annotators/#supervision.annotators.core.DotAnnotator) 添加了 `outline_color` 选项。

- Added [#1409](https://github.com/roboflow/supervision/pull/1409): 为 [`VertexLabelAnnotator`](https://supervision.roboflow.com/0.23.0/keypoint/annotators/#supervision.keypoint.annotators.VertexLabelAnnotator) 关键点 Annotator 添加了 `text_color` 选项。

- Changed [#1434](https://github.com/roboflow/supervision/pull/1434): [`InferenceSlicer`](https://supervision.roboflow.com/0.23.0/detection/tools/inference_slicer/) 现在具有 `overlap_wh` 参数，使处理重叠切片时计算切片尺寸更加容易。

- Fix [#1448](https://github.com/roboflow/supervision/pull/1448): 各种 Annotator 类型问题已得到解决，支持扩展的错误处理。

- Fix [#1348](https://github.com/roboflow/supervision/pull/1348): 引入了一种新的 [定位到特定视频帧](https://supervision.roboflow.com/0.23.0/utils/video/#supervision.utils.video.get_video_frames_generator) 的方法，解决了传统定位方法失败的情况。可以使用 `iterative_seek=True` 启用它。

```python
import supervision as sv

for frame in sv.get_video_frames_generator(
    source_path=<SOURCE_VIDEO_PATH>,
    start=60,
    iterative_seek=True
):
    ...
```

- Fix [#1424](https://github.com/roboflow/supervision/pull/1424): `plot_image` 函数现在清楚地表明尺寸单位是英寸。

!!! failure "Removed"

    [`ByteTrack`](trackers.md/#supervision.tracker.byte_tracker.core.ByteTrack) 中的 `track_buffer`, `track_thresh`, 和 `match_thresh` 参数已被弃用，并已在 `supervision-0.23.0` 中移除。请使用 `lost_track_buffer,` `track_activation_threshold`, 和 `minimum_matching_threshold`。

!!! failure "Removed"

    `sv.PolygonZone` 中的 `triggering_position` 参数已在 `supervision-0.23.0` 中移除。请使用 `triggering_anchors`。

!!! failure "Deprecated"

    `InferenceSlicer.__init__` 中的 `overlap_filter_strategy` 已弃用，将在 `supervision-0.27.0` 中移除。请使用 `overlap_strategy`。

!!! failure "Deprecated"

    `InferenceSlicer.__init__` 中的 `overlap_ratio_wh` 已弃用，将在 `supervision-0.27.0` 中移除。请使用 `overlap_wh`。

### 0.22.0 <small>Jul 12, 2024</small>

- Added [#1326](https://github.com/roboflow/supervision/pull/1326): [`sv.DetectionsDataset`](https://supervision.roboflow.com/0.22.0/datasets/core/#supervision.dataset.core.DetectionDataset) 和 [`sv.ClassificationDataset`](https://supervision.roboflow.com/0.22.0/datasets/core/#supervision.dataset.core.ClassificationDataset)，允许仅在需要时将图像加载到内存（延迟加载）。

!!! failure "Deprecated"

    使用参数 `images` 作为 `Dict[str, np.ndarray]` 来构造 `DetectionDataset` 已弃用，并将于 `supervision-0.26.0` 中移除。请改为传递路径列表 `List[str]`。

!!! failure "Deprecated"

    `DetectionDataset.images` 属性已弃用，并将于 `supervision-0.26.0` 中移除。请使用 `for path, image, annotation in dataset:` 循环加载图像，因为这不需要将所有图像加载到内存中。

```python
import roboflow
from roboflow import Roboflow
import supervision as sv

roboflow.login()
rf = Roboflow()

project = rf.workspace(<WORKSPACE_ID>).project(<PROJECT_ID>)
dataset = project.version(<PROJECT_VERSION>).download("coco")

ds_train = sv.DetectionDataset.from_coco(
    images_directory_path=f"{dataset.location}/train",
    annotations_path=f"{dataset.location}/train/_annotations.coco.json",
)

path, image, annotation = ds_train[0]
    # 按需加载图像

for path, image, annotation in ds_train:
    # 按需加载图像
```

- Added [#1296](https://github.com/roboflow/supervision/pull/1296): [`sv.Detections.from_lmm`](https://supervision.roboflow.com/0.22.0/detection/core/#supervision.detection.core.Detections.from_lmm) 现在支持解析 [Florence 2](https://huggingface.co/microsoft/Florence-2-large) 模型的输出，扩展了处理此大型多模态模型 (LMM) 输出的能力。这包括详细的对象检测、具有区域建议的 OCR、分割等。在我们的 [Colab notebook](https://colab.research.google.com/github/roboflow-ai/notebooks/blob/main/notebooks/how-to-finetune-florence-2-on-detection-dataset.ipynb) 中了解更多信息。

- Added [#1232](https://github.com/roboflow/supervision/pull/1232) 支持使用 Mediapipe 进行关键点检测。支持 [旧版](https://colab.research.google.com/github/googlesamples/mediapipe/blob/main/examples/pose_landmarker/python/%5BMediaPipe_Python_Tasks%5D_Pose_Landmarker.ipynb) 和 [现代版](https://ai.google.dev/edge/mediapipe/solutions/vision/pose_landmarker/python) 管道。请参阅 [`sv.KeyPoints.from_mediapipe`](https://supervision.roboflow.com/0.22.0/keypoint/core/#supervision.keypoint.core.KeyPoints.from_mediapipe) 获取更多信息。

- Added [#1316](https://github.com/roboflow/supervision/pull/1316): [`sv.KeyPoints.from_mediapipe`](https://supervision.roboflow.com/0.22.0/keypoint/core/#supervision.keypoint.core.KeyPoints.from_mediapipe) 已扩展以支持 Mediapipe 的 FaceMesh。此增强功能允许处理来自 `FaceLandmarker` 的面部地标和来自 `FaceMesh` 的旧版结果。

- Added [#1310](https://github.com/roboflow/supervision/pull/1310): [`sv.KeyPoints.from_detectron2`](https://supervision.roboflow.com/0.22.0/keypoint/core/#supervision.keypoint.core.KeyPoints.from_detectron2) 是一个新的 `KeyPoints` 方法，增加了对流行的 [Detectron 2](https://github.com/facebookresearch/detectron2) 平台中提取关键点的支持。

- Added [#1300](https://github.com/roboflow/supervision/pull/1300): [`sv.Detections.from_detectron2`](https://supervision.roboflow.com/0.22.0/detection/core/#supervision.detection.core.Detections.from_detectron2) 现在支持 Detectron2 的分割模型。生成的掩码可与 [`sv.MaskAnnotator`](https://supervision.roboflow.com/0.22.0/detection/annotators/#supervision.annotators.core.MaskAnnotator) 一起使用以显示注释。

```python
import supervision as sv
from detectron2 import model_zoo
from detectron2.engine import DefaultPredictor
from detectron2.config import get_cfg
import cv2

image = cv2.imread(<SOURCE_IMAGE_PATH>)
cfg = get_cfg()
cfg.merge_from_file(model_zoo.get_config_file("COCO-InstanceSegmentation/mask_rcnn_R_50_FPN_3x.yaml"))
cfg.MODEL.WEIGHTS = model_zoo.get_checkpoint_url("COCO-InstanceSegmentation/mask_rcnn_R_50_FPN_3x.yaml")
predictor = DefaultPredictor(cfg)

result = predictor(image)
detections = sv.Detections.from_detectron2(result)

mask_annotator = sv.MaskAnnotator()
annotated_frame = mask_annotator.annotate(scene=image.copy(), detections=detections)
```

- Added [#1277](https://github.com/roboflow/supervision/pull/1277): 如果您提供支持某种语言符号的字体，[`sv.RichLabelAnnotator`](https://supervision.roboflow.com/0.22.0/detection/annotators/#supervision.annotators.core.LabelAnnotator.annotate) 将在您的图像上绘制它们。
  - 各种其他 Annotator 已经过修改，以确保在使用 `numpy` 数组时能够正常进行就地操作。此外，我们修复了 `sv.ColorAnnotator` 在就地使用时用纯色填充框的错误。

```python
import cv2
import supervision as sv
import

image = cv2.imread(<SOURCE_IMAGE_PATH>)

model = get_model(model_id="yolov8n-640")
results = model.infer(image)[0]
detections = sv.Detections.from_inference(results)

rich_label_annotator = sv.RichLabelAnnotator(font_path=<TTF_FONT_PATH>)
annotated_image = rich_label_annotator.annotate(scene=image.copy(), detections=detections)
```

- Added [#1227](https://github.com/roboflow/supervision/pull/1227): 添加了加载 YOLO 格式的定向边界框数据集的支持。

```python
import supervision as sv

train_ds = sv.DetectionDataset.from_yolo(
    images_directory_path="/content/dataset/train/images",
    annotations_directory_path="/content/dataset/train/labels",
    data_yaml_path="/content/dataset/data.yaml",
    is_obb=True,
)

_, image, detections in train_ds[0]

obb_annotator = OrientedBoxAnnotator()
annotated_image = obb_annotator.annotate(scene=image.copy(), detections=detections)
```

- Fixed [#1312](https://github.com/roboflow/supervision/pull/1312): 修复了 [`CropAnnotator`](https://supervision.roboflow.com/0.22.0/detection/annotators/#supervision.annotators.core.TraceAnnotator.annotate)。

!!! failure "Removed"

    移除了 `BoxAnnotator`，但 `BoundingBoxAnnotator` 已重命名为 [`BoxAnnotator`](https://supervision.roboflow.com/0.22.0/detection/annotators/#supervision.annotators.core.BoxAnnotator)。请使用 [`BoxAnnotator`](https://supervision.roboflow.com/0.22.0/detection/annotators/#supervision.annotators.core.BoxAnnotator) 和 [`LabelAnnotator`](https://supervision.roboflow.com/0.22.0/detection/annotators/#supervision.annotators.core.LabelAnnotator) 的组合来模拟旧的 `BoundingBox` 行为。

!!! failure "Deprecated"

    `BoundingBoxAnnotator` 名称已被弃用，将在 `supervision-0.26.0` 中移除。它已被重命名为 [`BoxAnnotator`](https://supervision.roboflow.com/0.22.0/detection/annotators/#supervision.annotators.core.BoxAnnotator)。

- Added [#975](https://github.com/roboflow/supervision/pull/975) 📝 New Cookbooks: 将检测序列化为 [json](https://github.com/roboflow/supervision/blob/de896189b83a1f9434c0a37dd9192ee00d2a1283/docs/notebooks/serialise-detections-to-json.ipynb) 和 [csv](https://github.com/roboflow/supervision/blob/de896189b83a1f9434c0a37dd9192ee00d2a1283/docs/notebooks/serialise-detections-to-csv.ipynb)。

- Added [#1290](https://github.com/roboflow/supervision/pull/1290): 大部分是内部更改，我们的文件实用函数现在同时支持 `str` 和 `pathlib` 路径。

- Added [#1340](https://github.com/roboflow/supervision/pull/1340): 两个新的边界框格式转换方法 - [`xywh_to_xyxy`](https://supervision.roboflow.com/0.22.0/detection/utils/#supervision.detection.utils.xywh_to_xyxy) 和 [`xcycwh_to_xyxy`](https://supervision.roboflow.com/0.22.0/detection/utils/#supervision.detection.utils.xcycwh_to_xyxy)

!!! failure "Removed"

    `from_roboflow` 方法已因弃用而被移除。请改用 [from_inference](https://supervision.roboflow.com/0.22.0/detection/core/#supervision.detection.core.Detections.from_inference)。

!!! failure "Removed"

    `Color.white()` 已因弃用而被移除。请改用 `color.WHITE`。

!!! failure "Removed"

    `Color.black()` 已因弃用而被移除。请改用 `color.BLACK`。

!!! failure "Removed"

    `Color.red()` 已因弃用而被移除。请改用 `color.RED`。

!!! failure "Removed"

    `Color.green()` 已因弃用而被移除。请改用 `color.GREEN`。

!!! failure "Removed"

    `Color.blue()` 已因弃用而被移除。请改用 `color.BLUE`。

!!! failure "Removed"

    `ColorPalette.default()` 已因弃用而被移除。请改用 [ColorPalette.DEFAULT](https://supervision.roboflow.com/0.22.0/utils/draw/#supervision.draw.color.ColorPalette.DEFAULT)。

!!! failure "Removed"

    `FPSMonitor.__call__` 已因弃用而被移除。请改用属性 [FPSMonitor.fps](https://supervision.roboflow.com/0.22.0/utils/video/#supervision.utils.video.FPSMonitor.fps)。

### 0.21.0 <small>Jun 5, 2024</small>

- Added [#500](https://github.com/roboflow/supervision/pull/500): [`sv.Detections.with_nmm`](https://supervision.roboflow.com/0.21.0/detection/core/#supervision.detection.core.Detections.with_nmm) 以对当前对象检测集执行非极大值合并。

- Added [#1221](https://github.com/roboflow/supervision/pull/1221): [`sv.Detections.from_lmm`](https://supervision.roboflow.com/0.21.0/detection/core/#supervision.detection.core.Detections.from_lmm) 允许将大型多模态模型 (LMM) 的文本结果解析为 [`sv.Detections`](https://supervision.roboflow.com/0.21.0/detection/core/) 对象。目前 `from_lmm` 只支持 [PaliGemma](https://colab.research.google.com/github/roboflow-ai/notebooks/blob/main/notebooks/how-to-finetune-paligemma-on-detection-dataset.ipynb) 结果解析。

```python
import supervision as sv

paligemma_result = "<loc0256><loc0256><loc0768><loc0768> cat"
detections = sv.Detections.from_lmm(
    sv.LMM.PALIGEMMA,
    paligemma_result,
    resolution_wh=(1000, 1000),
    classes=["cat", "dog"],
)
detections.xyxy
# array([[250., 250., 750., 750.]])

detections.class_id
# array([0])
```

- Added [#1236](https://github.com/roboflow/supervision/pull/1236): [`sv.VertexLabelAnnotator`](https://supervision.roboflow.com/0.21.0/keypoint/annotators/#supervision.keypoint.annotators.EdgeAnnotator.annotate) 允许用自定义文本和颜色注释关键点骨架的每个顶点。

```python
import supervision as sv

image = ...
key_points = sv.KeyPoints(...)

edge_annotator = sv.EdgeAnnotator(
    color=sv.Color.GREEN,
    thickness=5
)
annotated_frame = edge_annotator.annotate(
    scene=image.copy(),
    key_points=key_points
)
```

- Added [#1147](https://github.com/roboflow/supervision/pull/1147): [`sv.KeyPoints.from_inference`](https://supervision.roboflow.com/0.21.0/keypoint/core/#supervision.keypoint.core.KeyPoints.from_inference) 允许从 [Inference](https://github.com/roboflow/inference) 结果创建 [`sv.KeyPoints`](https://supervision.roboflow.com/0.21.0/keypoint/core/)。

- Added [#1138](https://github.com/roboflow/supervision/pull/1138): [`sv.KeyPoints.from_yolo_nas`](https://supervision.roboflow.com/0.21.0/keypoint/core/#supervision.keypoint.core.KeyPoints.from_yolo_nas) 允许从 [YOLO-NAS](https://github.com/Deci-AI/super-gradients/blob/master/YOLONAS.md) 结果创建 [`sv.KeyPoints`](https://supervision.roboflow.com/0.21.0/keypoint/core/)。

- Added [#1163](https://github.com/roboflow/supervision/pull/1163): [`sv.mask_to_rle`](https://supervision.roboflow.com/0.21.0/datasets/utils/#supervision.dataset.utils.rle_to_mask) 和 [`sv.rle_to_mask`](https://supervision.roboflow.com/0.21.0/datasets/utils/#supervision.dataset.utils.rle_to_mask) 允许在掩码和 rle 格式之间轻松转换。

- Changed [#1236](https://github.com/roboflow/supervision/pull/1236): [`sv.InferenceSlicer`](https://supervision.roboflow.com/0.21.0/detection/tools/inference_slicer/) 允许选择重叠过滤策略 (`NONE`, `NON_MAX_SUPPRESSION` 和 `NON_MAX_MERGE`)。

- Changed [#1178](https://github.com/roboflow/supervision/pull/1178): [`sv.InferenceSlicer`](https://supervision.roboflow.com/0.21.0/detection/tools/inference_slicer/) 添加了实例分割模型支持。

```python
import cv2
import numpy as np
import supervision as sv
from inference import get_model

model = get_model(model_id="yolov8x-seg-640")
image = cv2.imread(<SOURCE_IMAGE_PATH>)

def callback(image_slice: np.ndarray) -> sv.Detections:
    results = model.infer(image_slice)[0]
    return sv.Detections.from_inference(results)

slicer = sv.InferenceSlicer(callback = callback)
detections = slicer(image)

mask_annotator = sv.MaskAnnotator()
label_annotator = sv.LabelAnnotator()

annotated_image = mask_annotator.annotate(
    scene=image, detections=detections)
annotated_image = label_annotator.annotate(
    scene=annotated_image, detections=detections)
```

- Changed [#1228](https://github.com/roboflow/supervision/pull/1228): [`sv.LineZone`](https://supervision.roboflow.com/0.21.0/detection/tools/line_zone/) 使其速度提高了 10-20 倍，具体取决于用例。

- Changed [#1163](https://github.com/roboflow/supervision/pull/1163): [`sv.DetectionDataset.from_coco`](https://supervision.roboflow.com/0.21.0/datasets/core/#supervision.dataset.core.DetectionDataset.from_coco) 和 [`sv.DetectionDataset.as_coco`](https://supervision.roboflow.com/0.21.0/datasets/core/#supervision.dataset.core.DetectionDataset.as_coco) 添加了对 run-length encoding (RLE) 掩码格式的支持。

### 0.20.0 <small>April 24, 2024</small>

- Added [#1128](https://github.com/roboflow/supervision/pull/1128): [`sv.KeyPoints`](/0.20.0/keypoint/core/#supervision.keypoint.core.KeyPoints) 以提供对姿态估计和更广泛的关键点检测模型的初始支持。

- Added [#1128](https://github.com/roboflow/supervision/pull/1128): [`sv.EdgeAnnotator`](/0.20.0/keypoint/annotators/#supervision.keypoint.annotators.EdgeAnnotator) 和 [`sv.VertexAnnotator`](/0.20.0/keypoint/annotators/#supervision.keypoint.annotators.VertexAnnotator) 以能够渲染关键点检测模型的结果。

```python
import cv2
import supervision as sv
from ultralytics import YOLO

image = cv2.imread(<SOURCE_IMAGE_PATH>)
model = YOLO('yolov8l-pose')

result = model(image, verbose=False)[0]
keypoints = sv.KeyPoints.from_ultralytics(result)

edge_annotators = sv.EdgeAnnotator(color=sv.Color.GREEN, thickness=5)
annotated_image = edge_annotators.annotate(image.copy(), keypoints)
```

- Changed [#1037](https://github.com/roboflow/supervision/pull/1037): [`sv.LabelAnnotator`](/0.20.0/annotators/#supervision.annotators.core.LabelAnnotator) 添加了一个额外的 `corner_radius` 参数，允许圆角化边界框的角落。

- Changed [#1109](https://github.com/roboflow/supervision/pull/1109): [`sv.PolygonZone`](/0.20.0/detection/tools/polygon_zone/#supervision.detection.tools.polygon_zone.PolygonZone) 使初始化 `sv.PolygonZone` 时不再需要 `frame_resolution_wh` 参数。

!!! failure "Deprecated"

    `sv.PolygonZone` 中的 `frame_resolution_wh` 参数已被弃用，将在 `supervision-0.24.0` 中移除。

- Changed [#1084](https://github.com/roboflow/supervision/pull/1084): [`sv.get_polygon_center`](/0.20.0/utils/geometry/#supervision.geometry.core.utils.get_polygon_center) 以计算更准确的多边形质心。

- Changed [#1069](https://github.com/roboflow/supervision/pull/1069): [`sv.Detections.from_transformers`](/0.20.0/detection/core/#supervision.detection.core.Detections.from_transformers) 添加了对 Transformers 分割模型和提取类名值ibouti。

```python
import torch
import supervision as sv
from PIL import Image
from transformers import DetrImageProcessor, DetrForSegmentation

processor = DetrImageProcessor.from_pretrained("facebook/detr-resnet-50-panoptic")
model = DetrForSegmentation.from_pretrained("facebook/detr-resnet-50-panoptic")

image = Image.open(<SOURCE_IMAGE_PATH>)
inputs = processor(images=image, return_tensors="pt")

with torch.no_grad():
    outputs = model(**inputs)

width, height = image.size
target_size = torch.tensor([[height, width]])
results = processor.post_process_segmentation(
    outputs=outputs, target_sizes=target_size)[0]
detections = sv.Detections.from_transformers(results, id2label=model.config.id2label)

mask_annotator = sv.MaskAnnotator()
label_annotator = sv.LabelAnnotator(text_position=sv.Position.CENTER)

annotated_image = mask_annotator.annotate(
    scene=image, detections=detections)
annotated_image = label_annotator.annotate(
    scene=annotated_image, detections=detections)
```

- Fixed [#787](https://github.com/roboflow/supervision/pull/787): [`sv.ByteTrack.update_with_detections`](/0.20.0/trackers/#supervision.tracker.byte_tracker.core.ByteTrack.update_with_detections) 之前在跟踪时删除了分割掩码。现在，`ByteTrack` 可以与分割模型一起使用。

### 0.19.0 <small>March 15, 2024</small>

- Added [#818](https://github.com/roboflow/supervision/pull/818): [`sv.CSVSink`](/0.19.0/detection/tools/save_detections/#supervision.detection.tools.csv_sink.CSVSink) 允许将图像、视频或流推理结果直接保存到 `.csv` 文件中。

```python
import supervision as sv
from ultralytics import YOLO

model = YOLO(<SOURCE_MODEL_PATH>)
csv_sink = sv.CSVSink(<RESULT_CSV_FILE_PATH>)
frames_generator = sv.get_video_frames_generator(<SOURCE_VIDEO_PATH>)

with csv_sink:
    for frame in frames_generator:
        result = model(frame)[0]
        detections = sv.Detections.from_ultralytics(result)
        csv_sink.append(detections, custom_data={<CUSTOM_LABEL>:<CUSTOM_DATA>})
```

- Added [#819](https://github.com/roboflow/supervision/pull/819): [`sv.JSONSink`](/0.19.0/detection/tools/save_detections/#supervision.detection.tools.csv_sink.JSONSink) 允许将图像、视频或流推理结果直接保存到 `.json` 文件中。

```python
import supervision as sv
from ultralytics import YOLO

model = YOLO(<SOURCE_MODEL_PATH>)
json_sink = sv.JSONSink(<RESULT_JSON_FILE_PATH>)
frames_generator = sv.get_video_frames_generator(<SOURCE_VIDEO_PATH>)

with json_sink:
    for frame in frames_generator:
        result = model(frame)[0]
        detections = sv.Detections.from_ultralytics(result)
        json_sink.append(detections, custom_data={<CUSTOM_LABEL>:<CUSTOM_DATA>})
```

- Added [#847](https://github.com/roboflow/supervision/pull/847): [`sv.mask_iou_batch`](/0.19.0/detection/utils/#supervision.detection.utils.mask_iou_batch) 允许计算两组掩码的 IoU（交并比）。

- Added [#847](https://github.com/roboflow/supervision/pull/847): [`sv.mask_non_max_suppression`](/0.19.0/detection/utils/#supervision.detection.utils.mask_non_max_suppression) 允许对分割预测执行非极大值抑制 (NMS)。

- Added [#888](https://github.com/roboflow/supervision/pull/888): [`sv.CropAnnotator`](/0.19.0/annotators/#supervision.annotators.core.CropAnnotator) 允许用户用缩放后的检测作物来注释场景。

```python
import cv2
import supervision as sv
from inference import get_model

image = cv2.imread(<SOURCE_IMAGE_PATH>)
model = get_model(model_id="yolov8n-640")

result = model.infer(image)[0]
detections = sv.Detections.from_inference(result)

crop_annotator = sv.CropAnnotator()
annotated_frame = crop_annotator.annotate(
    scene=image.copy(),
    detections=detections
)
```

- Changed [#827](https://github.com/roboflow/supervision/pull/827): [`sv.ByteTrack.reset`](/0.19.0/trackers/#supervision.tracker.ByteTrack.reset) 允许用户清除跟踪器状态，从而可以连续处理多个视频文件。

- Changed [#802](https://github.com/roboflow/supervision/pull/802): [`sv.LineZoneAnnotator`](/0.19.0/detection/tools/line_zone/#supervision.detection.line_zone.LineZone) 允许使用 `display_in_count` 和 `display_out_count` 属性隐藏进/出计数。

- Changed [#787](https://github.com/roboflow/supervision/pull/787): 更新了 [`sv.ByteTrack`](/0.19.0/trackers/#supervision.tracker.ByteTrack) 的输入参数和文档字符串，以提高可读性和易用性。

!!! failure "Deprecated"

    `sv.ByteTrack` 中的 `track_buffer`, `track_thresh`, 和 `match_thresh` 参数已被弃用，将在 `supervision-0.23.0` 中移除。请使用 `lost_track_buffer,` `track_activation_threshold`, 和 `minimum_matching_threshold`。

- Changed [#910](https://github.com/roboflow/supervision/pull/910): [`sv.PolygonZone`](/0.19.0/detection/tools/polygon_zone/#supervision.detection.tools.polygon_zone.PolygonZone) 现在接受一个特定边界框锚点列表，这些锚点必须在区域内才能计入检测。此更新标志着与先前要求（当时所有四个边界框角都必须存在）相比的重大改进。用户现在可以指定一个锚点，例如 `sv.Position.BOTTOM_CENTER`，或任何其他由 `List[sv.Position]` 定义的锚点组合。

!!! failure "Deprecated"

    `sv.PolygonZone` 中的 `triggering_position` 参数已被弃用，将在 `supervision-0.23.0` 中移除。请使用 `triggering_anchors`。

- Changed [#875](https://github.com/roboflow/supervision/pull/875): Annotators 添加了 Pillow 图像支持。所有 supervision Annotators 现在都可以接受 numpy 数组或 Pillow Image 作为图像。它们会自动检测其类型，绘制注释，并以与输入相同的格式返回输出。

- Fixed [#944](https://github.com/roboflow/supervision/pull/944): [`sv.DetectionsSmoother`](/0.19.0/detection/tools/smoother/#detection-smoother) 从 `sv.Detections` 中移除了 `tracking_id`。

### 0.18.0 <small>Jan 25, 2024</small>

- Added [#720](https://github.com/roboflow/supervision/pull/720): [`sv.PercentageBarAnnotator`](/0.18.0/annotators/#percentagebarannotator) 允许使用表示置信度或其他自定义属性的百分比值来注释图像和视频。

```python
>>> import supervision as sv

>>> image = ...
>>> detections = sv.Detections(...)

>>> percentage_bar_annotator = sv.PercentageBarAnnotator()
>>> annotated_frame = percentage_bar_annotator.annotate(
...     scene=image.copy(),
...     detections=detections
... )
```

- Added [#702](https://github.com/roboflow/supervision/pull/702): [`sv.RoundBoxAnnotator`](/0.18.0/annotators/#roundboxannotator) 允许用圆角边界框来注释图像和视频。

- Added [#770](https://github.com/roboflow/supervision/pull/770): [`sv.OrientedBoxAnnotator`](/0.18.0/annotators/#orientedboxannotator) 允许用 OBB（定向边界框）来注释图像和视频。

```python
import cv2
import supervision as sv
from ultralytics import YOLO

image = cv2.imread(<SOURCE_IMAGE_PATH>)
model = YOLO("yolov8n-obb.pt")

result = model(image)[0]
detections = sv.Detections.from_ultralytics(result)

oriented_box_annotator = sv.OrientedBoxAnnotator()
annotated_frame = oriented_box_annotator.annotate(
    scene=image.copy(),
    detections=detections
)
```

- Added [#696](https://github.com/roboflow/supervision/pull/696): [`sv.DetectionsSmoother`](/0.18.0/detection/tools/smoother/#detection-smoother) 允许在视频跟踪中对检测进行多帧平滑。

- Added [#769](https://github.com/roboflow/supervision/pull/769): [`sv.ColorPalette.from_matplotlib`](/0.18.0/draw/color/#supervision.draw.color.ColorPalette.from_matplotlib) 允许用户从 Matplotlib 调色板创建 `sv.ColorPalette` 实例。

```python
>>> import supervision as sv

>>> sv.ColorPalette.from_matplotlib('viridis', 5)
ColorPalette(colors=[Color(r=68, g=1, b=84), Color(r=59, g=82, b=139), ...])
```

- Changed [#770](https://github.com/roboflow/supervision/pull/770): [`sv.Detections.from_ultralytics`](/0.18.0/detection/core/#supervision.detection.core.Detections.from_ultralytics) 添加了对 OBB（定向边界框）的支持。

- Changed [#735](https://github.com/roboflow/supervision/pull/735): [`sv.LineZone`](/0.18.0/detection/tools/line_zone/#linezone) 现在接受一个特定边界锚点的列表，检测需要穿过这些锚点才能被计数。此更新标志着从先前要求（需要所有四个边界框角）的重大改进。用户现在可以指定一个锚点，例如 `sv.Position.BOTTOM_CENTER`，或 `List[sv.Position]` 定义的任何其他锚点组合。

- Changed [#756](https://github.com/roboflow/supervision/pull/756): [`sv.Color`](/0.18.0/draw/color/#color) 和 [`sv.ColorPalette`](/0.18.0/draw/color/#colorpalette) 访问预定义颜色的方法，从基于函数的实现 (`sv.Color.red()`) 转换为更直观和惯用的基于属性的方法 (`sv.Color.RED`)。

!!! failure "Deprecated"

    `sv.ColorPalette.default()` 已弃用，将在 `supervision-0.22.0` 中移除。请改用 `sv.ColorPalette.DEFAULT`。

- Changed [#769](https://github.com/roboflow/supervision/pull/769): [`sv.ColorPalette.DEFAULT`](/0.18.0/draw/color/#colorpalette) 值，为用户提供更广泛的注释颜色。

- Changed [#677](https://github.com/roboflow/supervision/pull/677): 将 `sv.Detections.from_roboflow` 重命名为 [`sv.Detections.from_inference`](/0.18.0/detection/core/#supervision.detection.core.Detections.from_inference)，简化了其功能，使其与 [inference](https://github.com/roboflow/inference) pip 包和 Robloflow [hosted API](https://docs.roboflow.com/deploy/hosted-api) 兼容。

!!! failure "Deprecated"

    `Detections.from_roboflow()` 已弃用，将在 `supervision-0.22.0` 中移除。请使用 `Detections.from_inference`。

- Fixed [#735](https://github.com/roboflow/supervision/pull/735): 修复了 [`sv.LineZone`](/0.18.0/detection/tools/line_zone/#linezone) 的功能，使其能够准确地更新对象从任何方向（包括侧面）穿过线条时的计数器。此增强功能支持更精确的跟踪和分析，例如计算道路每条车道的独立进/出计数。

### 0.17.0 <small>Dec 06, 2023</small>

- Added [#633](https://github.com/roboflow/supervision/pull/633): [`sv.PixelateAnnotator`](/0.17.0/annotators/#supervision.annotators.core.PixelateAnnotator) 允许像素化图像和视频上的对象。

- Added [#652](https://github.com/roboflow/supervision/pull/652): [`sv.TriangleAnnotator`](/0.17.0/annotators/#supervision.annotators.core.TriangleAnnotator) 允许用三角形标记注释图像和视频。

- Added [#602](https://github.com/roboflow/supervision/pull/602): [`sv.PolygonAnnotator`](/0.17.0/annotators/#supervision.annotators.core.PolygonAnnotator) 允许用分割掩码轮廓注释图像和视频。

```python
>>> import supervision as sv

>>> image = ...
>>> detections = sv.Detections(...)

>>> polygon_annotator = sv.PolygonAnnotator()
>>> annotated_frame = polygon_annotator.annotate(
...     scene=image.copy(),
...     detections=detections
... )
```

- Added [#476](https://github.com/roboflow/supervision/pull/476): [`sv.assets`](/0.18.0/assets/) 允许下载视频文件，您可以在演示中使用这些文件。

```python
>>> from supervision.assets import download_assets, VideoAssets
>>> download_assets(VideoAssets.VEHICLES)
"vehicles.mp4"
```

- Added [#605](https://github.com/roboflow/supervision/pull/605): [`Position.CENTER_OF_MASS`](/0.17.0/geometry/core/#position) 允许将标签放置在分割掩码的质心。

- Added [#651](https://github.com/roboflow/supervision/pull/651): [`sv.scale_boxes`](/0.17.0/detection/utils/#supervision.detection.utils.scale_boxes) 允许缩放 [`sv.Detections.xyxy`](/0.17.0/detection/core/#supervision.detection.core.Detections) 值。

- Added [#637](https://github.com/roboflow/supervision/pull/637): [`sv.calculate_dynamic_text_scale`](/0.17.0/draw/utils/#supervision.draw.utils.calculate_dynamic_text_scale) 和 [`sv.calculate_dynamic_line_thickness`](/0.17.0/draw/utils/#supervision.draw.utils.calculate_dynamic_line_thickness) 允许文本比例和线条粗细与图像分辨率匹配。

- Added [#620](https://github.com/roboflow/supervision/pull/620): [`sv.Color.as_hex`](/0.17.0/draw/color/#supervision.draw.color.Color.as_hex) 允许以 HEX 格式提取颜色值。

- Added [#572](https://github.com/roboflow/supervision/pull/572): [`sv.Classifications.from_timm`](/0.17.0/classification/core/#supervision.classification.core.Classifications.from_timm) 允许从 [timm](https://huggingface.co/docs/hub/timm) 模型加载分类结果。

- Added [#478](https://github.com/roboflow/supervision/pull/478): [`sv.Classifications.from_clip`](/0.17.0/classification/core/#supervision.classification.core.Classifications.from_clip) 允许从 [clip](https://github.com/openai/clip) 模型加载分类结果。

- Added [#571](https://github.com/roboflow/supervision/pull/571): [`sv.Detections.from_azure_analyze_image`](/0.17.0/detection/core/#supervision.detection.core.Detections.from_azure_analyze_image) 允许从 [Azure Image Analysis](https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/concept-object-detection-40) 加载检测结果。

- Changed [#646](https://github.com/roboflow/supervision/pull/646): 将 `sv.BoxMaskAnnotator` 重命名为 [`sv.ColorAnnotator`](/0.17.0/annotators/#supervision.annotators.core.ColorAnnotator)。

- Changed [#606](https://github.com/roboflow/supervision/pull/606): [`sv.MaskAnnotator`](/0.17.0/annotators/#supervision.annotators.core.MaskAnnotator) 使其速度 **提升 5 倍**。

- Fixed [#584](https://github.com/roboflow/supervision/pull/584): 修复了 [`sv.DetectionDataset.from_yolo`](/0.17.0/datasets/#supervision.dataset.core.DetectionDataset.from_yolo) 以忽略注释文件中的空行。

- Fixed [#555](https://github.com/roboflow/supervision/pull/555): 修复了 [`sv.BlurAnnotator`](/0.17.0/annotators/#supervision.annotators.core.BlurAnnotator) 在模糊检测之前裁剪负坐标。

- Fixed [#511](https://github.com/roboflow/supervision/pull/511): 修复了 [`sv.TraceAnnotator`](/0.17.0/annotators/#supervision.annotators.core.TraceAnnotator) 以尊重轨迹位置。

### 0.16.0 <small>Oct 19, 2023</small>

- Added [#422](https://github.com/roboflow/supervision/pull/422): [`sv.BoxMaskAnnotator`](/0.16.0/annotators/#supervision.annotators.core.BoxMaskAnnotator) 允许用 mox 掩码注释图像和视频。

- Added [#433](https://github.com/roboflow/supervision/pull/433): [`sv.HaloAnnotator`](/0.16.0/annotators/#supervision.annotators.core.HaloAnnotator) 允许用光晕效果注释图像和视频。

```python
>>> import supervision as sv

>>> image = ...
>>> detections = sv.Detections(...)

>>> halo_annotator = sv.HaloAnnotator()
>>> annotated_frame = halo_annotator.annotate(
...     scene=image.copy(),
...     detections=detections
... )
```

- Added [#466](https://github.com/roboflow/supervision/pull/466): [`sv.HeatMapAnnotator`](/0.16.0/annotators/#supervision.annotators.core.HeatMapAnnotator) 允许用热图注释视频。

- Added [#492](https://github.com/roboflow/supervision/pull/492): [`sv.DotAnnotator`](/0.16.0/annotators/#supervision.annotators.core.DotAnnotator) 允许用点注释图像和视频。

- Added [#449](https://github.com/roboflow/supervision/pull/449): [`sv.draw_image`](/0.16.0/draw/utils/#supervision.draw.utils.draw_image) 允许在给定场景中绘制图像，具有指定的不透明度和尺寸。

- Added [#280](https://github.com/roboflow/supervision/pull/280): [`sv.FPSMonitor`](/0.16.0/utils/video/#supervision.utils.video.FPSMonitor) 用于监视每秒帧数 (FPS) 以评估延迟。

- Added [#454](https://github.com/roboflow/supervision/pull/454): 🤗 Hugging Face Annotators [space](https://huggingface.co/spaces/Roboflow/Annotators)。

- Changed [#482](https://github.com/roboflow/supervision/pull/482): [`sv.LineZone.trigger`](/0.16.0/detection/tools/line_zone/#supervision.detection.line_counter.LineZone.trigger) 现在返回 `Tuple[np.ndarray, np.ndarray]`。第一个数组指示哪些检测从外部交叉到内部。第二个数组指示哪些检测从内部交叉到外部。

- Changed [#465](https://github.com/roboflow/supervision/pull/465): 将 Annotator 参数名称从 `color_map: str` 更改为 `color_lookup: ColorLookup` 枚举，以提高类型安全性。

- Changed [#426](https://github.com/roboflow/supervision/pull/426): [`sv.MaskAnnotator`](/0.16.0/annotators/#supervision.annotators.core.MaskAnnotator) 可实现 2 倍更快的注释。

- Fixed [#477](https://github.com/roboflow/supervision/pull/77): Poetry 环境定义允许正确的本地安装。

- Fixed [#430](https://github.com/roboflow/supervision/pull/430): 修复了 [`sv.ByteTrack`](/0.16.0/trackers/#supervision.tracker.byte_tracker.core.ByteTrack) 返回 `np.array([], dtype=int)` 的情况，当 `svDetections` 为空时。

!!! failure "Deprecated"

    `sv.Detections.from_yolov8` 和 `sv.Classifications.from_yolov8` 已被 [`sv.Detections.from_ultralytics`](/0.16.0/detection/core/#supervision.detection.core.Detections.from_ultralytics) 和 [`sv.Classifications.from_ultralytics`](/0.16.0/classification/core/#supervision.classification.core.Classifications.from_ultralytics) 取代。

### 0.15.0 <small>Oct 5, 2023</small>

- Added [#170](https://github.com/roboflow/supervision/pull/170): [`sv.BoundingBoxAnnotator`](/0.15.0/annotators/#supervision.annotators.core.BoundingBoxAnnotator) 允许用边界框注释图像和视频。

- Added [#170](https://github.com/roboflow/supervision/pull/170): [`sv.BoxCornerAnnotator `](/0.15.0/annotators/#supervision.annotators.core.BoxCornerAnnotator) 允许仅用边界框角注释图像和视频。

- Added [#170](https://github.com/roboflow/supervision/pull/170): [`sv.MaskAnnotator`](/0.15.0/annotators/#supervision.annotators.core.MaskAnnotator) 允许用分割掩码注释图像和视频。

- Added [#170](https://github.com/roboflow/supervision/pull/170): [`sv.EllipseAnnotator`](/0.15.0/annotators/#supervision.annotators.core.EllipseAnnotator) 允许用椭圆（体育比赛风格）注释图像和视频。

- Added [#386](https://github.com/roboflow/supervision/pull/386): [`sv.CircleAnnotator`](/0.15.0/annotators/#supervision.annotators.core.CircleAnnotator) 允许用圆圈注释图像和视频。

- Added [#354](https://github.com/roboflow/supervision/pull/354): [`sv.TraceAnnotator`](/0.15.0/annotators/#supervision.annotators.core.TraceAnnotator) 允许在视频上绘制移动对象的路径。

- Added [#405](https://github.com/roboflow/supervision/pull/405): [`sv.BlurAnnotator`](/0.15.0/annotators/#supervision.annotators.core.BlurAnnotator) 允许模糊图像和视频上的对象。

```python
>>> import supervision as sv

>>> image = ...
>>> detections = sv.Detections(...)

>>> bounding_box_annotator = sv.BoundingBoxAnnotator()
>>> annotated_frame = bounding_box_annotator.annotate(
...     scene=image.copy(),
...     detections=detections
... )
```

- Added [#354](https://github.com/roboflow/supervision/pull/354): Supervision 使用 [示例](https://github.com/roboflow/supervision/tree/develop/examples/traffic_analysis)。您现在可以学习如何使用 Supervision 进行交通流量分析。

- Changed [#399](https://github.com/roboflow/supervision/pull/399): [`sv.Detections.from_roboflow`](/0.15.0/detection/core/#supervision.detection.core.Detections.from_roboflow) 现在不需要指定 `class_list`。`class_id` 值可以直接从 [inference](https://github.com/roboflow/inference) 响应中提取。

- Changed [#381](https://github.com/roboflow/supervision/pull/381): [`sv.VideoSink`](/0.15.0/utils/video/#videosink) 现在允许自定义输出编解码器。

- Changed [#361](https://github.com/roboflow/supervision/pull/361): [`sv.InferenceSlicer`](/0.15.0/detection/tools/inference_slicer/#supervision.detection.tools.inference_slicer.InferenceSlicer) 现在可以在多线程模式下运行。

- Fixed [#348](https://github.com/roboflow/supervision/pull/348): 修复了 [`sv.Detections.from_deepsparse`](/0.15.0/detection/core/#supervision.detection.core.Detections.from_deepsparse) 以允许处理空的 [deepsparse](https://github.com/neuralmagic/deepsparse) 结果对象。

### 0.14.0 <small>Aug 31, 2023</small>

- Added [#282](https://github.com/roboflow/supervision/pull/282): 支持 SAHI 推理技术，使用 [`sv.InferenceSlicer`](/0.14.0/detection/tools/inference_slicer)。

```python
>>> import cv2
>>> import supervision as sv
>>> from ultralytics import YOLO

>>> image = cv2.imread(SOURCE_IMAGE_PATH)
>>> model = YOLO(...)

>>> def callback(image_slice: np.ndarray) -> sv.Detections:
...     result = model(image_slice)[0]
...     return sv.Detections.from_ultralytics(result)

>>> slicer = sv.InferenceSlicer(callback = callback)

>>> detections = slicer(image)
```

- Added [#297](https://github.com/roboflow/supervision/pull/297): [`Detections.from_deepsparse`](/0.14.0/detection/core/#supervision.detection.core.Detections.from_deepsparse) 以实现与 [DeepSparse](https://github.com/neuralmagic/deepsparse) 框架的无缝集成。

- Added [#281](https://github.com/roboflow/supervision/pull/281): [`sv.Classifications.from_ultralytics`](/0.14.0/classification/core/#supervision.classification.core.Classifications.from_ultralytics) 以实现与 [Ultralytics](https://github.com/ultralytics/ultralytics) 框架的无缝集成。这将使您能够将 supervision 与 Ultralytics 支持的所有 [models](https://docs.ultralytics.com/models/)一起使用。

!!! failure "Deprecated"

    [sv.Detections.from_yolov8](/0.14.0/detection/core/#supervision.detection.core.Detections.from_yolov8) 和 [sv.Classifications.from_yolov8](/0.14.0/classification/core/#supervision.classification.core.Classifications.from_yolov8) 已被弃用，将在 `supervision-0.16.0` 版本中移除。

- Added [#341](https://github.com/roboflow/supervision/pull/341): 第一个 supervision 用法示例脚本，展示了如何使用 YOLOv8 + Supervision 检测和跟踪视频中的对象。

- Changed [#296](https://github.com/roboflow/supervision/pull/296): [`sv.ClassificationDataset`](/0.14.0/dataset/core/#supervision.dataset.core.ClassificationDataset) 和 [`sv.DetectionDataset`](/0.14.0/dataset/core/#supervision.dataset.core.DetectionDataset) 现在使用图像路径（而不是图像名称）作为数据集键。

- Fixed [#300](https://github.com/roboflow/supervision/pull/300): 修复了 [`Detections.from_roboflow`](/0.14.0/detection/core/#supervision.detection.core.Detections.from_roboflow) 以过滤掉少于 3 个点的多边形。

### 0.13.0 <small>Aug 8, 2023</small>

- Added [#236](https://github.com/roboflow/supervision/pull/236): 支持使用 [`sv.MeanAveragePrecision`](/0.13.0/metrics/detection/#meanaverageprecision) 对对象检测模型进行平均精度 (mAP) 计算。

```python
>>> import supervision as sv
>>> from ultralytics import YOLO

>>> dataset = sv.DetectionDataset.from_yolo(...)

>>> model = YOLO(...)
>>> def callback(image: np.ndarray) -> sv.Detections:
...     result = model(image)[0]
...     return sv.Detections.from_yolov8(result)

>>> mean_average_precision = sv.MeanAveragePrecision.benchmark(
...     dataset = dataset,
...     callback = callback
... )

>>> mean_average_precision.map50_95
0.433
```

- Added [#256](https://github.com/roboflow/supervision/pull/256): 支持使用 [`sv.ByteTrack`](/0.13.0/tracker/core/#bytetrack) 进行对象跟踪。

- Added [#222](https://github.com/roboflow/supervision/pull/222): [`sv.Detections.from_ultralytics`](/0.13.0/detection/core/#supervision.detection.core.Detections.from_ultralytics) 以启用与 [Ultralytics](https://github.com/ultralytics/ultralytics) 框架的无缝集成。这将使您能够将 `supervision` 与 Ultralytics 支持的所有 [models](https://docs.ultralytics.com/models/) 一起使用。

!!! failure "Deprecated"

    [`sv.Detections.from_yolov8`](/0.13.0/detection/core/#supervision.detection.core.Detections.from_yolov8) 已被弃用，将在 `supervision-0.15.0` 版本中移除。

- Added [#191](https://github.com/roboflow/supervision/pull/191): [`sv.Detections.from_paddledet`](/0.13.0/detection/core/#supervision.detection.core.Detections.from_paddledet) 以启用与 [PaddleDetection](https://github.com/PaddlePaddle/PaddleDetection) 框架的无缝集成。

- Added [#245](https://github.com/roboflow/supervision/pull/245): 支持使用 [`sv.DetectionDataset.`](/0.13.0/dataset/core/#supervision.dataset.core.DetectionDataset.from_pascal_voc) 加载 PASCAL VOC 分割数据集。

### 0.12.0 <small>Jul 24, 2023</small>

!!! failure "Python 3.7. Support Terminated"

    随着 `supervision-0.12.0` 版本的发布，我们终止了对 Python 3.7 的官方支持。

- Added [#177](https://github.com/roboflow/supervision/pull/177): 对对象检测模型基准测试的初始支持，使用 [`sv.ConfusionMatrix`](/0.12.0/metrics/detection/#confusionmatrix)。

```python
>>> import supervision as sv
>>> from ultralytics import YOLO

>>> dataset = sv.DetectionDataset.from_yolo(...)

>>> model = YOLO(...)
>>> def callback(image: np.ndarray) -> sv.Detections:
...     result = model(image)[0]
...     return sv.Detections.from_yolov8(result)

>>> confusion_matrix = sv.ConfusionMatrix.benchmark(
...     dataset = dataset,
...     callback = callback
... )

>>> confusion_matrix.matrix
array([
    [0., 0., 0., 0.],
    [0., 1., 0., 1.],
    [0., 1., 1., 0.],
    [1., 1., 0., 0.]
])
```

- Added [#173](https://github.com/roboflow/supervision/pull/173): [`Detections.from_mmdetection`](/0.12.0/detection/core/#supervision.detection.core.Detections.from_mmdetection) 以实现与 [MMDetection](https://github.com/open-mmlab/mmdetection) 框架的无缝集成。

- Added [#130](https://github.com/roboflow/supervision/issues/130): 能够以 `headless` 或 `desktop` 模式 [安装](https://supervision.roboflow.com/) 包。

- Changed [#180](https://github.com/roboflow/supervision/pull/180): 将打包方法从 `setup.py` 更改为 `pyproject.toml`。

- Fixed [#188](https://github.com/roboflow/supervision/issues/188): 修复了 [`sv.DetectionDataset.from_cooc`](/0.12.0/dataset/core/#supervision.dataset.core.DetectionDataset.from_coco) 在没有注释的图像时无法加载的问题。

- Fixed [#226](https://github.com/roboflow/supervision/issues/226): 修复了 [`sv.DetectionDataset.from_yolo`](/0.12.0/dataset/core/#supervision.dataset.core.DetectionDataset.from_yolo) 无法加载背景实例的问题。

### 0.11.1 <small>Jun 29, 2023</small>

- Fix [#165](https://github.com/roboflow/supervision/pull/165): 修复了 [`as_folder_structure`](/0.11.1/dataset/core/#supervision.dataset.core.ClassificationDataset.as_folder_structure) 在推断结果为 [`sv.ClassificationDataset`](/0.11.1/dataset/core/#classificationdataset) 时无法保存的问题。

### 0.11.0 <small>Jun 28, 2023</small>

- Added [#150](https://github.com/roboflow/supervision/pull/150): 能够使用 [`as_coco`](/0.11.0/dataset/core/#supervision.dataset.core.DetectionDataset.as_coco) 和 [`from_coco`](/0.11.0/dataset/core/#supervision.dataset.core.DetectionDataset.from_coco) 方法以 COCO 格式加载和保存 [`sv.DetectionDataset`](/0.11.0/dataset/core/#detectiondataset)。

```python
>>> import supervision as sv

>>> ds = sv.DetectionDataset.from_coco(
...     images_directory_path='...',
...     annotations_path='...'
... )

>>> ds.as_coco(
...     images_directory_path='...',
...     annotations_path='...'
... )
```

- Added [#158](https://github.com/roboflow/supervision/pull/158): 能够使用 [`merge`](/0.11.0/dataset/core/#supervision.dataset.core.DetectionDataset.merge) 方法合并多个 [`sv.DetectionDataset`](/0.11.0/dataset/core/#detectiondataset)。

```python
>>> import supervision as sv

>>> ds_1 = sv.DetectionDataset(...)
>>> len(ds_1)
100
>>> ds_1.classes
['dog', 'person']

>>> ds_2 = sv.DetectionDataset(...)
>>> len(ds_2)
200
>>> ds_2.classes
['cat']

>>> ds_merged = sv.DetectionDataset.merge([ds_1, ds_2])
>>> len(ds_merged)
300
>>> ds_merged.classes
['cat', 'dog', 'person']
```

- Added [#162](https://github.com/roboflow/supervision/pull/162): 向 [`sv.get_video_frames_generator`](/0.11.0/utils/video/#get_video_frames_generator) 添加了额外的 `start` 和 `end` 参数，允许仅为视频的选定部分生成帧。

- Fix [#157](https://github.com/roboflow/supervision/pull/157): 从 `data.yaml` 错误加载 YOLO 数据集类名。

### 0.10.0 <small>Jun 14, 2023</small>

- Added [#125](https://github.com/roboflow/supervision/pull/125): 能够以文件夹结构格式加载和保存 [`sv.ClassificationDataset`](/0.10.0/dataset/core/#classificationdataset)。

```python
>>> import supervision as sv

>>> cs = sv.ClassificationDataset.from_folder_structure(
...     root_directory_path='...'
... )

>>> cs.as_folder_structure(
...     root_directory_path='...'
... )
```

- Added [#125](https://github.com/roboflow/supervision/pull/125): 支持 [`sv.ClassificationDataset.split`](/0.10.0/dataset/core/#supervision.dataset.core.ClassificationDataset.split)，允许将 `sv.ClassificationDataset` 分为两部分。

- Added [#110](https://github.com/roboflow/supervision/pull/110): 能够使用 [`sv.Detections.from_roboflow`](/0.10.0/detection/core/#supervision.detection.core.Detections.from_roboflow) 从 Roboflow API 结果中提取掩码。

- Added [commit hash](https://github.com/roboflow/supervision/commit/d000292eb2f2342544e0947b65528082e60fb8d6): Supervision Quickstart [notebook](https://colab.research.google.com/github/roboflow/supervision/blob/main/demo.ipynb)，您可以在其中了解有关 Detection、Dataset 和 Video API 的更多信息。

- Changed [#135](https://github.com/roboflow/supervision/pull/135): `sv.get_video_frames_generator` 文档，以更好地描述实际行为。

### 0.9.0 <small>Jun 7, 2023</small>

- Added [#118](https://github.com/roboflow/supervision/pull/118): 能够通过索引、索引列表或切片来选择 [`sv.Detections`](/0.9.0/detection/core/#supervision.detection.core.Detections.__getitem__)。以下示例说明了新的选择方法。

```python
>>> import supervision as sv

>>> detections = sv.Detections(...)
>>> len(detections[0])
1
>>> len(detections[[0, 1]])
2
>>> len(detections[0:2])
2
```

- Added [#101](https://github.com/roboflow/supervision/pull/101): 能够使用 [`sv.Detections.from_yolov8`](/0.8.0/detection/core/#supervision.detection.core.Detections.from_yolov8) 从 YOLOv8 结果中提取掩码。以下示例说明了如何从 YOLOv8 模型推理结果中提取布尔掩码。

- Added [#122](https://github.com/roboflow/supervision/pull/122): 能够使用 [`sv.crop`](/0.9.0/utils/image/#crop) 来裁剪图像。以下示例显示了如何为 `sv.Detections` 中的每个检测获取单独的裁剪。

- Added [#120](https://github.com/roboflow/supervision/pull/120): 能够使用 [`sv.ImageSink`](/0.9.0/utils/image/#imagesink) 将多个图像方便地保存到目录中。以下示例显示了如何将每十个视频帧保存为单独的图像。

```python
>>> import supervision as sv

>>> with sv.ImageSink(target_dir_path='target/directory/path') as sink:
...     for image in sv.get_video_frames_generator(source_path='source_video.mp4', stride=10):
...         sink.save_image(image=image)
```

- Fixed [#106](https://github.com/roboflow/supervision/issues/106): 修复了 [`sv.PolygonZone`](/0.8.0/detection/tools/polygon_zone/#polygonzone) 坐标处理不方便的问题。现在 `sv.PolygonZone` 接受 `[[x1, y1], [x2, y2], ...]` 形式的坐标，可以是整数或浮点数。

### 0.8.0 <small>May 17, 2023</small>

- Added [#100](https://github.com/roboflow/supervision/pull/100): 支持数据集继承。当前的 `Dataset` 已重命名为 `DetectionDataset`。现在 [`DetectionDataset`](/0.8.0/dataset/core/#detectiondataset) 继承自 `BaseDataset`。此更改是为了强制未来不同类型计算机视觉数据集 API 的一致性。
- Added [#100](https://github.com/roboflow/supervision/pull/100): 能够使用 [`DetectionDataset.as_yolo`](/0.8.0/dataset/core/#supervision.dataset.core.DetectionDataset.as_yolo) 将数据集保存为 YOLO 格式。

```python
>>> import roboflow
>>> from roboflow import Roboflow
>>> import supervision as sv

>>> roboflow.login()

>>> rf = Roboflow()

>>> project = rf.workspace(WORKSPACE_ID).project(PROJECT_ID)
>>> dataset = project.version(PROJECT_VERSION).download("yolov5")

>>> ds = sv.DetectionDataset.from_yolo(
...     images_directory_path=f"{dataset.location}/train/images",
...     annotations_directory_path=f"{dataset.location}/train/labels",
...     data_yaml_path=f"{dataset.location}/data.yaml"
... )

>>> ds.classes
['dog', 'person']
```

- Added [#102](https://github.com/roboflow/supervision/pull/103): 支持 [`DetectionDataset.split`](/0.8.0/dataset/core/#supervision.dataset.core.DetectionDataset.split)，允许将 `DetectionDataset` 分为两部分。

```python
>>> import supervision as sv

>>> ds = sv.DetectionDataset(...)
>>> train_ds, test_ds = ds.split(split_ratio=0.7, random_state=42, shuffle=True)

>>> len(train_ds), len(test_ds)
(700, 300)
```

- Changed [#100](https://github.com/roboflow/supervision/pull/100): `DetectionDataset.as_yolo` 和 `DetectionDataset.as_pascal_voc` 中 `approximation_percentage` 参数的默认值从 `0.75` 更改为 `0.0`。

### 0.7.0 <small>May 11, 2023</small>

- Added [#91](https://github.com/roboflow/supervision/pull/91): `Detections.from_yolo_nas` 以实现与 [YOLO-NAS](https://github.com/Deci-AI/super-gradients/blob/master/YOLONAS.md) 模型的无缝集成。
- Added [#86](https://github.com/roboflow/supervision/pull/86): 能够使用 `Dataset.from_yolo` 以 YOLO 格式加载数据集。
- Added [#84](https://github.com/roboflow/supervision/pull/84): `Detections.merge` 用于将多个 `Detections` 对象合并在一起。
- Fixed [#81](https://github.com/roboflow/supervision/pull/81): `LineZoneAnnotator.annotate` 未返回带注释的帧。
- Changed [#44](https://github.com/roboflow/supervision/pull/44): `LineZoneAnnotator.annotate` 以允许自定义进出标签文本。

### 0.6.0 <small>April 19, 2023</small>

- Added [#71](https://github.com/roboflow/supervision/pull/71): 初始 `Dataset` 支持以及将 `Detections` 保存为 Pascal VOC XML 格式的能力。
- Added [#71](https://github.com/roboflow/supervision/pull/71): 新的 `mask_to_polygons`, `filter_polygons_by_area`, `polygon_to_xyxy` 和 `approximate_polygon` 工具。
- Added [#72](https://github.com/roboflow/supervision/pull/72): 能够将 Pascal VOC XML **对象检测** 数据集加载为 `Dataset`。
- Changed [#70](https://github.com/roboflow/supervision/pull/70): `Detections` 属性的顺序，以使其与 `__iter__` 元组中的对象顺序一致。
- Changed [#71](https://github.com/roboflow/supervision/pull/71): `generate_2d_mask` 改为 `polygon_to_mask`。

### 0.5.2 <small>April 13, 2023</small>

- Fixed [#63](https://github.com/roboflow/supervision/pull/63): `LineZone.trigger` 函数期望 4 个值而不是 5 个。

### 0.5.1 <small>April 12, 2023</small>

- Fixed `Detections.__getitem__` 方法未返回选定项目的掩码。
- Fixed `Detections.area` 因掩码检测而崩溃。

### 0.5.0 <small>April 10, 2023</small>

- Added [#58](https://github.com/roboflow/supervision/pull/58): `Detections.mask` 以支持分割。
- Added [#58](https://github.com/roboflow/supervision/pull/58): `MaskAnnotator` 以允许轻松注释 `Detections.mask`。
- Added [#58](https://github.com/roboflow/supervision/pull/58): `Detections.from_sam` 以支持原生的 Segment Anything Model (SAM)。
- Changed [#58](https://github.com/roboflow/supervision/pull/58): `Detections.area` 的行为，使其不仅可以处理边界框，还可以处理掩码。

### 0.4.0 <small>April 5, 2023</small>

- Added [#46](https://github.com/roboflow/supervision/discussions/48): `Detections.empty` 以允许轻松创建空的 `Detections` 对象。
- Added [#56](https://github.com/roboflow/supervision/pull/56): `Detections.from_roboflow` 以允许从 Roboflow API 推理结果轻松创建 `Detections` 对象。
- Added [#56](https://github.com/roboflow/supervision/pull/56): `plot_images_grid` 以允许轻松地将多个图像绘制在单个图上。
- Added [#56](https://github.com/roboflow/supervision/pull/56): 使用 `detections_to_voc_xml` 方法对 Pascal VOC XML 格式的初始支持。
- Changed [#56](https://github.com/roboflow/supervision/pull/56): 将 `show_frame_in_notebook` 重构并重命名为 `plot_image`。

### 0.3.2 <small>March 23, 2023</small>

- Changed [#50](https://github.com/roboflow/supervision/issues/50): 允许 `Detections.class_id` 为 `None`。

### 0.3.1 <small>March 6, 2023</small>

- Fixed [#41](https://github.com/roboflow/supervision/issues/41): `PolygonZone` 在对象接触图像底部边缘时抛出异常。
- Fixed [#42](https://github.com/roboflow/supervision/issues/42): `Detections.wth_nms` 方法在 `Detections` 为空时抛出异常。
- Changed [#36](https://github.com/roboflow/supervision/pull/36): `Detections.wth_nms` 支持类别无关和非类别无关情况。

### 0.3.0 <small>March 6, 2023</small>

- Changed: 允许 `Detections.confidence` 为 `None`。
- Added: `Detections.from_transformers` 和 `Detections.from_detectron2` 以启用与 Transformers 和 Detectron2 模型的无缝集成。
- Added: `Detections.area` 以动态计算边界框面积。
- Added: `Detections.wth_nms` 以使用 NMS 过滤掉重复检测。初始实现为类别无关。

### 0.2.0 <small>Feb 2, 2023</small>

- Added: 高级的 `Detections` 过滤，使用类似 pandas 的 API。
- Added: `Detections.from_yolov5` 和 `Detections.from_yolov8` 以实现与 YOLOv5 和 YOLOv8 模型的无缝集成。

### 0.1.0 <small>Jan 19, 2023</small>

欢迎 Supervision 👋