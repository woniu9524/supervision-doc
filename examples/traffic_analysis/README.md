# 交通流量分析

## 👋 你好

此脚本使用 YOLOv8（一种目标检测方法）和 ByteTrack（一种简单而有效的在线多目标跟踪方法）执行交通流量分析。
它使用 supervision 包来执行跟踪、注释等多种任务。

https://github.com/roboflow/supervision/assets/26109316/c9436828-9fbf-4c25-ae8c-60e9c81b3900

## 💻 安装

- 克隆仓库并进入示例目录

    ```bash
    git clone --depth 1 -b develop https://github.com/roboflow/supervision.git
    cd supervision/examples/traffic_analysis
    ```

- 设置 python 环境并激活它 \[可选\]

    ```bash
    python3 -m venv venv
    source venv/bin/activate
    ```

- 安装所需的依赖

    ```bash
    pip install -r requirements.txt
    ```

- 下载 `traffic_analysis.pt` 和 `traffic_analysis.mov` 文件

    ```bash
    ./setup.sh
    ```

## 🛠️ 脚本参数

- ultralytics

    - `--source_weights_path`: 必需。指定 YOLO 模型权重文件的路径，这对于目标检测过程至关重要。此文件包含模型用于识别视频中对象的的数据。

    - `--source_video_path`: 必需。要分析的源视频文件的路径。这是将执行交通流量分析的输入视频。

    - `--target_video_path` (可选): 保存带注释的输出视频的路径。如果未指定，处理后的视频将实时显示而不保存。

    - `--confidence_threshold` (可选): 设置 YOLO 模型的置信度阈值以过滤检测结果。默认为 `0.3`。这决定了模型在识别视频中的对象时应有多大的置信度。

    - `--iou_threshold` (可选): 指定模型的 IOU（交并比）阈值。默认为 0.7。此值用于管理目标检测的准确性，尤其是在区分不同对象时。

- inference

    - `--roboflow_api_key` (可选): Roboflow 服务的 API 密钥。如果未直接提供，脚本会尝试从 `ROBOFLOW_API_KEY` 环境变量中获取。请遵循 [此指南](https://docs.roboflow.com/api-reference/authentication#retrieve-an-api-key) 获取您的 `API KEY`。

    - `--model_id` (可选): 指定要使用的 Roboflow 模型 ID。默认值为 `"vehicle-count-in-drone-video/6"`。

    - `--source_video_path`: 必需。要分析的源视频文件的路径。这是将执行交通流量分析的输入视频。

    - `--target_video_path` (可选): 保存带注释的输出视频的路径。如果未指定，处理后的视频将实时显示而不保存。

    - `--confidence_threshold` (可选): 设置 YOLO 模型的置信度阈值以过滤检测结果。默认为 `0.3`。这决定了模型在识别视频中的对象时应有多大的置信度。

    - `--iou_threshold` (可选): 指定模型的 IOU（交并比）阈值。默认为 0.7。此值用于管理目标检测的准确性，尤其是在区分不同对象时。

## ⚙️ 运行

- ultralytics

    ```bash
    python ultralytics_example.py \
        --source_weights_path data/traffic_analysis.pt \
        --source_video_path data/traffic_analysis.mov \
        --confidence_threshold 0.3 \
        --iou_threshold 0.5 \
        --target_video_path data/traffic_analysis_result.mov
    ```

- inference

    ```bash
    python inference_example.py \
        --roboflow_api_key <ROBOFLOW API KEY> \
        --source_video_path data/traffic_analysis.mov \
        --confidence_threshold 0.3 \
        --iou_threshold 0.5 \
        --target_video_path data/traffic_analysis_result.mov
    ```

## © 许可

此演示集成了两个主要组件，每个组件都有其自己的许可：

- ultralytics: 此演示中使用的目标检测模型 YOLOv8 在 [AGPL-3.0 许可](https://github.com/ultralytics/ultralytics/blob/main/LICENSE) 下分发。您可以在此处找到有关此许可的更多详细信息。

- supervision: 为此演示中的基于区域的分析提供支持的分析代码基于 Supervision 库，该库是在 [MIT 许可](https://github.com/roboflow/supervision/blob/develop/LICENSE.md) 下许可的。这使得 Supervision 部分的代码完全开源，并且可以在您的项目中免费使用。