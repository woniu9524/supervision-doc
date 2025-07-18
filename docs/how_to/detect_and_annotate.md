---
comments: true
---

# 检测与标注

Supervision 提供了一个无缝的流程，用于标注各种目标检测和分割模型生成的预测结果。本指南将展示如何使用 [Inference](https://github.com/roboflow/inference)、[Ultralytics](https://github.com/ultralytics/ultralytics) 或 [Transformers](https://github.com/huggingface/transformers) 包执行推理。之后，您将了解如何将这些预测结果导入 Supervision，并用于标注源图像。

![basic-annotation](https://media.roboflow.com/supervision_detect_and_annotate_example_1.png)

## 运行检测

首先，您需要从目标检测或分割模型中获取预测结果。

=== "Inference"
    ```python
    import cv2
    from inference import get_model

    model = get_model(model_id="yolov8n-640")
    image = cv2.imread(<SOURCE_IMAGE_PATH>)
    results = model.infer(image)[0]
    ```

=== "Ultralytics"
    ```python
    import cv2
    from ultralytics import YOLO

    model = YOLO("yolov8n.pt")
    image = cv2.imread(<SOURCE_IMAGE_PATH>)
    results = model(image)[0]
    ```

=== "Transformers"
    ```python
    import torch
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
    ```

## 将预测结果加载到 Supervision

现在我们已经从模型中获得了预测结果，可以将其加载到 Supervision 中。

=== "Inference"
    我们可以使用 [`sv.Detections.from_inference`](/latest/detection/core/#supervision.detection.core.Detections.from_inference) 方法，该方法同时接受来自检测和分割模型的模型结果。

    ```{ .py hl_lines="2 8" }
    import cv2
    import supervision as sv
    from inference import get_model

    model = get_model(model_id="yolov8n-640")
    image = cv2.imread(<SOURCE_IMAGE_PATH>)
    results = model.infer(image)[0]
    detections = sv.Detections.from_inference(results)
    ```

=== "Ultralytics"
    我们可以使用 [`sv.Detections.from_ultralytics`](/latest/detection/core/#supervision.detection.core.Detections.from_ultralytics) 方法，该方法同时接受来自检测和分割模型的模型结果。

    ```{ .py hl_lines="2 8" }
    import cv2
    import supervision as sv
    from ultralytics import YOLO

    model = YOLO("yolov8n.pt")
    image = cv2.imread(<SOURCE_IMAGE_PATH>)
    results = model(image)[0]
    detections = sv.Detections.from_ultralytics(results)
    ```

=== "Transformers"
    我们可以使用 [`sv.Detections.from_transformers`](/latest/detection/core/#supervision.detection.core.Detections.from_transformers) 方法，该方法同时接受来自检测和分割模型的模型结果。

    ```{ .py hl_lines="2 19-21" }
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

您可以使用以下方法从其他计算机视觉框架和库加载预测结果：

- [`from_deepsparse`](/latest/detection/core/#supervision.detection.core.Detections.from_deepsparse) ([Deepsparse](https://github.com/neuralmagic/deepsparse))
- [`from_detectron2`](/latest/detection/core/#supervision.detection.core.Detections.from_detectron2) ([Detectron2](https://github.com/facebookresearch/detectron2))
- [`from_mmdetection`](/latest/detection/core/#supervision.detection.core.Detections.from_mmdetection) ([MMDetection](https://github.com/open-mmlab/mmdetection))
- [`from_sam`](/latest/detection/core/#supervision.detection.core.Detections.from_sam) ([Segment Anything Model](https://github.com/facebookresearch/segment-anything))
- [`from_yolo_nas`](/latest/detection/core/#supervision.detection.core.Detections.from_yolo_nas) ([YOLO-NAS](https://github.com/Deci-AI/super-gradients/blob/master/YOLONAS.md))

## 使用检测结果标注图像

最后，我们可以用预测结果标注图像。由于我们使用的是目标检测模型，我们将使用 [`sv.BoxAnnotator`](/latest/detection/annotators/#supervision.annotators.core.BoxAnnotator) 和 [`sv.LabelAnnotator`](/latest/detection/annotators/#supervision.annotators.core.LabelAnnotator) 类。

=== "Inference"
    ```{ .py hl_lines="10-16" }
    import cv2
    import supervision as sv
    from inference import get_model

    model = get_model(model_id="yolov8n-640")
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
    ```{ .py hl_lines="10-16" }
    import cv2
    import supervision as sv
    from ultralytics import YOLO

    model = YOLO("yolov8n.pt")
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
    ```{ .py hl_lines="23-30" }
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

    box_annotator = sv.BoxAnnotator()
    label_annotator = sv.LabelAnnotator()

    annotated_image = box_annotator.annotate(
        scene=image, detections=detections)
    annotated_image = label_annotator.annotate(
        scene=annotated_image, detections=detections)
    ```

![basic-annotation](https://media.roboflow.com/supervision_detect_and_annotate_example_1.png)

## 显示自定义标签

默认情况下，[`sv.LabelAnnotator`](/latest/detection/annotators/#supervision.annotators.core.LabelAnnotator) 会使用 `class_name`（如果可能）或 `class_id` 来标记每个检测结果。您可以通过将自定义 `labels` 列表传递给 `annotate` 方法来覆盖此行为。

=== "Inference"
    ```{ .py hl_lines="13-17 22" }
    import cv2
    import supervision as sv
    from inference import get_model

    model = get_model(model_id="yolov8n-640")
    image = cv2.imread(<SOURCE_IMAGE_PATH>)
    results = model.infer(image)[0]
    detections = sv.Detections.from_inference(results)

    box_annotator = sv.BoxAnnotator()
    label_annotator = sv.LabelAnnotator()

    labels = [
        f"{class_name} {confidence:.2f}"
        for class_name, confidence
        in zip(detections['class_name'], detections.confidence)
    ]

    annotated_image = box_annotator.annotate(
        scene=image, detections=detections)
    annotated_image = label_annotator.annotate(
        scene=annotated_image, detections=detections, labels=labels)
    ```

=== "Ultralytics"
    ```{ .py hl_lines="13-17 22" }
    import cv2
    import supervision as sv
    from ultralytics import YOLO

    model = YOLO("yolov8n.pt")
    image = cv2.imread(<SOURCE_IMAGE_PATH>)
    results = model(image)[0]
    detections = sv.Detections.from_ultralytics(results)

    box_annotator = sv.BoxAnnotator()
    label_annotator = sv.LabelAnnotator()

    labels = [
        f"{class_name} {confidence:.2f}"
        for class_name, confidence
        in zip(detections['class_name'], detections.confidence)
    ]

    annotated_image = box_annotator.annotate(
        scene=image, detections=detections)
    annotated_image = label_annotator.annotate(
        scene=annotated_image, detections=detections, labels=labels)
    ```

=== "Transformers"
    ```{ .py hl_lines="26-30 35" }
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

    box_annotator = sv.BoxAnnotator()
    label_annotator = sv.LabelAnnotator()

    labels = [
        f"{class_name} {confidence:.2f}"
        for class_name, confidence
        in zip(detections['class_name'], detections.confidence)
    ]

    annotated_image = box_annotator.annotate(
        scene=image, detections=detections)
    annotated_image = label_annotator.annotate(
        scene=annotated_image, detections=detections, labels=labels)
    ```

![custom-label-annotation](https://media.roboflow.com/supervision_detect_and_annotate_example_2.png)

## 使用分割结果标注图像

如果您运行的是分割模型，[`sv.MaskAnnotator`](/latest/detection/annotators/#supervision.annotators.core.MaskAnnotator) 是 [`sv.BoxAnnotator`](/latest/detection/annotators/#supervision.annotators.core.BoxAnnotator) 的一个直接替代品，它允许您绘制掩码而不是边界框。

=== "Inference"
    ```python
    import cv2
    import supervision as sv
    from inference import get_model

    model = get_model(model_id="yolov8n-seg-640")
    image = cv2.imread(<SOURCE_IMAGE_PATH>)
    results = model.infer(image)[0]
    detections = sv.Detections.from_inference(results)

    mask_annotator = sv.MaskAnnotator()
    label_annotator = sv.LabelAnnotator(text_position=sv.Position.CENTER_OF_MASS)

    annotated_image = mask_annotator.annotate(
        scene=image, detections=detections)
    annotated_image = label_annotator.annotate(
        scene=annotated_image, detections=detections)
    ```

=== "Ultralytics"
    ```python
    import cv2
    import supervision as sv
    from ultralytics import YOLO

    model = YOLO("yolov8n-seg.pt")
    image = cv2.imread(<SOURCE_IMAGE_PATH>)
    results = model(image)[0]
    detections = sv.Detections.from_ultralytics(results)

    mask_annotator = sv.MaskAnnotator()
    label_annotator = sv.LabelAnnotator(text_position=sv.Position.CENTER_OF_MASS)

    annotated_image = mask_annotator.annotate(
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
    detections = sv.Detections.from_transformers(
        transformers_results=results,
        id2label=model.config.id2label)

    mask_annotator = sv.MaskAnnotator()
    label_annotator = sv.LabelAnnotator(text_position=sv.Position.CENTER_OF_MASS)

    labels = [
        f"{class_name} {confidence:.2f}"
        for class_name, confidence
        in zip(detections['class_name'], detections.confidence)
    ]

    annotated_image = mask_annotator.annotate(
        scene=image, detections=detections)
    annotated_image = label_annotator.annotate(
        scene=annotated_image, detections=detections, labels=labels)
    ```

![segmentation-annotation](https://media.roboflow.com/supervision_detect_and_annotate_example_3.png)