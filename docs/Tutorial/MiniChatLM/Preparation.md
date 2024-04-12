## Install Miniforge

根据 [Miniforge Github](https://github.com/conda-forge/miniforge) 中的内容安装 Miniforge

如何进入环境：

```
eval "$(/maindata/data/shared/Security-SFT/cmz/env_root/miniforge3/bin/conda shell.bash hook)"
```

安装 Miniforge 工具是为了使用 `mamba` 工具，这个工具可以更好的替代 `conda`

!!! info "mamba"
    Mamba 是一个针对 Conda 包和环境管理的开源项目，它旨在提供 Conda 的完全兼容实现，同时还提供一些优化和改进。与 Conda 相比，Mamba 的主要优势包括：

    1. **速度**：Mamba 使用了 C++ 和多线程，这使得它在解析和处理包时比 Conda 快很多。这在处理包含大量包的复杂环境时尤其有用。

    2. **并行下载**：Mamba 支持并行下载，可以更快地获取多个包。

    3. **改进的冲突报告**：当包之间存在版本冲突时，Mamba 可以提供更详细的错误报告，帮助用户找出问题所在。

    4. **更好的集成**：Mamba 更好地集成了 Python 的 PyPi 和 Conda 的生态系统，使得两者之间的包可以更加无缝地协同工作。

    5. **跨平台**：和 Conda 一样，Mamba 也是跨平台的，可以在 Windows、macOS 和 Linux 上运行。

    `mamba` 的使用方法和 `conda` 一样


## Clone Repo

```
git clone --depth 1 https://github.com/charent/ChatLM-mini-Chinese.git

cd ChatLM-mini-Chinese
```

!!! info "--depth 1"
    `--depth 1` 是一个选项，用于指定 `git clone` 命令的深度。这个选项通常用于创建一个浅克隆（shallow clone）。

    在默认情况下，`git clone` 会下载整个项目的历史记录，包括所有的提交和分支。如果一个项目有很长的历史，那么克隆这个项目可能会花费很长的时间，并且需要大量的磁盘空间。

    当你使用 `--depth 1` 选项时，`git clone` 只会下载最近的一次提交。这样做可以大大减少下载的数据量，使得克隆过程更快，占用的磁盘空间更少。但是，这也意味着你无法访问项目的完整历史记录。

    所以，`git clone --depth 1 https://github.com/charent/ChatLM-mini-Chinese.git` 这条命令的意思是，克隆 `https://github.com/charent/ChatLM-mini-Chinese.git` 这个项目，但只下载最近的一次提交，不下载其他的历史记录。

## Create Env

使用 python 3.10：

```
mamba create -n MiniChatLM python=3.10

mamba activate MiniChatLM
```

安装依赖：

```
pip install -r ./requirements.txt
```

