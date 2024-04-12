Task01 的部分就是照着原文档的这个部分做：[module1](https://minitorch.github.io/module1/module1/)

### 把 Task0 的工作同步过来

在 module-1 的目录中 运行 `python sync_previous_module.py minitorch-module-0-Username minitorch-module-1-Username`

### Task1.1: Numerical Derivatives

实现标量数值导数计算。这个函数不会在主库中使用，但对于测试整个模块来说将是至关重要的。

!!! info "todo"
    完成 `minitorch/autodiff.py` 中标记为 `task1_1` 的测试函数。

**minitorch.autodiff.central_difference(f: Any, *vals: Any, arg: int = 0, epsilon: float = 1e-06) -> Any**

计算函数 `f` 关于一个参数的导数的近似值。

实际上计算的就是“中心差分”，这是一种近似计算倒数值的算法，公式为 `(f(x+epsilon/2)-f(x-epsilon/2))/epsilon`，推导过程可参考：[中心差分法数值微分求解](https://zhuanlan.zhihu.com/p/361234743)

参数：

- `f`: 从n个标量参数到一个值的任意函数
- `*vals`: n个浮点值 x_0, ..., x_{n-1}
- `arg`: 计算导数的参数的编号 i
- `epsilon`: 一个小常数

返回：

- 任意值: f'i(x0,...,xn−1) 的近似值


??? info "example 中 * 的用法"
    在Python中，星号`*`有多种用法，具体取决于它的上下文。在这个函数中，`*`被用在两个地方，分别是函数定义中的`*vals`和函数调用中的`f(*tmp_val)`。

    1. `*vals`：在函数定义中，`*`用于收集所有位置参数到一个元组中。这就意味着你可以向这个函数传递任意数量的位置参数，它们都会被收集到`vals`这个元组中。例如，如果你调用`central_difference(f, 1, 2, 3)`，那么在函数内部，`vals`就会是一个元组`(1, 2, 3)`。

    2. `f(*tmp_val)`：在函数调用中，`*`用于将一个序列（例如列表或元组）解包成位置参数。这就意味着如果你有一个列表`tmp_val = [1, 2, 3]`，那么`f(*tmp_val)`等价于`f(1, 2, 3)`。

    这两种用法都是Python中的参数解包（argument unpacking）特性的一部分，它们使得函数可以更灵活地处理参数。


### Task1.2: Scalars

实现对于 `minitorch.Scalar` 类所需的重写数学函数。每个函数都需要将内部 Python 操作符连接到正确的 `minitorch.Function.forward` 调用。

阅读我们已经实现的 ScalarFunctions 示例以获取指导。你可能会发现重用 Module 0 中的操作符很有用。

我们为你构建了一个调试工具，以便你观察表达式的工作情况，看看如何构建图。你可以在 Autodiff Sandbox 中运行它。你可以更改文件顶部的表达式，然后运行代码在 Streamlit 中创建一个图：

```
streamlit run app.py -- 1
```

!!! info "todo"
    完成 `minitorch/scalar_functions.py` 中的下列函数。
    这一步就是用 Task0 中实现的 operator 来实现各种函数的 forward，记得要保存 backward 需要用到的值在 ctx 中

**minitorch.scalar_functions.Mul.forward(ctx: Context, a: float, b: float) -> float staticmethod**

**minitorch.scalar_functions.Inv.forward(ctx: Context, a: float) -> float staticmethod**

**minitorch.scalar_functions.Neg.forward(ctx: Context, a: float) -> float staticmethod**

**minitorch.scalar_functions.Sigmoid.forward(ctx: Context, a: float) -> float staticmethod**

**minitorch.scalar_functions.ReLU.forward(ctx: Context, a: float) -> float staticmethod**

**minitorch.scalar_functions.Exp.forward(ctx: Context, a: float) -> float staticmethod**

**minitorch.scalar_functions.LT.forward(ctx: Context, a: float, b: float) -> float staticmethod**

**minitorch.scalar_functions.EQ.forward(ctx: Context, a: float, b: float) -> float staticmethod**

!!! info "todo"
    在 `minitorch/scalar.py` 中完成以下函数，并通过标记为 `task1_2` 的测试。查看 Python 数值覆盖接口以获取这些方法的接口。所有这些函数都应返回 `minitorch.Scalar` 参数。

**minitorch.scalar.Scalar.__lt__(b: ScalarLike) -> Scalar**

**minitorch.scalar.Scalar.__gt__(b: ScalarLike) -> Scalar**

**minitorch.scalar.Scalar.__sub__(b: ScalarLike) -> Scalar**

**minitorch.scalar.Scalar.__neg__() -> Scalar**

**minitorch.scalar.Scalar.__add__(b: ScalarLike) -> Scalar**

**minitorch.scalar.Scalar.log() -> Scalar**

**minitorch.scalar.Scalar.exp() -> Scalar**

**minitorch.scalar.Scalar.sigmoid() -> Scalar**

**minitorch.scalar.Scalar.relu() -> Scalar**

这个部分我认为要把 `scalar_functions.py` 中的 `assert isinstance(c, float), "Expected return type float got %s" % (type(c))` 给注释掉，因为 Python 会把 1.0 自动变成 1，所以会导致这个地方过不了测试，参考[这个 Issue](https://github.com/minitorch/Module-1/issues/10)


### Task1.3: Chain Rule

为 `Scalar` 中的任意参数函数实现 `chain_rule` 函数。这个函数应该能够通过传入一个上下文和导数来对一个函数进行反向处理，然后收集局部导数。然后，它应该将这些局部导数与正确的变量配对并返回。此外，这个函数也应该过滤掉在前向传播中使用但不需要导数的常数。

!!! info "todo"
    完成 `minitorch/scalar.py` 中标记为 `task1_3` 的测试函数。

**minitorch.scalar.Scalar.chain_rule(d_output: Any) -> Iterable[Tuple[Variable, Any]]**


### Task1.4: Backpropagation

实现反向传播。每一个操作都需要将内部的Python操作符连接到正确的 `minitorch.Function.backward` 调用。

参考我们已经实现的 ScalarFunctions 示例以获取指导。如果你忘记了这些标识是如何工作的，也可以随时查阅[微分规则](https://en.wikipedia.org/wiki/Differentiation_rules)。

!!! info "todo"
    完成 `minitorch/autodiff.py` 和 `minitorch/scalar_functions.py` 中标记为 `task1_4` 的测试函数。

**minitorch.topological_sort(variable: Variable) -> Iterable[Variable]**

计算计算图的拓扑顺序。

参数：

- variable (Variable) - 最右边的变量

返回：

- Iterable[Variable] - 从右边开始的非常量变量的拓扑顺序。

**minitorch.backpropagate(variable: Variable, deriv: Any) -> None**

在计算图上运行反向传播，以计算叶节点的导数。

参数：

- variable (Variable) - 最右侧的变量
- deriv ( ) - 我们希望向叶节点反向传播的导数。

无返回值。应通过accumulate_derivative将其结果写入每个叶节点的导数值。

**minitorch.scalar_functions.Mul.backward(ctx: Context, d_output: float) -> Tuple[float, float] staticmethod**

**minitorch.scalar_functions.Inv.backward(ctx: Context, d_output: float) -> float staticmethod**

**minitorch.scalar_functions.Neg.backward(ctx: Context, d_output: float) -> float staticmethod**

**minitorch.scalar_functions.Sigmoid.backward(ctx: Context, d_output: float) -> float staticmethod**

**minitorch.scalar_functions.ReLU.backward(ctx: Context, d_output: float) -> float staticmethod**

**minitorch.scalar_functions.Exp.backward(ctx: Context, d_output: float) -> float staticmethod**


### Task1.5: Training

如果你的代码可以运行，你现在应该能够运行训练脚本了。仔细研究 `project/run_scalar.py` 中的代码，以理解神经网络正在执行的操作。

你还需要模块代码来实现 `Network` 和 `Linear` 的参数。你可以在文件底部修改数据集和带有参数的模块。可以从这个简单的配置开始：

```
PTS = 50
DATASET = minitorch.datasets["Simple"](PTS)
HIDDEN = 2
RATE = 0.5
```

你可以接着尝试一些更复杂的事情，例如：

```
PTS = 50
DATASET = minitorch.datasets["Xor"](PTS)
HIDDEN = 10
RATE = 0.5
```

如果你的代码运行成功，你应该能够进行完整的可视化操作：

```
streamlit run app.py -- 1
```

!!! info "todo"
    为四个主要数据集各训练一个标量模型。

    将输出的训练日志和最终图像添加到你的README文件中。

最后，记得要把 `requirements.txt` 的 `mypy` 版本改成 `mypy == 0.981` 才能通过 github ci 自动评测