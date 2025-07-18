---
comments: true
status: deprecated
---

# 已弃用

这些功能已被淘汰，原因是存在更好的替代方案或在未来版本中可能出现问题。已弃用功能的有效期为**五个后续版本**，以便用户有时间过渡到更新的方法。


- `InferenceSlicer.__init__` 中的 `overlap_filter_strategy` 在 [`InferenceSlicer.__init__`](https://supervision.roboflow.com/latest/detection/tools/inference_slicer/) 已弃用，并将在 `supervision-0.27.0` 中移除。请使用 `overlap_strategy`。
- `InferenceSlicer.__init__` 中的 `overlap_ratio_wh` 在 [`InferenceSlicer.__init__`](https://supervision.roboflow.com/latest/detection/tools/inference_slicer/) 已弃用，并将在 `supervision-0.27.0` 中移除。请使用 `overlap_wh`。
- `sv.LMM` 枚举已弃用，并将在 `supervision-0.31.0` 中移除。请使用 `sv.VLM`。
- [`sv.Detections.from_lmm`](https://supervision.roboflow.com/0.26.0/detection/core/#supervision.detection.core.Detections.from_lmm) 属性已弃用，并将在 `supervision-0.31.0` 中移除。请使用 [`sv.Detections.from_vlm`](https://supervision.roboflow.com/0.26.0/detection/core/#supervision.detection.core.Detections.from_vlm)。

# 已移除

### 0.26.0

- `supervision-0.26.0` 中的 `sv.DetectionDataset.images` 属性已被移除。请使用 `for path, image, annotation in dataset:` 循环遍历图像，这样无需将所有图像加载到内存中。此外，使用 `images` 参数（类型为 `Dict[str, np.ndarray]`）构建 `sv.DetectionDataset` 已弃用并在 `supervision-0.26.0` 中被移除。请改用路径列表 `List[str]`。
- `sv.BoundingBoxAnnotator` 的名称已弃用并在 `supervision-0.26.0` 中被移除。它已重命名为 [`sv.BoxAnnotator`](https://supervision.roboflow.com/0.22.0/detection/annotators/#supervision.annotators.core.BoxAnnotator)。


### 0.24.0

- [`sv.PolygonZone`](detection/tools/polygon_zone.md/#supervision.detection.tools.polygon_zone.PolygonZone) 中的 `frame_resolution_wh ` 参数已被移除。
- Supervision 的安装方法 `"headless"` 和 `"desktop"` 已移除，因为它们不再需要。`pip install supervision[headless]` 将安装基础库，并发出关于不存在的额外组件的无害警告。

### 0.23.0

- [`ByteTrack`](trackers.md/#supervision.tracker.byte_tracker.core.ByteTrack) 中的 `track_buffer`、`track_thresh` 和 `match_thresh` 参数已弃用，并自 `supervision-0.23.0` 起移除。请使用 `lost_track_buffer`、`track_activation_threshold` 和 `minimum_matching_threshold`。
- [`sv.PolygonZone`](detection/tools/polygon_zone.md/#supervision.detection.tools.polygon_zone.PolygonZone) 中的 `triggering_position ` 参数已自 `supervision-0.23.0` 起移除。请使用 `triggering_anchors`。

### 0.22.0

- `supervision-0.22.0` 起已移除 `sv.Detections.from_roboflow`。请使用 [`Detections.from_inference`](detection/core.md/#supervision.detection.core.Detections.from_inference)。
- `supervision-0.22.0` 起已移除 `sv.Color.white()` 方法。请使用常量 `sv.Color.WHITE`。
- `supervision-0.22.0` 起已移除 `sv.Color.black()` 方法。请使用常量 `sv.Color.BLACK`。
- `supervision-0.22.0` 起已移除 `sv.Color.red()` 方法。请使用常量 `sv.Color.RED`。
- `supervision-0.22.0` 起已移除 `sv.Color.green()` 方法。请使用常量 `sv.Color.GREEN`。
- `supervision-0.22.0` 起已移除 `sv.Color.blue()` 方法。请使用常量 `sv.Color.BLUE`。
- `supervision-0.22.0` 起已移除 `sv.ColorPalette.default()` 方法。请使用常量 [`ColorPalette.DEFAULT`](/utils/draw/#supervision.draw.color.ColorPalette.DEFAULT)。
- `supervision-0.22.0` 时已移除 `sv.BoxAnnotator`，但 `sv.BoundingBoxAnnotator` 已立即更名为 `sv.BoxAnnotator`。请使用 [`BoxAnnotator`](detection/annotators.md/#supervision.annotators.core.BoxAnnotator) 和 [`LabelAnnotator`](detection/annotators.md/#supervision.annotators.core.LabelAnnotator) 替换旧的 `sv.BoxAnnotator`。
- `supervision-0.22.0` 起已移除 `sv.FPSMonitor.__call__` 方法。请使用属性 [`sv.FPSMonitor.fps`](utils/video.md/#supervision.utils.video.FPSMonitor.fps)。