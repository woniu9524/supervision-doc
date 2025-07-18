# 统计区域内人数

[![Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/roboflow-ai/notebooks/blob/main/notebooks/how-to-detect-and-count-objects-in-polygon-zone.ipynb)
[![YouTube](https://badges.aleen42.com/src/youtube.svg)](https://www.youtube.com/watch?v=l_kf9CfZ_8M)

## 👋 你好

本演示是一个视频分析工具，用于统计和高亮显示视频中特定区域内的物体。每个区域及其中的物体都会用不同的颜色进行标记，方便查看和统计每个区域内的物体数量。该工具可以保存处理后的视频，或将处理结果实时显示在屏幕上。

https://github.com/roboflow/supervision/assets/26109316/f84db7b5-79e2-4142-a1da-64daa43ce667

## 💻 安装

- 克隆仓库并进入示例目录

    ```bash
    git clone --depth 1 -b develop https://github.com/roboflow/supervision.git
    cd supervision/examples/count_people_in_zone
    ```

- 设置 Python 环境并激活 \[可选]

    ```bash
    python3 -m venv venv
    source venv/bin/activate
    ```

- 安装必要的依赖项

    ```bash
    pip install -r requirements.txt
    ```

- 下载 `traffic_analysis.pt` 和 `traffic_analysis.mov` 文件

    ```bash
    ./setup.sh
    ```

## 🛠️ 脚本参数

- ultralytics

    - `--source_weights_path` (可选): YOLO 模型权重文件的路径。
        如果未指定，默认为 `"yolov8x.pt"`。

    - `--zone_configuration_path`: 指定包含区域配置的 JSON 文件的路径。
        此文件定义了将在视频中进行计数的区域边界。

    - `--source_video_path`: 要分析的源视频文件的路径。

    - `--target_video_path` (可选): 保存带标注的输出视频的路径。
        如果未提供，处理后的视频将实时显示。

    - `--confidence_threshold` (可选): 设置 YOLO 模型的置信度阈值以过滤检测结果。
        默认为 `0.3`。

    - `--iou_threshold` (可选): 指定模型的 IOU (Intersection Over Union) 阈值。
        默认为 `0.7`。

- inference

    - `--roboflow_api_key` (可选): Roboflow 服务的 API 密钥。
        如果未直接提供，脚本会尝试从 `ROBOFLOW_API_KEY` 环境变量中获取。
        请按照[此指南](https://docs.roboflow.com/api-reference/authentication#retrieve-an-api-key)获取您的 `API KEY`。

    - `--model_id` (可选): 指定要使用的 Roboflow 模型 ID。
        默认值为 `"yolov8x-1280"`。

    - `--zone_configuration_path`: 指定包含区域配置的 JSON 文件的路径。
        此文件定义了将在视频中进行计数的区域边界。

    - `--source_video_path`: 要分析的源视频文件的路径。

    - `--target_video_path` (可选): 保存带标注的输出视频的路径。
        如果未提供，处理后的视频将实时显示。

    - `--confidence_threshold` (可选): 设置 YOLO 模型的置信度阈值以过滤检测结果。
        默认为 `0.3`。

    - `--iou_threshold` (可选): 指定模型的 IOU (Intersection Over Union) 阈值。
        默认为 `0.7`。

## 📌 区域配置

- `horizontal-zone-config.json`: 定义了横跨画面分割的区域。
- `multi-zone-config.json`: 配置了具有自定义形状和位置的多个区域。
- `quarters-zone-config.json`: 将画面分割成四个相等的区域。
- `vertical-zone-config.json`: 将画面分割成宽度相等的垂直区域。

## ⚙️ 运行示例

- ultralytics

    ```bash
    python ultralytics_example.py \
        --zone_configuration_path data/multi-zone-config.json \
        --source_video_path data/market-square.mp4 \
        --confidence_threshold 0.3 \
        --iou_threshold 0.5
    ```

- inference

    ```bash
    python inference_example.py \
        --roboflow_api_key <ROBOFLOW API KEY> \
        --zone_configuration_path data/multi-zone-config.json \
        --source_video_path data/market-square.mp4 \
        --confidence_threshold 0.3 \
        --iou_threshold 0.5
    ```

## © 版权

此演示集成了两个主要组件，每个组件都有自己的许可：

- ultralytics: 此演示中使用的目标检测模型 YOLOv8，根据
    [AGPL-3.0 许可证](https://github.com/ultralytics/ultralytics/blob/main/LICENSE)分发。
    您可以在此处找到有关此许可证的更多详细信息。

- supervision: 为此演示中的基于区域的分析提供动力的分析代码，基于 Supervision 库，
    该库根据
    [MIT 许可证](https://github.com/roboflow/supervision/blob/develop/LICENSE.md)获得许可。
    这使得 Supervision 部分的代码完全开源，可以在您的项目中免费使用。