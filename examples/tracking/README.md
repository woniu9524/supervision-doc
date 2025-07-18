# 目标跟踪

## 👋 你好

该脚本提供了使用 YOLOv8 进行目标检测，以及使用 Supervision 进行跟踪和标注来处理视频的功能。

## 💻 安装

- 克隆仓库并进入示例目录

    ```bash
    git clone --depth 1 -b develop https://github.com/roboflow/supervision.git
    cd supervision/examples/tracking
    ```

- 设置 Python 环境并激活它 \[可选\]

    ```bash
    python3 -m venv venv
    source venv/bin/activate
    ```

- 安装所需的依赖项

    ```bash
    pip install -r requirements.txt
    ```

## 🛠️ 脚本参数

- ultralytics

    - `--source_weights_path`: 必需。指定 YOLO 模型的权重文件路径，这对于目标检测过程至关重要。此文件包含模型用于识别视频中对象的的数据。

    - `--source_video_path`: 必需。要处理的源视频文件的路径。这是将执行对象检测和标注的视频。

    - `--target_video_path`: 必需。处理后添加了标注的视频将要保存的路径。这是你的输出视频文件。

    - `--confidence_threshold` (可选): 设置模型在视频中识别对象的置信度级别。默认为 `0.3`。较高的阈值会使模型更具选择性，而较低的阈值会使其在识别对象时更具包容性。

    - `--iou_threshold` (可选): 指定模型的 IOU（交并比）阈值，默认为 `0.7`。此参数有助于区分不同的对象，尤其是在拥挤的场景中。

- 推理（inference）

    - `--roboflow_api_key` (可选): Roboflow 服务的 API 密钥。如果未直接提供，脚本会尝试从 `ROBOFLOW_API_KEY` 环境变量中获取。请遵循 [此指南](https://docs.roboflow.com/api-reference/authentication#retrieve-an-api-key) 来获取你的 `API KEY`。

    - `--model_id` (可选): 指定要使用的 Roboflow 模型 ID。默认值为 `"yolov8x-1280"`。

    - `--source_video_path`: 必需。要处理的源视频文件的路径。这是将执行对象检测和标注的视频。

    - `--target_video_path`: 必需。处理后添加了标注的视频将要保存的路径。这是你的输出视频文件。

    - `--confidence_threshold` (可选): 设置模型在视频中识别对象的置信度级别。默认为 `0.3`。较高的阈值会使模型更具选择性，而较低的阈值会使其在识别对象时更具包容性。

    - `--iou_threshold` (可选): 指定模型的 IOU（交并比）阈值，默认为 `0.7`。此参数有助于区分不同的对象，尤其是在拥挤的场景中。

## ⚙️ 运行

- 推理（inference）

    ```bash
    python inference_example.py \
        --roboflow_api_key <ROBOFLOW API KEY> \
        --source_video_path input.mp4 \
        --target_video_path tracking_result.mp4
    ```

- ultralytics

    ```bash
    python ultralytics_example.py \
        --source_weights_path yolov8s.pt \
        --source_video_path input.mp4 \
        --target_video_path tracking_result.mp4
    ```

## © 许可

此演示集成了两个主要组件，每个组件都有自己的许可：

- ultralytics：此演示中使用的对象检测模型 YOLOv8，是在 [AGPL-3.0 许可](https://github.com/ultralytics/ultralytics/blob/main/LICENSE) 下分发的。你可以在此处找到有关此许可的更多详细信息。

- supervision：为演示中的基于区域的分析提供支持的分析代码基于 Supervision 库，该库是在 [MIT 许可](https://github.com/roboflow/supervision/blob/develop/LICENSE.md) 下许可的。这使得 Supervision 部分的代码完全开源，并且可以在你的项目中免费使用。