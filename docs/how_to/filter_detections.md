---
comments: true
---

# 筛选检测结果

`Detections` 类提供的先进筛选功能，使您能够高效地缩小和优化目标检测结果。本节将介绍各种筛选方法，包括按特定类别或一组类别、置信度、目标面积、边界框面积、相对面积、框尺寸以及指定区域进行筛选。每种方法都配有简洁的代码示例，以帮助用户清晰地了解如何在应用中实现这些筛选器。

### 按特定类别筛选

允许您仅选择属于一个已选类别的检测结果。

=== "筛选后"
    ```python
    import supervision as sv

    detections = sv.Detections(...)
    detections = detections[detections.class_id == 0]
    ```

    <div class="result" markdown>

    ![by-specific-class](https://media.roboflow.com/open-source/supervision/supervision-detection-by-specific-class.png){ align=center width="800" }

    </div>

=== "筛选前"
    ```python
    import supervision as sv

    detections = sv.Detections(...)
    detections = detections[detections.class_id == 0]
    ```

    <div class="result" markdown>

    ![original](https://media.roboflow.com/open-source/supervision/supervision-detection-original.png){ align=center width="800" }

    </div>

### 按类别集合筛选

允许您仅选择属于已选类别集合的检测结果。

=== "筛选后"
    ```python
    import numpy as np
    import supervision as sv

    selected_classes = [0, 2, 3]
    detections = sv.Detections(...)
    detections = detections[np.isin(detections.class_id, selected_classes)]
    ```

    <div class="result" markdown>

    ![by-set-of-classes](https://media.roboflow.com/open-source/supervision/supervision-detection-by-set-of-classes.png){ align=center width="800" }

    </div>

=== "筛选前"
    ```python
    import numpy as np
    import supervision as sv

    class_id = [0, 2, 3]
    detections = sv.Detections(...)
    detections = detections[np.isin(detections.class_id, class_id)]
    ```

    <div class="result" markdown>

    ![original](https://media.roboflow.com/open-source/supervision/supervision-detection-original.png){ align=center width="800" }

    </div>

### 按置信度筛选

允许您选择具有特定置信度的检测结果，例如高于选定阈值的检测结果。

=== "筛选后"
    ```python
    import supervision as sv

    detections = sv.Detections(...)
    detections = detections[detections.confidence > 0.5]
    ```

    <div class="result" markdown>

    ![by-set-of-classes](https://media.roboflow.com/open-source/supervision/supervision-detection-by-confidence.png){ align=center width="800" }

    </div>

=== "筛选前"
    ```python
    import supervision as sv

    detections = sv.Detections(...)
    detections = detections[detections.confidence > 0.5]
    ```

    <div class="result" markdown>

    ![original](https://media.roboflow.com/open-source/supervision/supervision-detection-original.png){ align=center width="800" }

    </div>

### 按面积筛选

允许您根据检测结果的大小进行选择。我们将面积定义为检测结果在图像中占据的像素数量。在下面的示例中，我们筛选掉了过小的检测结果。

=== "筛选后"
    ```python
    import supervision as sv

    detections = sv.Detections(...)
    detections = detections[detections.area > 1000]
    ```

    <div class="result" markdown>

    ![by-area](https://media.roboflow.com/open-source/supervision/supervision-detection-by-area.png){ align=center width="800" }

    </div>

=== "筛选前"
    ```python
    import supervision as sv

    detections = sv.Detections(...)
    detections = detections[detections.area > 1000]
    ```

    <div class="result" markdown>

    ![original](https://media.roboflow.com/open-source/supervision/supervision-detection-original.png){ align=center width="800" }

    </div>

### 按相对面积筛选

允许您根据检测结果相对于整个图像大小的比例来选择。有时，检测结果的大小概念会因图像而异。在 1280x720 图像上占据 10000 像素的检测结果可能很大，但在 3840x2160 图像上可能很小。在这种情况下，我们可以根据检测结果占据图像面积的百分比进行筛选。在下面的示例中，我们移除了过大的检测结果。

=== "筛选后"
    ```python
    import supervision as sv

    image = ...
    height, width, channels = image.shape
    image_area = height * width

    detections = sv.Detections(...)
    detections = detections[(detections.area / image_area) < 0.8]
    ```

    <div class="result" markdown>

    ![by-relative-area](https://media.roboflow.com/open-source/supervision/supervision-detection-by-relative-area.png?updatedAt=1683207183434){ align=center width="800" }

    </div>

=== "筛选前"
    ```python
    import supervision as sv

    image = ...
    height, width, channels = image.shape
    image_area = height * width

    detections = sv.Detections(...)
    detections = detections[(detections.area / image_area) < 0.8]
    ```

    <div class="result" markdown>

    ![original](https://media.roboflow.com/open-source/supervision/supervision-detection-original.png){ align=center width="800" }

    </div>

### 按边界框尺寸筛选

允许您根据检测结果的尺寸进行选择。边界框的大小以及其坐标都可以作为拒绝检测标准的依据。实现此类筛选需要一些自定义代码，但相对简单快捷。

=== "筛选后"
    ```python
    import supervision as sv

    detections = sv.Detections(...)
    w = detections.xyxy[:, 2] - detections.xyxy[:, 0]
    h = detections.xyxy[:, 3] - detections.xyxy[:, 1]
    detections = detections[(w > 200) & (h > 200)]
    ```

    <div class="result" markdown>

    ![by-box-dimensions](https://media.roboflow.com/open-source/supervision/supervision-detection-by-box-dimensions.png){ align=center width="800" }

    </div>

=== "筛选前"
    ```python
    import supervision as sv

    detections = sv.Detections(...)
    w = detections.xyxy[:, 2] - detections.xyxy[:, 0]
    h = detections.xyxy[:, 3] - detections.xyxy[:, 1]
    detections = detections[(w > 200) & (h > 200)]
    ```

    <div class="result" markdown>

    ![original](https://media.roboflow.com/open-source/supervision/supervision-detection-original.png){ align=center width="800" }

    </div>

### 按 `PolygonZone` 筛选

允许您将 `Detections` 与 `PolygonZone` 结合使用，以剔除区域内外的边界框。在下面的示例中，您可以看到如何滤除图像下半部分的所有检测结果。

=== "筛选后"
    ```python
    import supervision as sv

    zone = sv.PolygonZone(...)
    detections = sv.Detections(...)
    mask = zone.trigger(detections=detections)
    detections = detections[mask]
    ```

    <div class="result" markdown>

    ![by-polygon-zone](https://media.roboflow.com/open-source/supervision/supervision-detection-by-polygon-zone.png?updatedAt=1683211380445){ align=center width="800" }

    </div>

=== "筛选前"
    ```python
    import supervision as sv

    zone = sv.PolygonZone(...)
    detections = sv.Detections(...)
    mask = zone.trigger(detections=detections)
    detections = detections[mask]
    ```

    <div class="result" markdown>

    ![original](https://media.roboflow.com/open-source/supervision/supervision-detection-original.png){ align=center width="800" }

    </div>

### 按混合条件筛选

然而，`Detections` 最强大的地方在于，你可以通过简单地使用 `&` 或 `|` 组合单独的条件，来构建任意复杂的逻辑条件。

=== "筛选后"
    ```python
    import supervision as sv

    zone = sv.PolygonZone(...)
    detections = sv.Detections(...)
    mask = zone.trigger(detections=detections)
    detections = detections[(detections.confidence > 0.7) & mask]
    ```

    <div class="result" markdown>

    ![by-mixed-conditions](https://media.roboflow.com/open-source/supervision/supervision-detection-by-mixed-conditions.png){ align=center width="800" }

    </div>

=== "筛选前"
    ```python
    import supervision as sv

    zone = sv.PolygonZone(...)
    detections = sv.Detections(...)
    mask = zone.trigger(detections=detections)
    detections = detections[mask]
    ```

    <div class="result" markdown>

    ![original](https://media.roboflow.com/open-source/supervision/supervision-detection-original.png){ align=center width="800" }

    </div>