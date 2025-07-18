# 区域内时长

[![YouTube](https://badges.aleen42.com/src/youtube.svg)](https://www.youtube.com/watch?v=hAWpsIuem10)

## 👋 你好

演示如何利用计算机视觉分析等待时间，以及跟踪物体或个人在视频帧预定义区域内停留的时长。这个示例项目非常适用于零售分析或交通管理等应用。

https://github.com/roboflow/supervision/assets/26109316/d051cc8a-dd15-41d4-aa36-d38b86334c39

## 💻 安装

- 克隆仓库并进入示例目录

    ```bash
    git clone --depth 1 -b develop https://github.com/roboflow/supervision.git
    cd supervision/examples/time_in_zone
    ```

- 设置 Python 环境并激活它（可选）

    ```bash
    python3 -m venv venv
    source venv/bin/activate
    ```

- 安装所需依赖

    ```bash
    pip install -r requirements.txt
    ```

## 🛠 脚本

### `download_from_youtube`

此脚本允许您从 YouTube 下载视频。

- `--url`: 您想下载的 YouTube 视频的完整 URL。
- `--output_path` (可选): 指定保存视频的目录。
- `--file_name` (可选): 设置保存的视频文件名。

```bash
python scripts/download_from_youtube.py \
    --url "https://www.youtube.com/watch?v=-8zyEwAa50Q" \
    --output_path "data/checkout" \
    --file_name "video.mp4"
```

```bash
python scripts/download_from_youtube.py \
    --url "https://www.youtube.com/watch?v=MNn9qKG2UFI" \
    --output_path "data/traffic" \
    --file_name "video.mp4"
```

### `stream_from_file`

此脚本允许您从目录流式传输视频文件。这是模拟本地测试的实时视频流的绝佳方式。视频将以循环方式流式传输到 `rtsp://localhost:8554/live0.stream` URL。此脚本需要安装 Docker。

- `--video_directory`: 包含要流式传输的视频文件的目录。
- `--number_of_streams`: 要流式传输的视频文件数量。

```bash
python scripts/stream_from_file.py \
    --video_directory "data/checkout" \
    --number_of_streams 1
```

```bash
python scripts/stream_from_file.py \
    --video_directory "data/traffic" \
    --number_of_streams 1
```

### `draw_zones`

如果您想在自己的视频上测试区域内时长分析，可以使用此脚本设计自定义区域并将结果保存为 JSON 文件。脚本将打开一个窗口，您可以在其中绘制多边形到源图像或视频文件上。这些多边形将保存为 JSON 文件。

- `--source_path`: 用于绘制多边形的源图像或视频文件的路径。

- `--zone_configuration_path`: 要将多边形标注保存为 JSON 文件的路径。

- `enter` - 完成当前多边形的绘制。

- `escape` - 取消当前多边形的绘制。

- `q` - 退出绘制窗口。

- `s` - 将区域配置保存到 JSON 文件。

```bash
python scripts/draw_zones.py \
    --source_path "data/checkout/video.mp4" \
    --zone_configuration_path "data/checkout/config.json"
```

```bash
python scripts/draw_zones.py \
    --source_path "data/traffic/video.mp4" \
    --zone_configuration_path "data/traffic/config.json"
```

https://github.com/roboflow/supervision/assets/26109316/9d514c9e-2a61-418b-ae49-6ac1ad6ae5ac

## 🎬 视频和流处理

### `inference_file_example`

使用 Roboflow Inference 模型对视频文件运行对象检测的脚本。

- `--zone_configuration_path`: 区域配置 JSON 文件的路径。
- `--source_video_path`: 源视频文件的路径。
- `--model_id`: Roboflow 模型 ID。
- `--classes`: 要跟踪的类别 ID 列表。如果为空，则跟踪所有类别。
- `--confidence_threshold`: 检测的置信度级别（`0` 到 `1`）。默认为 `0.3`。
- `--iou_threshold`: 非极大值抑制 (non-max suppression) 的 IOU 阈值。默认为 `0.7`。

```bash
python inference_file_example.py \
    --zone_configuration_path "data/checkout/config.json" \
    --source_video_path "data/checkout/video.mp4" \
    --model_id "yolov8x-640" \
    --classes 0 \
    --confidence_threshold 0.3 \
    --iou_threshold 0.7
```

https://github.com/roboflow/supervision/assets/26109316/d051cc8a-dd15-41d4-aa36-d38b86334c39

```bash
python inference_file_example.py \
    --zone_configuration_path "data/traffic/config.json" \
    --source_video_path "data/traffic/video.mp4" \
    --model_id "yolov8x-640" \
    --classes 2 5 6 7 \
    --confidence_threshold 0.3 \
    --iou_threshold 0.7
```

https://github.com/roboflow/supervision/assets/26109316/5ec896d7-4b39-4426-8979-11e71666878b

### `inference_stream_example`

使用 Roboflow Inference 模型对视频流运行对象检测的脚本。

- `--zone_configuration_path`: 区域配置 JSON 文件的路径。
- `--rtsp_url`: 视频流的完整 RTSP URL。
- `--model_id`: Roboflow 模型 ID。
- `--classes`: 要跟踪的类别 ID 列表。如果为空，则跟踪所有类别。
- `--confidence_threshold`: 检测的置信度级别（`0` 到 `1`）。默认为 `0.3`。
- `--iou_threshold`: 非极大值抑制 (non-max suppression) 的 IOU 阈值。默认为 `0.7`。

```bash
python inference_stream_example.py \
    --zone_configuration_path "data/checkout/config.json" \
    --rtsp_url "rtsp://localhost:8554/live0.stream" \
    --model_id "yolov8x-640" \
    --classes 0 \
    --confidence_threshold 0.3 \
    --iou_threshold 0.7
```

```bash
python inference_stream_example.py \
    --zone_configuration_path "data/traffic/config.json" \
    --rtsp_url "rtsp://localhost:8554/live0.stream" \
    --model_id "yolov8x-640" \
    --classes 2 5 6 7 \
    --confidence_threshold 0.3 \
    --iou_threshold 0.7
```

<details>
<summary>👉 显示 ultralytics 示例</summary>

### `ultralytics_file_example`

使用 Ultralytics YOLOv8 模型对视频文件运行对象检测的脚本。

- `--zone_configuration_path`: 区域配置 JSON 文件的路径。
- `--source_video_path`: 源视频文件的路径。
- `--weights`: 模型权重文件的路径。默认为 `'yolov8s.pt'`。
- `--device`: 计算设备（`'cpu'`, `'mps'` 或 `'cuda'`）。默认为 `'cpu'`。
- `--classes`: 要跟踪的类别 ID 列表。如果为空，则跟踪所有类别。
- `--confidence_threshold`: 检测的置信度级别（`0` 到 `1`）。默认为 `0.3`。
- `--iou_threshold`: 非极大值抑制 (non-max suppression) 的 IOU 阈值。默认为 `0.7`。

```bash
python ultralytics_file_example.py \
    --zone_configuration_path "data/checkout/config.json" \
    --source_video_path "data/checkout/video.mp4" \
    --weights "yolov8x.pt" \
    --device "cpu" \
    --classes 0 \
    --confidence_threshold 0.3 \
    --iou_threshold 0.7
```

```bash
python ultralytics_file_example.py \
    --zone_configuration_path "data/traffic/config.json" \
    --source_video_path "data/traffic/video.mp4" \
    --weights "yolov8x.pt" \
    --device "cpu" \
    --classes 2 5 6 7 \
    --confidence_threshold 0.3 \
    --iou_threshold 0.7
```

### `ultralytics_stream_example`

使用 Ultralytics YOLOv8模型对视频流运行对象检测的脚本。

- `--zone_configuration_path`: 区域配置 JSON 文件的路径。
- `--rtsp_url`: 视频流的完整 RTSP URL。
- `--weights`: 模型权重文件的路径。默认为 `'yolov8s.pt'`。
- `--device`: 计算设备（`'cpu'`, `'mps'` 或 `'cuda'`）。默认为 `'cpu'`。
- `--classes`: 要跟踪的类别 ID 列表。如果为空，则跟踪所有类别。
- `--confidence_threshold`: 检测的置信度级别（`0` 到 `1`）。默认为 `0.3`。
- `--iou_threshold`: 非极大值抑制 (non-max suppression) 的 IOU 阈值。默认为 `0.7`。

```bash
python ultralytics_stream_example.py \
    --zone_configuration_path "data/checkout/config.json" \
    --rtsp_url "rtsp://localhost:8554/live0.stream" \
    --weights "yolov8x.pt" \
    --device "cpu" \
    --classes 0 \
    --confidence_threshold 0.3 \
    --iou_threshold 0.7
```

```bash
python ultralytics_stream_example.py \
    --zone_configuration_path "data/traffic/config.json" \
    --rtsp_url "rtsp://localhost:8554/live0.stream" \
    --weights "yolov8x.pt" \
    --device "cpu" \
    --classes 2 5 6 7 \
    --confidence_threshold 0.3 \
    --iou_threshold 0.7
```

</details>

## © 许可

此演示集成了两个主要组件，每个组件都有自己的许可：

- ultralytics：此演示中使用的对象检测模型 YOLOv8，根据 [AGPL-3.0 许可](https://github.com/ultralytics/ultralytics/blob/main/LICENSE) 分发。您可以在此处找到有关此许可的更多详细信息。

- supervision：为本次演示中的基于区域的分析提供支持的分析代码基于 Supervision 库，该库根据 [MIT 许可](https://github.com/roboflow/supervision/blob/develop/LICENSE.md) 进行许可。这使得 Supervision 部分的代码完全开源，可在您的项目中免费使用。