---
template: index.html
comments: true
hide:
  - navigation
  - toc
---

<div class="md-typeset">
  <h1></h1>
</div>

<div align="center" id="logo" style="padding-top: 1rem;">
  <a align="center" href="" target="_blank">
      <img width="850"
          src="https://media.roboflow.com/open-source/supervision/rf-supervision-banner.png?updatedAt=1678995927529">
  </a>
</div>

<style>
    #hello {
        margin: 0;
    }
</style>

## 👋 你好

我们为你提供可复用的计算机视觉工具。无论你需要从硬盘加载数据集、绘制检测框到图像或视频上，还是统计区域内的检测数量，我们都能满足你！

<video controls>
    <source
        src="https://media.roboflow.com/traffic_analysis_result.mp4"
        type="video/mp4"
    >
</video>

## 💻 安装

你可以在 **Python>=3.9** 的环境中安装 `supervision`。

!!! example "安装"

    === "pip (推荐)"
        [![version](https://badge.fury.io/py/supervision.svg)](https://badge.fury.io/py/supervision)
        [![downloads](https://img.shields.io/pypi/dm/supervision)](https://pypistats.org/packages/supervision)
        [![license](https://img.shields.io/pypi/l/supervision)](https://github.com/roboflow/supervision/blob/main/LICENSE.md)
        [![python-version](https://img.shields.io/pypi/pyversions/supervision)](https://badge.fury.io/py/supervision)

        ```bash
        pip install supervision
        ```

    === "poetry"
        [![version](https://badge.fury.io/py/supervision.svg)](https://badge.fury.io/py/supervision)
        [![downloads](https://img.shields.io/pypi/dm/supervision)](https://pypistats.org/packages/supervision)
        [![license](https://img.shields.io/pypi/l/supervision)](https://github.com/roboflow/supervision/blob/main/LICENSE.md)
        [![python-version](https://img.shields.io/pypi/pyversions/supervision)](https://badge.fury.io/py/supervision)

        ```bash
        poetry add supervision
        ```

    === "uv"
        [![version](https://badge.fury.io/py/supervision.svg)](https://badge.fury.io/py/supervision)
        [![downloads](https://img.shields.io/pypi/dm/supervision)](https://pypistats.org/packages/supervision)
        [![license](https://img.shields.io/pypi/l/supervision)](https://github.com/roboflow/supervision/blob/main/LICENSE.md)
        [![python-version](https://img.shields.io/pypi/pyversions/supervision)](https://badge.fury.io/py/supervision)

        ```bash
        uv pip install supervision
        ```

        对于 uv 项目：

        ```bash
        uv add supervision
        ```

    === "rye"
        [![version](https://badge.fury.io/py/supervision.svg)](https://badge.fury.io/py/supervision)
        [![downloads](https://img.shields.io/pypi/dm/supervision)](https://pypistats.org/packages/supervision)
        [![license](https://img.shields.io/pypi/l/supervision)](https://github.com/roboflow/supervision/blob/main/LICENSE.md)
        [![python-version](https://img.shields.io/pypi/pyversions/supervision)](https://badge.fury.io/py/supervision)

        ```bash
        rye add supervision
        ```


!!! example "conda/mamba 安装"
    === "conda"
        [![conda-recipe](https://img.shields.io/badge/recipe-supervision-green.svg)](https://anaconda.org/conda-forge/supervision) [![conda-downloads](https://img.shields.io/conda/dn/conda-forge/supervision.svg)](https://anaconda.org/conda-forge/supervision) [![conda-version](https://img.shields.io/conda/vn/conda-forge/supervision.svg)](https://anaconda.org/conda-forge/supervision) [![conda-platforms](https://img.shields.io/conda/pn/conda-forge/supervision.svg)](https://anaconda.org/conda-forge/supervision)

        ```bash
        conda install -c conda-forge supervision
        ```

    === "mamba"
        [![mamba-recipe](https://img.shields.io/badge/recipe-supervision-green.svg)](https://anaconda.org/conda-forge/supervision) [![mamba-downloads](https://img.shields.io/conda/dn/conda-forge/supervision.svg)](https://anaconda.org/conda-forge/supervision) [![mamba-version](https://img.shields.io/conda/vn/conda-forge/supervision.svg)](https://anaconda.org/conda-forge/supervision) [![mamba-platforms](https://img.shields.io/conda/pn/conda-forge/supervision.svg)](https://anaconda.org/conda-forge/supervision)

        ```bash
        mamba install -c conda-forge supervision
        ```

!!! example "git clone (用于开发)"
    === "virtualenv"
        ```bash
        # 克隆仓库并进入根目录
        git clone --depth 1 -b develop https://github.com/roboflow/supervision.git
        cd supervision

        # 设置 python 环境并激活
        python3 -m venv venv
        source venv/bin/activate
        pip install --upgrade pip

        # 安装
        pip install -e "."
        ```

    === "uv"
        ```bash
        # 克隆仓库并进入根目录
        git clone --depth 1 -b develop https://github.com/roboflow/supervision.git
        cd supervision

        # 设置 python 环境并激活
        uv venv
        source .venv/bin/activate

        # 安装
        uv pip install -r pyproject.toml -e . --all-extras

        ```

## 🚀 快速开始

<div class="grid cards" markdown>

- **检测与标注**

    ---

    标注来自各种对象检测和分割模型的预测结果

    [:octicons-arrow-right-24: 教程](how_to/detect_and_annotate.md)

- **物体追踪**

    ---

    通过实现无缝物体追踪来增强视频分析

    [:octicons-arrow-right-24: 教程](how_to/track_objects.md)

- **检测小物体**

    ---

    学习如何在图像中检测小物体

    [:octicons-arrow-right-24: 教程](how_to/detect_small_objects.md)

- **计算越线物体数量**

    ---

    探索准确计算和分析越过预定线的物体数量的方法

    [:octicons-arrow-right-24: Notebook](https://supervision.roboflow.com/latest/notebooks/count-objects-crossing-the-line/)

- > **区域内物体过滤**

    ---

    掌握在特定区域内选择性地过滤和聚焦物体对象的技巧

- **速查表**

    ---

    获取最常用的 `supervision` 函数的快速参考指南

    [:octicons-arrow-right-24: 速查表](https://roboflow.github.io/cheatsheet-supervision/)

</div>