这里是 Minitorch 项目的一些前置知识，请配合 [Contributing](https://minitorch.github.io/module0/contributing/) 查看

## Pytest

`pytest`是一个成熟的全功能的Python测试工具，它用于编写所有级别的测试，包括单元测试、集成测试和功能测试。它是Python测试库中的一个主要选择，与其他测试工具（如`unittest`和`nose`）相比，`pytest`提供了一些独特的功能。


1. **详细的错误报告**：当测试失败时，`pytest`可以提供详细的错误报告，包括失败的测试名称、失败的位置（文件名和行号）、失败的原因和相关的代码片段。这有助于开发者快速定位和解决问题。

2. **自动发现测试**：`pytest`可以自动发现并运行你的测试代码。你只需要按照一定的约定命名你的测试文件（如`test_*.py`或`*_test.py`），然后在这些文件中定义你的测试函数（函数名以`test_`开头），`pytest`就可以自动找到并运行这些测试。

3. **强大的断言机制**：在`pytest`中，你可以使用Python的`assert`语句来进行断言。相比于其他测试工具提供的专用断言方法（如`assertEquals`、`assertTrue`等），`assert`语句更简单、更自然。此外，`pytest`会自动分析`assert`语句并在断言失败时提供详细的信息，包括相关变量的值和表达式的结果。

4. **灵活的fixture机制**：在`pytest`中，fixture是一种特殊的函数，可以被测试函数使用。Fixture可以用来设置和清理测试需要的环境，例如打开和关闭数据库连接、创建和删除文件、准备和清理测试数据等。你可以在一个特殊的函数`pytest.fixture()`中定义fixture，然后在测试函数中通过参数的方式使用fixture。Fixture可以被多个测试重复使用，可以带参数，还可以依赖其他fixture。

5. **参数化测试**：`pytest`提供了`pytest.mark.parametrize`装饰器，可以用来参数化测试函数。你可以为测试函数提供多组输入数据和预期结果，`pytest`会为每组数据生成并运行一个测试。这样，你可以用一份代码测试多种情况，提高测试效率。

6. **插件支持**：`pytest`有一个活跃的插件生态系统，有很多第三方插件可以用来扩展`pytest`的功能，例如`pytest-cov`用于生成测试覆盖率报告，`pytest-xdist`用于并行运行测试，`pytest-mock`用于模拟对象等。你也可以编写自己的插件来满足特定的测试需求。

7. **标记（marking）和选择/跳过测试**：`pytest`提供了`pytest.mark`装饰器，可以用来给测试函数添加元数据，例如标记某些测试为慢速的或需要网络连接的。然后你可以使用`-m`选项告诉`pytest`只运行或跳过带有特定标记的测试。此外，`pytest`也提供了`pytest.skip`和`pytest.xfail`函数，可以用来跳过或预期失败的测试。

这些特性使得`pytest`成为Python测试工具中的一个主要选择，它可以帮助你编写高效、准确、易维护的测试代码。

要使用`pytest`进行测试，你需要先安装`pytest`。你可以使用`pip`安装：

```bash
pip install pytest
```

然后，你可以创建一个测试文件，例如`test_example.py`。在这个文件中，你可以定义你的测试函数。每个测试函数的名称都应该以`test_`开头。例如：

```python
def test_addition():
    assert 1 + 1 == 2
```

然后，你可以在命令行中运行`pytest`命令来执行你的测试：

```bash
pytest test_example.py
```

`pytest`会自动发现并运行你的测试函数，并报告测试结果。

如果你需要设置或清理测试环境，你可以使用`pytest`的fixture机制。例如，你可以创建一个fixture函数来打开和关闭数据库连接：

```python
import pytest

@pytest.fixture
def db_connection():
    conn = create_connection()  # 创建数据库连接
    yield conn  # 提供数据库连接给测试函数使用
    conn.close()  # 关闭数据库连接

def test_database(db_connection):
    assert db_connection.is_open()
```

在这个例子中，`db_connection`是一个fixture函数，它被`test_database`函数使用。当`test_database`函数运行时，`pytest`会自动调用`db_connection`函数并将其返回值（即数据库连接）传递给`test_database`函数。

如果你需要为一个测试函数提供多组输入数据和预期结果，你可以使用`pytest.mark.parametrize`装饰器。例如：

```python
import pytest

@pytest.mark.parametrize("input,expected", [(1, 1), (2, 4), (3, 9)])
def test_square(input, expected):
    assert input**2 == expected
```

在这个例子中，`test_square`函数会被运行三次，每次使用一组不同的输入数据和预期结果。


在`pytest`中，你可以使用`pytest.mark`装饰器给测试函数添加标记。这可以用来分类你的测试，或者给测试添加一些元数据。例如，你可以标记一些测试为慢速的：

```python
import pytest

@pytest.mark.slow
def test_slow_operation():
    ...
```

你也可以使用多个标记：

```python
@pytest.mark.slow
@pytest.mark.network
def test_slow_network_operation():
    ...
```

然后，你可以使用`-m`选项告诉`pytest`只运行或跳过带有特定标记的测试：

```bash
pytest -m slow  # 只运行标记为slow的测试
pytest -m "not slow"  # 跳过标记为slow的测试
```


`pytest`提供了多种方式来选择和跳过测试。

- 你可以在命令行中指定要运行的测试文件、模块、类或函数：

  ```bash
  pytest test_example.py::test_addition  # 只运行test_example.py文件中的test_addition函数
  ```

- 你可以使用`-k`选项来运行匹配特定模式的测试：

  ```bash
  pytest -k "addition or multiplication"  # 只运行名称中包含"addition"或"multiplication"的测试
  ```

- 你可以使用`pytest.skip`函数来跳过某个测试：

  ```python
  def test_example():
      pytest.skip("Skipping this test for now")
  ```

  当`pytest`运行到这个测试时，它会直接跳过这个测试，不执行其内部的代码。

- 你也可以使用`pytest.mark.skip`或`pytest.mark.skipif`装饰器来跳过某个测试：

  ```python
  @pytest.mark.skip(reason="No way to test this")
  def test_example():
      ...

  @pytest.mark.skipif(sys.version_info < (3, 7), reason="requires Python 3.7 or higher")
  def test_example():
      ...
  ```

  在这些例子中，`test_example`函数会被跳过，不会被执行。第一个例子中，测试总是被跳过；第二个例子中，测试只在Python版本低于3.7时被跳过。

以上就是`pytest`中测试标记、测试选择和跳过的基本用法。这些功能可以帮助你更灵活地管理和运行你的测试。

## Black

PSF/Black 是一个 Python 代码格式化工具，也被称为 "Black"。它是由 Python Software Foundation (PSF) 开发和维护的。Black 的设计目标是减少小的样式决定带来的无意义的争论，并通过提供一个标准的自动格式化工具，使开发者可以更专注于编程逻辑本身。

Black 的主要特性包括：

1. **自动格式化**：Black 可以自动修改你的 Python 代码，使其符合 PEP 8（Python 的主要编程风格指南）的规定。

2. **无配置**：Black 的设计理念是“任何配置都是不必要的”。它有一个固定且非常小的配置集，这意味着你不需要花时间在代码风格的微调上。

3. **快速**：Black 是一个快速的格式化工具，可以在很短的时间内处理大量的代码。

4. **确定性**：给定一段代码，Black 总是会生成相同的输出，无论运行多少次。

使用 Black 的基本命令是 `black [OPTIONS] [SRC]...`，其中 `SRC` 是你想要格式化的 Python 文件或目录。例如，如果你想格式化一个名为 `my_script.py` 的文件，你可以运行 `black my_script.py`。

Black 还提供了一些选项，如 `--check`（检查是否需要格式化而不实际格式化）、`--diff`（显示需要格式化的差异）、`--line-length`（设置行长度，虽然默认是 88，但你可以改变它）等。

总的来说，Black 是一个强大的工具，可以帮助 Python 开发者保持代码的一致性和可读性，从而提高代码质量。

## Flake8

Flake8是Python编程语言的一个静态代码检查工具，它可以帮助开发者在代码提交到代码库之前，自动检查代码是否符合PEP 8编码规范，以及进行一些简单的语法错误和代码复杂度的检查。Flake8实际上是将PyFlakes，pep8，Ned Batchelder's McCabe script这三个工具集成在一起，所以它可以在一个工具中同时进行语法检查，代码风格检查和复杂度计算。

以下是Flake8的一些主要特点：

1. **错误和异常检查**：Flake8可以检查Python代码中的语法错误和异常，例如未定义的变量和未使用的导入。

2. **代码风格检查**：Flake8遵循PEP 8，这是Python社区广泛接受的代码风格指南。通过检查代码是否符合PEP 8，Flake8可以帮助开发者编写更易读、更统一的代码。

3. **代码复杂度计算**：Flake8使用McCabe脚本来计算代码的复杂度。这可以帮助开发者识别需要重构的复杂函数或方法。

4. **插件支持**：Flake8支持插件，这意味着你可以扩展其功能以满足特定的需求。有许多现成的Flake8插件可以用来检查诸如代码注释、代码格式和逻辑错误等更多方面。

5. **易于集成**：Flake8可以轻松集成到各种开发环境和持续集成工具中，如VS Code，PyCharm，Jenkins，Travis CI等。

使用Flake8非常简单，你可以通过pip安装它：

```bash
pip install flake8
```

然后，你可以在命令行中运行Flake8来检查单个文件或整个项目：

```bash
flake8 path/to/your/code.py
```

或者：

```bash
flake8 path/to/your/project
```

Flake8会列出所有发现的问题，每个问题一行。每行包括文件名，问题所在行数，问题所在列数，以及问题的描述。

## PEP 8（Python 的主要编程风格指南）

PEP 8是Python社区为了确保代码的一致性和可读性而制定的编程风格指南。PEP是Python Enhancement Proposal的缩写，意为Python增强提案，其中PEP 8是最著名的一项提案。以下是PEP 8的主要内容：

1. **缩进**：PEP 8建议每级缩进使用4个空格。在连续行中，应使用括号或使用悬挂缩进来区分不同的部分。

2. **最大行长度**：PEP 8建议每行的最大长度为79个字符。这样可以确保在大多数显示器上，代码可以不用滚动就能完全显示。

3. **空行**：PEP 8建议在顶级函数和类定义之间使用两个空行，而在类方法之间使用一个空行。

4. **导入**：每个导入语句应独占一行，并且应按照从最通用到最不通用的顺序导入：标准库导入、相关第三方导入、本地应用/库的导入。

5. **空格**：PEP 8提供了关于何时应该和不应该使用空格的详细指南。例如，在逗号、冒号和分号后面应该使用空格，但在括号内部则不应该使用空格。

6. **注释**：PEP 8建议在注释中使用完整的句子，并且在注释符号(#)后面使用一个空格。

7. **命名约定**：PEP 8提供了关于如何命名类、函数、方法、变量等的详细指南。例如，类名应该使用驼峰命名法，而函数和方法名应该使用小写字母和下划线。

8. **编程建议**：PEP 8还包括一些编程建议，例如不要使用比较运算符来比较单例，如None。

以上只是PEP 8中的一部分内容，PEP 8的全文包含了更多的细节和例子。尽管PEP 8提供了许多有用的指南，但它并不是强制性的。在某些情况下，为了提高代码的可读性或者为了与旧代码保持一致，可能需要违反PEP 8的规定。
