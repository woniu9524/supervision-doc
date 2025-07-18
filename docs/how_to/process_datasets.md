---
comments: true
---

使用 Supervision，您可以加载和操作分类、目标检测和分割数据集。本教程将指导您完成 Supervision 中数据集的加载、拆分、合并、可视化和增强。

## 下载数据集

在本教程中，我们将使用来自 [Roboflow Universe](https://universe.roboflow.com/) 的数据集，这是一个包含数千个计算机视觉数据集的公共存储库。如果您已经拥有 [COCO](https://roboflow.com/formats/coco-json)、[YOLO](https://roboflow.com/formats/yolov8-pytorch-txt) 或 [Pascal VOC](https://roboflow.com/formats/pascal-voc-xml) 格式的数据集，则可以跳过此部分。

```bash
pip install roboflow
```

接下来，登录您的 Roboflow 账户，并以 COCO、YOLO 或 Pascal VOC 格式下载您选择的数据集。您可以自定义以下代码片段，填入您的工作空间 ID、项目 ID 和版本号。

=== "COCO"

    ```python
    import roboflow

    roboflow.login()

    rf = roboflow.Roboflow()
    project = rf.workspace('<WORKSPACE_ID>').project('<PROJECT_ID>')
    dataset = project.version('<PROJECT_VERSION>').download("coco")
    ```

=== "YOLO"

    ```python
    import roboflow

    roboflow.login()

    rf = roboflow.Roboflow()
    project = rf.workspace('<WORKSPACE_ID>').project('<PROJECT_ID>')
    dataset = project.version('<PROJECT_VERSION>').download("yolov8")
    ```

=== "Pascal VOC"

    ```python
    import roboflow

    roboflow.login()

    rf = roboflow.Roboflow()
    project = rf.workspace('<WORKSPACE_ID>').project('<PROJECT_ID>')
    dataset = project.version('<PROJECT_VERSION>').download("voc")
    ```

## 加载数据集

Supervision 库提供了方便的函数来加载各种格式的数据集。如果您的数据集已经分割为训练集、测试集和验证集，您可以将它们分别加载为 [`sv.DetectionDataset`](https://supervision.roboflow.com/latest/datasets/core/#supervision.dataset.core.DetectionDataset) 实例。

=== "COCO"

    我们可以使用 [`sv.DetectionDataset.from_coco`](https://supervision.roboflow.com/latest/datasets/core/#supervision.dataset.core.DetectionDataset.from_coco) 来加载 [COCO](https://roboflow.com/formats/coco-json) 格式的标注。

    ```python
    import supervision as sv

    ds_train = sv.DetectionDataset.from_coco(
        images_directory_path=f'{dataset.location}/train',
        annotations_path=f'{dataset.location}/train/_annotations.coco.json',
    )
    ds_valid = sv.DetectionDataset.from_coco(
        images_directory_path=f'{dataset.location}/valid',
        annotations_path=f'{dataset.location}/valid/_annotations.coco.json',
    )
    ds_test = sv.DetectionDataset.from_coco(
        images_directory_path=f'{dataset.location}/test',
        annotations_path=f'{dataset.location}/test/_annotations.coco.json',
    )

    ds_train.classes
    # ['person', 'bicycle', 'car', ...]

    len(ds_train), len(ds_valid), len(ds_test)
    # 800, 100, 100
    ```

=== "YOLO"

    我们可以使用 [`sv.DetectionDataset.from_yolo`](https://supervision.roboflow.com/latest/datasets/core/#supervision.dataset.core.DetectionDataset.from_yolo) 来加载 [YOLO](https://roboflow.com/formats/yolov8-pytorch-txt) 格式的标注。

    ```python
    import supervision as sv

    ds_train = sv.DetectionDataset.from_yolo(
        images_directory_path=f'{dataset.location}/train/images',
        annotations_directory_path=f'{dataset.location}/train/labels',
        data_yaml_path=f'{dataset.location}/data.yaml'
    )
    ds_valid = sv.DetectionDataset.from_yolo(
        images_directory_path=f'{dataset.location}/valid/images',
        annotations_directory_path=f'{dataset.location}/valid/labels',
        data_yaml_path=f'{dataset.location}/data.yaml'
    )
    ds_test = sv.DetectionDataset.from_yolo(
        images_directory_path=f'{dataset.location}/test/images',
        annotations_directory_path=f'{dataset.location}/test/labels',
        data_yaml_path=f'{dataset.location}/data.yaml'
    )

    ds_train.classes
    # ['person', 'bicycle', 'car', ...]

    len(ds_train), len(ds_valid), len(ds_test)
    # 800, 100, 100
    ```

=== "Pascal VOC"

    我们可以使用 [`sv.DetectionDataset.from_pascal_voc`](https://supervision.roboflow.com/latest/datasets/core/#supervision.dataset.core.DetectionDataset.from_pascal_voc) 来加载 [Pascal VOC](https://roboflow.com/formats/pascal-voc-xml) 格式的标注。

    ```python
    import supervision as sv

    ds_train = sv.DetectionDataset.from_pascal_voc(
        images_directory_path=f'{dataset.location}/train/images',
        annotations_directory_path=f'{dataset.location}/train/labels'
    )
    ds_valid = sv.DetectionDataset.from_pascal_voc(
        images_directory_path=f'{dataset.location}/valid/images',
        annotations_directory_path=f'{dataset.location}/valid/labels'
    )
    ds_test = sv.DetectionDataset.from_pascal_voc(
        images_directory_path=f'{dataset.location}/test/images',
        annotations_directory_path=f'{dataset.location}/test/labels'
    )

    ds_train.classes
    # ['person', 'bicycle', 'car', ...]

    len(ds_train), len(ds_valid), len(ds_test)
    # 800, 100, 100
    ```

## 拆分数据集

如果您的数据集尚未分割为训练集、测试集和验证集，您可以使用 [`sv.DetectionDataset.split`](https://supervision.roboflow.com/latest/datasets/core/#supervision.dataset.core.DetectionDataset.split) 方法轻松完成。我们可以按如下方式进行分割，确保数据的随机打乱。

```python
import supervision as sv

ds = sv.DetectionDataset(...)

len(ds)
# 1000

ds_train, ds = ds.split(split_ratio=0.8, shuffle=True)
ds_valid, ds_test = ds.split(split_ratio=0.5, shuffle=True)

len(ds_train), len(ds_valid), len(ds_test)
# 800, 100, 100
```

## 合并数据集

如果您有多个数据集希望合并，可以使用 [`sv.DetectionDataset.merge`](https://supervision.roboflow.com/latest/datasets/core/#supervision.dataset.core.DetectionDataset.merge) 方法。

=== "COCO"

    ```{ .py hl_lines="22-28" }
    import supervision as sv

    ds_train = sv.DetectionDataset.from_coco(
        images_directory_path=f'{dataset.location}/train',
        annotations_path=f'{dataset.location}/train/_annotations.coco.json',
    )
    ds_valid = sv.DetectionDataset.from_coco(
        images_directory_path=f'{dataset.location}/valid',
        annotations_path=f'{dataset.location}/valid/_annotations.coco.json',
    )
    ds_test = sv.DetectionDataset.from_coco(
        images_directory_path=f'{dataset.location}/test',
        annotations_path=f'{dataset.location}/test/_annotations.coco.json',
    )

    ds_train.classes
    # ['person', 'bicycle', 'car', ...]

    len(ds_train), len(ds_valid), len(ds_test)
    # 800, 100, 100

    ds = sv.DetectionDataset.merge([ds_train, ds_valid, ds_test])

    ds.classes
    # ['person', 'bicycle', 'car', ...]

    len(ds)
    # 1000
    ```

=== "YOLO"

    ```{ .py hl_lines="25-31" }
    import supervision as sv

    ds_train = sv.DetectionDataset.from_yolo(
        images_directory_path=f'{dataset.location}/train/images',
        annotations_directory_path=f'{dataset.location}/train/labels',
        data_yaml_path=f'{dataset.location}/data.yaml'
    )
    ds_valid = sv.DetectionDataset.from_yolo(
        images_directory_path=f'{dataset.location}/valid/images',
        annotations_directory_path=f'{dataset.location}/valid/labels',
        data_yaml_path=f'{dataset.location}/data.yaml'
    )
    ds_test = sv.DetectionDataset.from_yolo(
        images_directory_path=f'{dataset.location}/test/images',
        annotations_directory_path=f'{dataset.location}/test/labels',
        data_yaml_path=f'{dataset.location}/data.yaml'
    )

    ds_train.classes
    # ['person', 'bicycle', 'car', ...]

    len(ds_train), len(ds_valid), len(ds_test)
    # 800, 100, 100

    ds = sv.DetectionDataset.merge([ds_train, ds_valid, ds_test])

    ds.classes
    # ['person', 'bicycle', 'car', ...]

    len(ds)
    # 1000
    ```

=== "Pascal VOC"

    ```{ .py hl_lines="22-28" }
    import supervision as sv

    ds_train = sv.DetectionDataset.from_pascal_voc(
        images_directory_path=f'{dataset.location}/train/images',
        annotations_directory_path=f'{dataset.location}/train/labels'
    )
    ds_valid = sv.DetectionDataset.from_pascal_voc(
        images_directory_path=f'{dataset.location}/valid/images',
        annotations_directory_path=f'{dataset.location}/valid/labels'
    )
    ds_test = sv.DetectionDataset.from_pascal_voc(
        images_directory_path=f'{dataset.location}/test/images',
        annotations_directory_path=f'{dataset.location}/test/labels'
    )

    ds_train.classes
    # ['person', 'bicycle', 'car', ...]

    len(ds_train), len(ds_valid), len(ds_test)
    # 800, 100, 100

    ds = sv.DetectionDataset.merge([ds_train, ds_valid, ds_test])

    ds.classes
    # ['person', 'bicycle', 'car', ...]

    len(ds)
    # 1000
    ```

## 遍历数据集

有两种方法可以遍历 `sv.DetectionDataset`：可以直接对 `sv.DetectionDataset` 实例调用 [for 循环](https://supervision.roboflow.com/latest/datasets/core/#supervision.dataset.core.DetectionDataset.__iter__)，或者按 [索引](https://supervision.roboflow.com/latest/datasets/core/#supervision.dataset.core.DetectionDataset.__getitem__) 加载 `sv.DetectionDataset` 条目。

```python
import supervision as sv

ds = sv.DetectionDataset(...)

# 方式一
for image_path, image, annotations in ds:
    ... # 处理每张图片及其标注

# 方式二
for idx in range(len(ds)):
    image_path, image, annotations = ds[idx]
    ... # 处理索引 `idx` 处的图片和标注
```

## 可视化数据集

Supervision 库提供了方便的工具来可视化您的检测数据集。您可以创建标注图像的网格，以便快速检查您的数据和标签。首先，初始化 [`sv.BoxAnnotator`](https://supervision.roboflow.com/latest/detection/annotators/#supervision.annotators.core.BoxAnnotator) 和 [`sv.LabelAnnotator`](https://supervision.roboflow.com/latest/detection/annotators/#supervision.annotators.core.LabelAnnotator)。然后，遍历数据集的一个子集（例如，前 25 张图片），在每张图片上绘制边界框和类别标签。最后，将标注后的图像合并成一个网格进行显示。

```python
import supervision as sv

ds = sv.DetectionDataset(...)

box_annotator = sv.BoxAnnotator()
label_annotator = sv.LabelAnnotator()

annotated_images = []
for i in range(16):
    _, image, annotations = ds[i]

    labels = [ds.classes[class_id] for class_id in annotations.class_id]

    annotated_image = image.copy()
    annotated_image = box_annotator.annotate(annotated_image, annotations)
    annotated_image = label_annotator.annotate(annotated_image, annotations, labels)
    annotated_images.append(annotated_image)

grid = sv.create_tiles(
    annotated_images,
    grid_size=(4, 4),
    single_tile_size=(400, 400),
    tile_padding_color=sv.Color.WHITE,
    tile_margin_color=sv.Color.WHITE
)
```

![visualize-dataset](https://media.roboflow.com/supervision-docs/visualize-dataset.png)

## 保存数据集

=== "COCO"

    我们可以使用 [`sv.DetectionDataset.as_coco`](https://supervision.roboflow.com/datasets/#supervision.dataset.core.DetectionDataset.as_coco) 方法将标注保存为 [COCO](https://roboflow.com/formats/coco-json) 格式。

    ```python
    import supervision as sv

    ds = sv.DetectionDataset(...)

    ds.as_coco(
        images_directory_path='<IMAGE_DIRECTORY_PATH>',
        annotations_path='<ANNOTATIONS_PATH>'
    )
    ```

=== "YOLO"

    我们可以使用 [`sv.DetectionDataset.as_yolo`](https://supervision.roboflow.com/datasets/#supervision.dataset.core.DetectionDataset.as_yolo) 方法将标注保存为 [YOLO](https://roboflow.com/formats/yolov8-pytorch-txt) 格式。

    ```python
    import supervision as sv

    ds = sv.DetectionDataset(...)

    ds.as_yolo(
        images_directory_path='<IMAGE_DIRECTORY_PATH>',
        annotations_directory_path='<ANNOTATIONS_DIRECTORY_PATH>',
        data_yaml_path='<DATA_YAML_PATH>'
    )
    ```

=== "Pascal VOC"

    我们可以使用 [`sv.DetectionDataset.as_pascal_voc`](https://supervision.roboflow.com/datasets/#supervision.dataset.core.DetectionDataset.as_pascal_voc) 方法将标注保存为 [Pascal VOC](https://roboflow.com/formats/pascal-voc-xml) 格式。

    ```python
    import supervision as sv

    ds = sv.DetectionDataset(...)

    ds.as_pascal_voc(
        images_directory_path='<IMAGE_DIRECTORY_PATH>',
        annotations_directory_path='<ANNOTATIONS_DIRECTORY_PATH>'
    )
    ```

## 增强数据集

在本节中，我们将探讨如何结合使用 Supervision 和 Albumentations 来增强我们的数据集。数据增强是计算机视觉中的一项常用技术，用于增加训练数据集的大小和多样性，从而提高模型的性能和泛化能力。

```bash
pip install augmentation
```

Albumentations 提供了一个灵活而强大的图像增强 API。该库的核心是 [`Compose`](https://albumentations.ai/docs/api_reference/full_reference/?h=compose#albumentations.core.composition.Compose) 类，它允许您将多个图像变换链接在一起。每个变换都使用专用类进行定义，例如 [`HorizontalFlip`](https://albumentations.ai/docs/api_reference/full_reference/?h=horizontalflip0#albumentations.augmentations.geometric.transforms.HorizontalFlip)、[`RandomBrightnessContrast`](https://albumentations.ai/docs/api_reference/full_reference/?h=horizontalflip0#albumentations.augmentations.transforms.RandomBrightnessContrast) 或 [`Perspective`](https://albumentations.ai/docs/api_reference/full_reference/?h=horizontalflip0#albumentations.augmentations.geometric.transforms.Perspective)。

```python
import albumentations as A

augmentation = A.Compose(
    transforms=[
        A.Perspective(p=0.1),
        A.HorizontalFlip(p=0.5),
        A.RandomBrightnessContrast(p=0.5)
    ],
    bbox_params=A.BboxParams(
        format='pascal_voc',
        label_fields=['category']
    ),
)
```

关键在于设置 `format='pascal_voc'`，这对应于 Supervision 中使用的 `[x_min, y_min, x_max, y_max]` 边界框格式。

```python
import numpy as np
import supervision as sv
from dataclasses import replace

ds = sv.DetectionDataset(...)

_, original_image, original_annotations = ds[0]

output = augmentation(
    image=original_image,
    bboxes=original_annotations.xyxy,
    category=original_annotations.class_id
)

augmented_image = output['image']
augmented_annotations = replace(
    original_annotations,
    xyxy=np.array(output['bboxes']),
    class_id=np.array(output['category'])
)
```

![augment-dataset](https://media.roboflow.com/supervision-docs/augment-dataset.png)