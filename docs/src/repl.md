# REPL

## 快速开始

将 [Jupyter kernels](https://docs.jupyter.org/en/latest/projects/kernels.html) 的能力引入编辑器！Zed 内置的 REPL 让你在普通文本文件中像 Notebook 一样交互式运行代码。

<figure style="width: 100%; margin: 0; overflow: hidden; border-top-left-radius: 2px; border-top-right-radius: 2px;">
    <video loop controls playsinline>
        <source
            src="https://customer-snccc0j9v3kfzkif.cloudflarestream.com/aec66e79f23d6d1a0bee5e388a3f17cc/downloads/default.mp4"
            type='video/webm; codecs="vp8.0, vorbis"'
        />
        <source
            src="https://customer-snccc0j9v3kfzkif.cloudflarestream.com/aec66e79f23d6d1a0bee5e388a3f17cc/downloads/default.mp4"
            type='video/mp4; codecs="avc1.4D401E, mp4a.40.2"'
        />
        <source
          src="https://zed.dev/img/post/repl/typescript-deno-kernel-markdown.png"
          type="image/png"
        />
    </video>
</figure>

## 安装

Zed 支持多种语言的 REPL。使用前需要为目标语言安装相应的内核（kernel）。

**当前支持的语言：**

- [Python（ipykernel）](#python)
- [TypeScript（Deno）](#typescript-deno)
- [R（Ark）](#r-ark)
- [R（Xeus）](#r-xeus)
- [Julia](#julia)
- [Scala（Almond）](#scala)

安装完成后，即可在对应语言的文件（包括 Markdown 等）中使用 REPL。新安装的 kernel 可通过执行 `repl: refresh kernelspecs` 刷新列表。

## 使用方式

打开目标语言的文件，执行 `repl: run`（macOS 默认 `ctrl-shift-enter`，Windows/Linux 可自定义）即可运行选中区域、当前块或当前行。也可以点击工具栏的 REPL 图标。

运行结果会显示在选区下方；可通过 `repl: clear outputs` 命令或工具栏菜单清理输出。

### 单元模式

Zed 支持“脚本式 Notebook”，即在 Python 中以 `# %%`、在 TypeScript 中以 `// %%` 标记单元。`repl: run` 会按单元分块执行：

```python
# %% Cell 1
import time
import numpy as np

# %% Cell 2
import matplotlib.pyplot as plt
from matplotlib import style
style.use('ggplot')
```

## 各语言安装说明

### Python {#python}

#### 全局环境

<div class="warning">
在 macOS 上，请勿使用系统自带的 Python。可通过 [pyenv](https://github.com/pyenv/pyenv?tab=readme-ov-file#installation) 或虚拟环境安装。
</div>

安装内核：

```sh
pip install ipykernel
python -m ipykernel install --user
```

#### Conda 环境

```sh
source activate myenv
conda install ipykernel
python -m ipykernel install --user --name myenv --display-name "Python (myenv)"
```

#### virtualenv（pip）

```sh
source activate myenv
pip install ipykernel
python -m ipykernel install --user --name myenv --display-name "Python (myenv)"
```

### R（Ark Kernel）{#r-ark}

从 [Ark 发布页](https://github.com/posit-dev/ark/releases) 下载安装。例如在 macOS 解压 `ark` 可执行文件并放入 `/usr/local/bin`，然后执行：

```sh
ark --install
```

### R（Xeus Kernel）{#r-xeus}

1. 安装 [Xeus-R](https://github.com/jupyter-xeus/xeus-r)
2. 在 Zed 扩展中搜索并安装 “R” 扩展

### TypeScript（Deno）{#typescript-deno}

1. [安装 Deno](https://docs.deno.com/runtime/manual/getting_started/installation/)
2. 安装 Deno 的 Jupyter 内核：

```sh
deno jupyter --install
```

### Julia

1. 从 [官方站点](https://julialang.org/downloads/) 下载并安装 Julia
2. 在 Zed 扩展中安装 “Julia” 扩展

### Scala

1. 使用 `cs setup` 安装 [Scala](https://www.scala-lang.org/download/)（Coursier）：
   - `brew install coursier/formulas/coursier && cs setup`
2. 按 [Almond](https://almond.sh/docs/quick-start-install) 指引安装 REPL：
   - `brew install --cask temurin`
   - `brew install coursier/formulas/coursier && cs setup`
   - `coursier launch --use-bootstrap almond -- --install`

## 指定默认内核 {#changing-kernels}

Zed 会自动检测可用内核。如果需要为某种语言指定默认内核，可在 `settings.json` 中配置：

```json [settings]
{
  "jupyter": {
    "kernel_selections": {
      "python": "conda-env",
      "typescript": "deno",
      "javascript": "deno",
      "r": "ark"
    }
  }
}
```

## 调试 Kernelspec

使用 `repl: sessions` 查看当前会话，`repl: refresh kernelspecs` 刷新列表。如果安装了 `jupyter`，可运行：

```sh
jupyter kernelspec list
```

输出示例：

```sh
Available kernels:
  ark                   /Users/z/Library/Jupyter/kernels/ark
  conda-base            /Users/z/Library/Jupyter/kernels/conda-base
  deno                  /Users/z/Library/Jupyter/kernels/deno
  python3               /Users/z/Library/Jupyter/kernels/python3
```

> 注意：Zed 会尝试通过 `sys.prefix` 与 `CONDA_PREFIX` 自动查找 Python 内核。如需精确控制，可在目标环境中执行 `python -m ipykernel install --user --name myenv --display-name "Python (myenv)"`。
