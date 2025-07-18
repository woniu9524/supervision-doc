---
comments: true
---

# 检测小目标

本指南将展示如何使用 [`InferenceSlicer`](/latest/detection/tools/inference_slicer/#supervision.detection.tools.inference_slicer.InferenceSlicer)
在 [`Inference`](https://github.com/roboflow/inference),
[`Ultralytics`](https://github.com/ultralytics/ultralytics) 或
[`Transformers`](https://github.com/huggingface/transformers) 包中检测小目标。

<video controls>
    <source src="https://media.roboflow.com/supervision_detect_small_objects_example.mp4" type="video/mp4">
</video>

## 基线检测

高分辨率图像中的小目标检测由于目标相对于图像分辨率的大小而带来挑战。

=== "Inference"
    ```python
    import cv2
    import supervision as sv
    from inference import get_model

    model = get_model(model_id="yolov8x-640")
    image = cv2.imread(<SOURCE_IMAGE_PATH>)
    results = model.infer(image)[0]
    detections = sv.Detections.from_inference(results)

    box_annotator = sv.BoxAnnotator()
    label_annotator = sv.LabelAnnotator()

    annotated_image = box_annotator.annotate(
        scene=image, detections=detections)
    annotated_image = label_annotator.annotate(
        scene=annotated_image, detections=detections)
    ```

=== "Ultralytics"
    ```python
    import cv2
    import supervision as sv
    from ultralytics import YOLO

    model = YOLO("yolov8x.pt")
    image = cv2.imread(<SOURCE_IMAGE_PATH>)
    results = model(image)[0]
    detections = sv.Detections.from_ultralytics(results)

    box_annotator = sv.BoxAnnotator()
    label_annotator = sv.LabelAnnotator()

    annotated_image = box_annotator.annotate(
        scene=image, detections=detections)
    annotated_image = label_annotator.annotate(
        scene=annotated_image, detections=detections)
    ```

=== "Transformers"
    ```python
    import torch
    import supervision as sv
    from PIL import Image
    from transformers import DetrImageProcessor, DetrForSegmentation

    processor = DetrImageProcessor.from_pretrained("facebook/detr-resnet-50")
    model = DetrForSegmentation.from_pretrained("facebook/detr-resnet-50")

    image = Image.open(<SOURCE_IMAGE_PATH>)
    inputs = processor(images=image, return_tensors="pt")

    with torch.no_grad():
        outputs = model(**inputs)

    width, height = image_slice.size
    target_size = torch.tensor([[width, height]])
    results = processor.post_process_object_detection(
        outputs=outputs, target_sizes=target_size)[0]
    detections = sv.Detections.from_transformers(results)

    box_annotator = sv.BoxAnnotator()
    label_annotator = sv.LabelAnnotator()

    labels = [
        model.config.id2label[class_id]
        for class_id
        in detections.class_id
    ]

    annotated_image = box_annotator.annotate(
        scene=image, detections=detections)
    annotated_image = label_annotator.annotate(
        scene=annotated_image, detections=detections, labels=labels)
    ```

![basic-detection](https://media.roboflow.com/supervision_detect_small_objects_example_1.png)

## 输入分辨率

在检测前修改图像的输入分辨率可以提高小目标的识别能力，但会以牺牲处理速度和增加内存使用为代价。对于超高分辨率图像（4K 及以上），此方法效果较差。

=== "Inference"
    ```{ .py hl_lines="5" }
    import cv2
    import supervision as sv
    from inference import get_model

    model = get_model(model_id="yolov8x-1280")
    image = cv2.imread(<SOURCE_IMAGE_PATH>)
    results = model.infer(image)[0]
    detections = sv.Detections.from_inference(results)

    box_annotator = sv.BoxAnnotator()
    label_annotator = sv.LabelAnnotator()

    annotated_image = box_annotator.annotate(
        scene=image, detections=detections)
    annotated_image = label_annotator.annotate(
        scene=annotated_image, detections=detections)
    ```

=== "Ultralytics"
    ```{ .py hl_lines="7" }
    import cv2
    import supervision as sv
    from ultralytics import YOLO

    model = YOLO("yolov8x.pt")
    image = cv2.imread(<SOURCE_IMAGE_PATH>)
    results = model(image, imgsz=1280)[0]
    detections = sv.Detections.from_ultralytics(results)

    box_annotator = sv.BoxAnnotator()
    label_annotator = sv.LabelAnnotator()

    annotated_image = box_annotator.annotate(
        scene=image, detections=detections)
    annotated_image = label_annotator.annotate(
        scene=annotated_image, detections=detections)
    ```

![detection-with-high-input-resolution](https://media.roboflow.com/supervision_detect_small_objects_example_2.png)

## Inference Slicer

[`InferenceSlicer`](/latest/detection/tools/inference_slicer/#supervision.detection.tools.inference_slicer.InferenceSlicer)
通过将高分辨率图像分割成更小的块、对每个块进行检测然后聚合结果来处理高分辨率图像。

<video controls>
    <source src="https://media.roboflow.com/supervision_detect_small_objects_example_2.mp4" type="video/mp4">
</video>

=== "Inference"
    ```{ .py hl_lines="9-14" }
    import cv2
    import numpy as np
    import supervision as sv
    from inference import get_model

    model = get_model(model_id="yolov8x-640")
    image = cv2.imread(<SOURCE_IMAGE_PATH>)

    def callback(image_slice: np.ndarray) -> sv.Detections:
        results = model.infer(image_slice)[0]
        return sv.Detections.from_inference(results)

    slicer = sv.InferenceSlicer(callback = callback)
    detections = slicer(image)

    box_annotator = sv.BoxAnnotator()
    label_annotator = sv.LabelAnnotator()

    annotated_image = box_annotator.annotate(
        scene=image, detections=detections)
    annotated_image = label_annotator.annotate(
        scene=annotated_image, detections=detections)
    ```

=== "Ultralytics"
    ```{ .py hl_lines="9-14" }
    import cv2
    import numpy as np
    import supervision as sv
    from ultralytics import YOLO

    model = YOLO("yolov8x.pt")
    image = cv2.imread(<SOURCE_IMAGE_PATH>)

    def callback(image_slice: np.ndarray) -> sv.Detections:
        result = model(image_slice)[0]
        return sv.Detections.from_ultralytics(result)

    slicer = sv.InferenceSlicer(callback = callback)
    detections = slicer(image)

    box_annotator = sv.BoxAnnotator()
    label_annotator = sv.LabelAnnotator()

    annotated_image = box_annotator.annotate(
        scene=image, detections=detections)
    annotated_image = label_annotator.annotate(
        scene=annotated_image, detections=detections)
    ```

=== "Transformers"
    ```{ .py hl_lines="13-28" }
    import cv2
    import torch
    import numpy as np
    import supervision as sv
    from PIL import Image
    from transformers import DetrImageProcessor, DetrForObjectDetection

    processor = DetrImageProcessor.from_pretrained("facebook/detr-resnet-50")
    model = DetrForObjectDetection.from_pretrained("facebook/detr-resnet-50")

    image = cv2.imread(<SOURCE_IMAGE_PATH>)

    def callback(image_slice: np.ndarray) -> sv.Detections:
        image_slice = cv2.cvtColor(image_slice, cv2.COLOR_BGR2RGB)
        image_slice = Image.fromarray(image_slice)
        inputs = processor(images=image_slice, return_tensors="pt")

        with torch.no_grad():
            outputs = model(**inputs)

        width, height = image_slice.size
        target_size = torch.tensor([[width, height]])
        results = processor.post_process_object_detection(
            outputs=outputs, target_sizes=target_size)[0]
        return sv.Detections.from_transformers(results)

    slicer = sv.InferenceSlicer(callback = callback)
    detections = slicer(image)

    box_annotator = sv.BoxAnnotator()
    label_annotator = sv.LabelAnnotator()

    labels = [
        model.config.id2label[class_id]
        for class_id
        in detections.class_id
    ]

    annotated_image = box_annotator.annotate(
        scene=image, detections=detections)
    annotated_image = label_annotator.annotate(
        scene=annotated_image, detections=detections, labels=labels)
    ```

![detection-with-inference-slicer](https://media.roboflow.com/supervision_detect_small_objects_example_3.png)

## 小目标分割

[`InferenceSlicer`](/latest/detection/tools/inference_slicer/#supervision.detection.tools.inference_slicer.InferenceSlicer) 也可以执行分割任务。

=== "Inference"
    ```{ .py hl_lines="6 16 19-20" }
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

=== "Ultralytics"
    ```{ .py hl_lines="6 16 19-20" }
    import cv2
    import numpy as np
    import supervision as sv
    from ultralytics import YOLO

    model = YOLO("yolov8x-seg.pt")
    image = cv2.imread(<SOURCE_IMAGE_PATH>)

    def callback(image_slice: np.ndarray) -> sv.Detections:
        result = model(image_slice)[0]
        return sv.Detections.from_ultralytics(result)

    slicer = sv.InferenceSlicer(callback = callback)
    detections = slicer(image)

    mask_annotator = sv.MaskAnnotator()
    label_annotator = sv.LabelAnnotator()

    annotated_image = mask_annotator.annotate(
        scene=image, detections=detections)
    annotated_image = label_annotator.annotate(
        scene=annotated_image, detections=detections)
    ```

![detection-with-inference-slicer](https://media.roboflow.com/supervision-docs/inference-slicer-segmentation-example.png)