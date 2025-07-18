# 速度估算

[![Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/roboflow-ai/notebooks/blob/main/notebooks/how-to-estimate-vehicle-speed-with-computer-vision.ipynb)
[![YouTube](https://badges.aleen42.com/src/youtube.svg)](https://youtu.be/uWP6UjDeZvY)

## 👋 你好

本示例使用各种对象检测模型和 ByteTrack（一种简单而有效的在线多对象跟踪方法）来进行速度估算分析。它使用 supervision 包来执行跟踪、标注等多种任务。

https://github.com/roboflow/supervision/assets/26109316/d50118c1-2ae4-458d-915a-5d860fd36f71

> \[!IMPORTANT\]
> 如果您计划在视频文件上运行速度估算脚本，请调整 [`SOURCE`](https://github.com/roboflow/supervision/blob/e32b05a636dab2ea1f39299e529c4b22b8baa8da/examples/speed_estimation/ultralytics_example.py#L10) 和 [`TARGET`](https://github.com/roboflow/supervision/blob/e32b05a636dab2ea1f39299e529c4b22b8baa8da/examples/speed_estimation/ultralytics_example.py#L15) 配置。对于每个摄像机视图，这些都需要单独调整。您可以从我们的 YouTube [教程](https://youtu.be/uWP6UjDeZvY) 中了解更多信息。

## 💻 安装

- 克隆仓库并导航至示例目录

    ```bash
    git clone --depth 1 -b develop https://github.com/roboflow/supervision.git
    cd supervision/examples/speed_estimation
    ```

- 设置 Python 环境并激活它 \[可选\]

    ```bash
    python3.10 -m venv venv
    source venv/bin/activate
    ```

- 安装所需的依赖项

    ```bash
    pip install -r requirements.txt
    ```

- 下载 `vehicles.mp4` 文件

    ```bash
    python3.10 video_downloader.py
    ```

## 🛠️ 脚本参数

- `--roboflow_api_key`（可选）：Roboflow 服务的 API 密钥。如果未直接提供，脚本会尝试从 `ROBOFLOW_API_KEY` 环境变量中获取。请遵循 [此指南](https://docs.roboflow.com/api-reference/authentication#retrieve-an-api-key) 来获取您的 `API KEY`。

- `--model_id`（可选）：指定要使用的 Roboflow 模型 ID。默认值为 `"yolov8x-1280"`。

- `--source_weights_path`：必需。指定 YOLO 模型权重文件的路径，这对于对象检测过程至关重要。此文件包含模型用于识别视频中对象的数据。

- `--source_video_path`：必需。要分析的源视频文件的路径。这是将执行交通流量分析的输入视频。

- `--target_video_path`：用于保存带标注的输出视频的路径。如果未指定，则处理后的视频将实时显示，而不会保存。

- `--confidence_threshold`（可选）：设置 YOLO 模型的置信度阈值以过滤检测结果。默认为 `0.3`。这决定了模型在识别视频中的对象时应有多大的置信度。

- `--iou_threshold`（可选）：指定模型的 IOU（Intersection Over Union）阈值。默认为 0.7。此值用于管理对象检测的准确性，尤其是在区分不同对象时。

## ⚙️ 运行

- yolo-nas

    ```bash
    python yolo_nas_example.py \
        --source_video_path data/vehicles.mp4 \
        --target_video_path data/vehicles-result.mp4 \
        --confidence_threshold 0.3 \
        --iou_threshold 0.5
    ```

- inference

    ```bash
    python inference_example.py \
        --roboflow_api_key <ROBOFLOW API KEY> \
        --source_video_path data/vehicles.mp4 \
        --target_video_path data/vehicles-result.mp4 \
        --confidence_threshold 0.3 \
        --iou_threshold 0.5
    ```

- ultralytics

    ```bash
    python ultralytics_example.py \
        --source_video_path data/vehicles.mp4 \
        --target_video_path data/vehicles-result.mp4 \
        --confidence_threshold 0.3 \
        --iou_threshold 0.5
    ```

## © 许可

此演示集成了两个主要组件，每个组件都有其自身的许可：

- ultralytics：此演示中使用的对象检测模型 YOLOv8 是根据 [AGPL-3.0 许可证](https://github.com/ultralytics/ultralytics/blob/main/LICENSE) 分发的。您可以在此处找到有关此许可证的更多详细信息。

- supervision：为演示中的基于区域的分析提供支持的分析代码基于 Supervision 库，该库根据 [MIT 许可证](https://github.com/roboflow/supervision/blob/develop/LICENSE.md) 进行许可。这使得 Supervision 部分的代码完全开源，并且可以在您的项目中使用。