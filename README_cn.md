# DragGAN 程序使用说明

本文档仅包含本项目的使用方法，涵盖 DragGAN GUI 与 Gradio 网页版的启动、参数说明与 Feature Fusion 的使用。

## 环境准备

如果有 CUDA 显卡，请按照官方推荐环境配置：

```sh
conda env create -f environment.yml
conda activate stylegan3
pip install -r requirements.txt
```

Mac M1/M2 或 CPU 情况：

```sh
cat environment.yml | \
  grep -v -E 'nvidia|cuda' > environment-no-nvidia.yml && \
    conda env create -f environment-no-nvidia.yml
conda activate stylegan3

# On MacOS
export PYTORCH_ENABLE_MPS_FALLBACK=1
```

## 模型权重下载

```sh
python scripts/download_model.py
```

将下载的模型放在 `./checkpoints` 下。额外模型可参考 `main/README.md` 中的链接说明。

## 启动 GUI 版本（本地桌面）

Linux/macOS：

```sh
sh scripts/gui.sh
```

Windows：

```sh
.\scripts\gui.bat
```

### GUI 版本参数

```sh
python visualizer_drag.py --fusion
python visualizer_drag.py --no-fusion
python visualizer_drag.py --fusion --fusion-res 512
```

### GUI 版本特征融合操作

- **Feature Fusion** 开关与 **Fusion Resolution** 输入框位于左侧 Mask 区域。
- `--fusion-res` 会设定默认融合分辨率（如 256、512）。

## 启动 Gradio 网页版

```sh
python visualizer_drag_gradio.py
```

如需监听公网：

```sh
python visualizer_drag_gradio.py --listen
```

### Gradio 版本命令行参数

```sh
# 启用特征融合
python visualizer_drag_gradio.py --fusion

# 禁用特征融合（默认）
python visualizer_drag_gradio.py --no-fusion

# 启用并指定融合分辨率
python visualizer_drag_gradio.py --fusion --fusion-res 512
```

### Gradio 版本界面操作

特征融合控制位于图片区域下方：

- **Enable Feature Fusion**：开关
- **Fusion Resolution**：下拉选择（64 / 128 / 256 / 512）

## Feature Fusion（背景保留）说明

Feature Fusion 会在高分辨率特征层执行融合，以保持背景细节稳定：

$$F_{out} = M \cdot F_{generated} + (1 - M) \cdot F_{initial}$$

其中：

- `M=1` 表示可动区域
- `M=0` 表示固定背景区域

## 使用流程（通用）

1. 选择模型和随机种子，生成初始图像  
2. 添加控制点 (Add Points)  
3. 编辑 Mask 区域（保留背景）  
4. 开启 Feature Fusion（可选）  
5. Start 开始拖拽  

## Docker 运行 Gradio

```sh
docker build . -t draggan:latest
docker run -p 7860:7860 -v "$PWD":/workspace/src -it draggan:latest bash
cd src && python visualizer_drag_gradio.py --listen
```

如需 GPU：

```sh
docker run --gpus all -p 7860:7860 -v "$PWD":/workspace/src -it draggan:latest bash
```

