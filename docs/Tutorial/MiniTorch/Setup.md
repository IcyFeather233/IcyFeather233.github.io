### 环境要求

创建一个虚拟环境，要求Python 3.8 ~ 3.11

### 创建单个 Module 个人仓库

点击下面的链接来创建单个的 Module 个人仓库

<div style="text-align: center;">
    <img src="../../../assets/MiniTorch/ownmodule.png" width="80%">
</div>

创建之后 git clone 到本地即可

### 安装依赖

```shell
python -m pip install -r requirements.txt
python -m pip install -r requirements.extra.txt
python -m pip install -Ue .
```

??? info "`requirements.txt` 和 `requirements.extra.txt`"
    minitorch 已经是比较老的项目了，按照原仓库里面的配置可能会后续出错。
    
    建议把这两个文件里面的包版本号都删掉，然后把 `requirements.extra.txt` 里面的 `torch` 移动到 `requirements.txt` 里面最前面，这样后面的那些包可以自适应 `torch` 的版本

    建议 Python 版本：3.9

    ```title="requirements.txt"
    torch
    colorama
    hypothesis
    mypy
    numba
    numpy
    pre-commit
    pytest
    pytest-env
    pytest-runner
    typing_extensions
    ```

    ```title="requirements.extra.txt"
    datasets
    embeddings
    networkx
    plotly
    pydot
    python-mnist
    streamlit
    streamlit-ace
    watchdog
    ```

如果是使用的 conda，还需要：`conda install llvmlite`

### 测试

进入 python 环境然后运行：

```
import minitorch
```

测试是否成功