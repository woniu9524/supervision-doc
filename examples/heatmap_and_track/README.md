# 热力图（heatmap）与追踪（tracking）

## 👋 你好

该脚本利用 YOLOv8（一种目标检测方法）和 ByteTrack（一种简单但有效的在线多目标追踪方法）来进行热力图和追踪分析。它使用了 `supervision` 包来执行多项任务，例如绘制热力图标注、追踪目标等。

## 💻 安装

- 克隆仓库并导航到示例目录

    ```bash
    git clone --depth 1 -b develop https://github.com/roboflow/supervision.git
    cd supervision/examples/heatmap_and_track
    ```

- 设置 Python 环境并激活它（可选）

    ```bash
    python3 -m venv venv
    source venv/bin/activate
    ```

- 安装所需的依赖项

    ```bash
    pip install -r requirements.txt
    ```

## 🛠️ 脚本参数

- `--source_weights_path`：必需。指定 YOLO 模型权重的路径。此文件包含目标检测所需的已训练模型数据。
- `--source_video_path`（可选）：将被分析的源视频文件的路径。这是将执行人群分析的输入视频。
    如果未指定，则默认为 `supervision` 资源中的 `people-walking.mp4`。
- `--target_video_path`（可选）：保存带标注的输出 `.mp4` 视频的路径。
- `--confidence_threshold`（可选）：设置 YOLO 模型的置信度阈值以过滤检测结果。默认为 `0.3`。这决定了模型识别视频中对象的置信度。
- `--iou_threshold`（可选）：指定模型的 IOU（交并比）阈值。默认为 0.7。该值用于管理目标检测的准确性，特别是在区分不同对象时。
- `--heatmap_alpha`（可选）：叠加蒙版的透明度，介于 0 和 1 之间。
- `--radius`（可选）：热力圆的半径。
- `--track_threshold`（可选）：用于激活追踪的检测置信度阈值。
- `--track_seconds`（可选）：丢失追踪时要缓冲的秒数。
- `--match_threshold`（可选）：用于将追踪与检测匹配的阈值。

## ⚙️ 运行

```bash
python script.py \
    --source_weights_path weight.pt \
    --source_video_path  input_video.mp4 \
    --confidence_threshold 0.3 \
    --iou_threshold 0.5 \
    --target_video_path  output_video.mp4
```

## © 版权

此演示集成了两个主要组件，每个组件都有其自己的许可：

- ultralytics：此演示中使用的目标检测模型 YOLOv8，在 [AGPL-3.0 许可](https://github.com/ultralytics/ultralytics/blob/main/LICENSE)下分发。
    您可以在此处找到有关此许可的更多详细信息。

- supervision：为演示中的基于区域的分析提供支持的分析代码基于 Supervision 库，该库在 [MIT 许可](https://github.com/roboflow/supervision/blob/develop/LICENSE.md)下获得许可。这使得 Supervision 部分的代码完全开源，并且可以在您的项目中使用。