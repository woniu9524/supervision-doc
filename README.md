<div align="center">
  <p>
    <a align="center" href="" target="https://supervision.roboflow.com">
      <img
        width="100%"
        src="https://media.roboflow.com/open-source/supervision/rf-supervision-banner.png?updatedAt=1678995927529"
      >
    </a>
  </p>

<br>

[notebooks](https://github.com/roboflow/notebooks) | [inference](https://github.com/roboflow/inference) | [autodistill](https://github.com/autodistill/autodistill) | [maestro](https://github.com/roboflow/multimodal-maestro)

<br>

[![version](https://badge.fury.io/py/supervision.svg)](https://badge.fury.io/py/supervision)
[![downloads](https://img.shields.io/pypi/dm/supervision)](https://pypistats.org/packages/supervision)
[![snyk](https://snyk.io/advisor/python/supervision/badge.svg)](https://snyk.io/advisor/python/supervision)
[![license](https://img.shields.io/pypi/l/supervision)](https://github.com/roboflow/supervision/blob/main/LICENSE.md)
[![python-version](https://img.shields.io/pypi/pyversions/supervision)](https://badge.fury.io/py/supervision)
[![colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/roboflow/supervision/blob/main/demo.ipynb)
[![gradio](https://img.shields.io/badge/%F0%9F%A4%97%20Hugging%20Face-Spaces-blue)](https://huggingface.co/spaces/Roboflow/Annotators)
[![discord](https://img.shields.io/discord/1159501506232451173?logo=discord&label=discord&labelColor=fff&color=5865f2&link=https%3A%2F%2Fdiscord.gg%2FGbfgXGJ8Bk)](https://discord.gg/GbfgXGJ8Bk)
[![built-with-material-for-mkdocs](https://img.shields.io/badge/Material_for_MkDocs-526CFE?logo=MaterialForMkDocs&logoColor=white)](https://squidfunk.github.io/mkdocs-material/)

  <div align="center">
    <a href="https://trendshift.io/repositories/124"  target="_blank"><img src="https://trendshift.io/api/badge/repositories/124" alt="roboflow%2Fsupervision | Trendshift" style="width: 250px; height: 55px;" width="250" height="55"/></a>
  </div>

</div>

## 👋 你好

**我们为你编写可复用的计算机视觉工具。** 无论是需要从硬盘加载数据集、在图像或视频上绘制检测框，还是计算区域内的检测数量。你都可以信赖我们！🤝

## 💻 安装

在 [**Python>=3.9**](https://www.python.org/) 环境中，通过 pip 安装 supervision 包。

```bash
pip install supervision
```

在我们的[指南](https://roboflow.github.io/supervision/)中可以找到关于 conda、mamba 以及从源码安装的更多信息。

## 🔥 快速开始

### 模型

Supervision 的设计目标是模型无关。只需接入任何分类、检测或分割模型。为了方便你使用，我们为 Ultralytics、Transformers 或 MMDetection 等主流库创建了[连接器](https://supervision.roboflow.com/latest/detection/core/#detections)。

```python
import cv2
import supervision as sv
from ultralytics import YOLO

image = cv2.imread(...)
model = YOLO("yolov8s.pt")
result = model(image)[0]
detections = sv.Detections.from_ultralytics(result)

len(detections)
# 5
```

<details>
<summary>👉 更多模型连接器</summary>

- inference

  使用 [Inference](https://github.com/roboflow/inference) 需要一个 [Roboflow API KEY](https://docs.roboflow.com/api-reference/authentication#retrieve-an-api-key)。

  ```python
  import cv2
  import supervision as sv
  from inference import get_model

  image = cv2.imread(...)
  model = get_model(model_id="yolov8s-640", api_key=<ROBOFLOW API KEY>)
  result = model.infer(image)[0]
  detections = sv.Detections.from_inference(result)

  len(detections)
  # 5
  ```

</details>

### 标注器 (Annotators)

Supervision 提供了广泛且高度可定制的[标注器](https://supervision.roboflow.com/latest/detection/annotators/)，可让你组合出最适合你用例的可视化效果。

```python
import cv2
import supervision as sv

image = cv2.imread(...)
detections = sv.Detections(...)

box_annotator = sv.BoxAnnotator()
annotated_frame = box_annotator.annotate(
  scene=image.copy(),
  detections=detections)
```

https://github.com/roboflow/supervision/assets/26109316/691e219c-0565-4403-9218-ab5644f39bce

### 数据集

Supervision 提供了一套[工具](https://supervision.roboflow.com/latest/datasets/core/)，可让你以支持的格式之一加载、拆分、合并和保存数据集。

```python
import supervision as sv
from roboflow import Roboflow

project = Roboflow().workspace(<WORKSPACE_ID>).project(<PROJECT_ID>)
dataset = project.version(<PROJECT_VERSION>).download("coco")

ds = sv.DetectionDataset.from_coco(
    images_directory_path=f"{dataset.location}/train",
    annotations_path=f"{dataset.location}/train/_annotations.coco.json",
)

path, image, annotation = ds[0]
    # 按需加载图像

for path, image, annotation in ds:
    # 按需加载图像
```

<details close>
<summary>👉 更多数据集工具</summary>

- 加载

  ```python
  dataset = sv.DetectionDataset.from_yolo(
      images_directory_path=...,
      annotations_directory_path=...,
      data_yaml_path=...
  )

  dataset = sv.DetectionDataset.from_pascal_voc(
      images_directory_path=...,
      annotations_directory_path=...
  )

  dataset = sv.DetectionDataset.from_coco(
      images_directory_path=...,
      annotations_path=...
  )
  ```

- 拆分

  ```python
  train_dataset, test_dataset = dataset.split(split_ratio=0.7)
  test_dataset, valid_dataset = test_dataset.split(split_ratio=0.5)

  len(train_dataset), len(test_dataset), len(valid_dataset)
  # (700, 150, 150)
  ```

- 合并

  ```python
  ds_1 = sv.DetectionDataset(...)
  len(ds_1)
  # 100
  ds_1.classes
  # ['dog', 'person']

  ds_2 = sv.DetectionDataset(...)
  len(ds_2)
  # 200
  ds_2.classes
  # ['cat']

  ds_merged = sv.DetectionDataset.merge([ds_1, ds_2])
  len(ds_merged)
  # 300
  ds_merged.classes
  # ['cat', 'dog', 'person']
  ```

- 保存

  ```python
  dataset.as_yolo(
      images_directory_path=...,
      annotations_directory_path=...,
      data_yaml_path=...
  )

  dataset.as_pascal_voc(
      images_directory_path=...,
      annotations_directory_path=...
  )

  dataset.as_coco(
      images_directory_path=...,
      annotations_path=...
  )
  ```

- 转换

  ```python
  sv.DetectionDataset.from_yolo(
      images_directory_path=...,
      annotations_directory_path=...,
      data_yaml_path=...
  ).as_pascal_voc(
      images_directory_path=...,
      annotations_directory_path=...
  )
  ```

</details>

## 🎬 教程

想学习如何使用 Supervision？请浏览我们的[操作指南](https://supervision.roboflow.com/develop/how_to/detect_and_annotate/)、[端到端示例](https://github.com/roboflow/supervision/tree/develop/examples)、[速查表](https://roboflow.github.io/cheatsheet-supervision/)和[操作手册](https://supervision.roboflow.com/develop/cookbooks/)！

<br/>

<p align="left">
<a href="https://youtu.be/hAWpsIuem10" title="Dwell Time Analysis with Computer Vision | Real-Time Stream Processing"><img src="https://github.com/SkalskiP/SkalskiP/assets/26109316/a742823d-c158-407d-b30f-063a5d11b4e1" alt="Dwell Time Analysis with Computer Vision | Real-Time Stream Processing" width="300px" align="left" /></a>
<a href="https://youtu.be/hAWpsIuem10" title="Dwell Time Analysis with Computer Vision | Real-Time Stream Processing"><strong>基于计算机视觉的停留时间分析 | 实时流处理</strong></a>
<div><strong>创建时间: 2024 年 4 月 5 日</strong></div>
<br/>了解如何使用计算机视觉分析等待时间并优化流程。本教程涵盖对象检测、跟踪以及计算指定区域内的检测数量。使用这些技术可以在零售、交通管理或其他场景中改善客户体验。</p>

<br/>

<p align="left">
<a href="https://youtu.be/uWP6UjDeZvY" title="Speed Estimation & Vehicle Tracking | Computer Vision | Open Source"><img src="https://github.com/SkalskiP/SkalskiP/assets/26109316/61a444c8-b135-48ce-b979-2a5ab47c5a91" alt="Speed Estimation & Vehicle Tracking | Computer Vision | Open Source" width="300px" align="left" /></a>
<a href="https://youtu.be/uWP6UjDeZvY" title="Speed Estimation & Vehicle Tracking | Computer Vision | Open Source"><strong>速度估算与车辆跟踪 | 计算机视觉 | 开源</strong></a>
<div><strong>创建时间: 2024 年 1 月 11 日</strong></div>
<br/>学习如何使用 YOLO、ByteTrack 和 Roboflow Inference 进行车辆跟踪和速度估算。本综合教程涵盖对象检测、多对象跟踪、检测过滤、透视变换、速度估算、可视化改进等内容。</p>

## 💜 使用 supervision 构建

您是否使用 supervision 构建了很棒的东西？[告诉我们！](https://github.com/roboflow/supervision/discussions/categories/built-with-supervision)

https://user-images.githubusercontent.com/26109316/207858600-ee862b22-0353-440b-ad85-caa0c4777904.mp4

https://github.com/roboflow/supervision/assets/26109316/c9436828-9fbf-4c25-ae8c-60e9c81b3900

https://github.com/roboflow/supervision/assets/26109316/3ac6982f-4943-4108-9b7f-51787ef1a69f

## 📚 文档

访问我们的[文档](https://roboflow.github.io/supervision)页面，了解 supervision 如何帮助您更快、更可靠地构建计算机视觉应用程序。

## 🏆 贡献

我们非常乐意获得您的反馈！请参阅我们的[贡献指南](https://github.com/roboflow/supervision/blob/main/CONTRIBUTING.md)以开始。感谢所有贡献者！🙏

<p align="center">
    <a href="https://github.com/roboflow/supervision/graphs/contributors">
      <img src="https://contrib.rocks/image?repo=roboflow/supervision" />
    </a>
</p>

<br>

<div align="center">

<div align="center">
      <a href="https://youtube.com/roboflow">
          <img
            src="https://media.roboflow.com/notebooks/template/icons/purple/youtube.png?ik-sdk-version=javascript-1.4.3&updatedAt=1672949634652"
            width="3%"
          />
      </a>
      <img src="https://raw.githubusercontent.com/ultralytics/assets/main/social/logo-transparent.png" width="3%"/>
      <a href="https://roboflow.com">
          <img
            src="https://media.roboflow.com/notebooks/template/icons/purple/roboflow-app.png?ik-sdk-version=javascript-1.4.3&updatedAt=1672949746649"
            width="3%"
          />
      </a>
      <img src="https://raw.githubusercontent.com/ultralytics/assets/main/social/logo-transparent.png" width="3%"/>
      <a href="https://www.linkedin.com/company/roboflow-ai/">
          <img
            src="https://media.roboflow.com/notebooks/template/icons/purple/linkedin.png?ik-sdk-version=javascript-1.4.3&updatedAt=1672949633691"
            width="3%"
          />
      </a>
      <img src="https://raw.githubusercontent.com/ultralytics/assets/main/social/logo-transparent.png" width="3%"/>
      <a href="https://docs.roboflow.com">
          <img
            src="https://media.roboflow.com/notebooks/template/icons/purple/knowledge.png?ik-sdk-version=javascript-1.4.3&updatedAt=1672949634511"
            width="3%"
          />
      </a>
      <img src="https://raw.githubusercontent.com/ultralytics/assets/main/social/logo-transparent.png" width="3%"/>
      <a href="https://discuss.roboflow.com">
          <img
            src="https://media.roboflow.com/notebooks/template/icons/purple/forum.png?ik-sdk-version=javascript-1.4.3&updatedAt=1672949633584"
            width="3%"
          />
      <img src="https://raw.githubusercontent.com/ultralytics/assets/main/social/logo-transparent.png" width="3%"/>
      <a href="https://blog.roboflow.com">
          <img
            src="https://media.roboflow.com/notebooks/template/icons/purple/blog.png?ik-sdk-version=javascript-1.4.3&updatedAt=1672949633605"
            width="3%"
          />
      </a>
      </a>
  </div>
</div>