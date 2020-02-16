---
title: 将机器学习试验代码转换为生产代码
titleSuffix: Azure Machine Learning
description: 了解如何使用 MLOpsPython 代码模板将机器学习试验代码转换为生产代码。
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 1be97f9754733561111cedcf95fe237b7788583e
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122552"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>教程：将机器学习试验代码转换为生产代码

机器学习项目要求提供试验，在其中可以使用真实数据集通过 Jupyter Notebook 等灵活工具测试假设条件。 准备好将模型投入生产后，应将模型代码放在生产代码存储库中。 在某些情况下，模型代码必须转换为 Python 脚本才能放到生产代码存储库中。 本教程提供有关如何将试验代码导出到 Python 脚本的建议方法。  

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 清理不必要的代码
> * 将 Jupyter Notebook 代码重构为函数
> * 为相关任务创建 Python 脚本
> * 创建单元测试

## <a name="prerequisites"></a>必备条件

- 生成 [MLOpsPython 模板](https://github.com/microsoft/MLOpsPython/generate)并使用 `experimentation/Diabetes Ridge Regression Training.ipynb` 和 `experimentation/Diabetes Ridge Regression Scoring.ipynb` 笔记本。 从试验代码转换为生产代码时，这些笔记本将用作示例。
- 安装 nbconvert。 请仅按照[安装](https://nbconvert.readthedocs.io/en/latest/install.html)页上的“安装 nbconvert”部分中的安装说明进行操作。 

## <a name="remove-all-nonessential-code"></a>删除所有不必要的代码

试验期间编写的某些代码仅用于探索目的。 因此，将试验代码转换为生产代码的第一步是删除这些不必要的代码。 删除不必要的代码还使代码更易于维护。 在本部分，你将从 `experimentation/Diabetes Ridge Regression Training.ipynb` 笔记本中删除代码。 输出 `X` 和 `y` 形状的语句以及调用 `features.describe` 的单元仅用于数据探索目的，可将其删除。 删除不必要的代码后，`experimentation/Diabetes Ridge Regression Training.ipynb` 应类似于以下代码（不包括 Markdown）：

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
 
X, y = load_diabetes(return_X_y=True)

X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

alpha = 0.5

reg = Ridge(alpha=alpha)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg.predict(data["test"]["X"])
print("mse", mean_squared_error(preds, data["test"]["y"]))

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>将代码重构为函数

第二步，需要将 Jupyter 代码重构为函数。 将代码重构为函数可使单元测试变得更容易，并使代码更易于维护。 在本部分，你将重构：
- 糖尿病岭回归训练笔记本 (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- 糖尿病岭回归评分笔记本 (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>将“糖尿病岭回归训练”笔记本重构为函数
在 `experimentation/Diabetes Ridge Regression Training.ipynb` 中完成以下步骤：

1. 创建名为 `train_model` 的函数，该函数采用 `data` 和 `alpha` 参数并返回一个模型。 
1. 将标题“基于训练集训练模型”和“基于验证集验证模型”下的代码复制到 `train_model` 函数中。

`train_model` 函数应类似于以下代码：

```python
def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg
```

创建 `train_model` 函数后，将标题“基于训练集训练模型”和“基于验证集验证模型”下的代码替换为以下语句：

```python
reg = train_model(data, alpha)
```

上述语句调用传递 `data` 和 `alpha` 参数的 `train_model` 函数并返回模型。

在 `experimentation/Diabetes Ridge Regression Training.ipynb` 中完成以下步骤：

1. 创建名为 `main` 的新函数，该函数不采用任何参数，且不返回任何内容。
1. 将标题“加载数据”、“将数据拆分为训练集和验证集”和“保存模型”下的代码复制到 `main` 函数中。
1. 将新建的 `train_model` 调用复制到 `main` 函数中。

`main` 函数应类似于以下代码：

```python
def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)
```

创建 `main` 函数后，将标题“加载数据”、“将数据拆分为训练集和验证集”和“保存模型”下的代码以及新建的 `train_model` 调用替换为以下语句：

```python
main()
```

重构后，`experimentation/Diabetes Ridge Regression Training.ipynb` 应类似于以下代码（不包括 Markdown）：

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>将“糖尿病岭回归评分”笔记本重构为函数
在 `experimentation/Diabetes Ridge Regression Scoring.ipynb` 中完成以下步骤：

1. 创建名为 `init` 的新函数，该函数不采用任何参数，且不返回任何内容。
1. 将“加载模型”标题下的代码复制到 `init` 函数中。

`init` 函数应类似于以下代码：

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

创建 `init` 函数后，将标题“加载模型”下的所有代码替换为单个 `init` 调用，如下所示：

```python
init()
```

在 `experimentation/Diabetes Ridge Regression Scoring.ipynb` 中完成以下步骤：

1. 创建名为 `run` 的新函数，该函数采用 `raw_data` 和 `request_headers` 作为参数，并返回结果字典，如下所示：

    ```python
    {"result": result.tolist()}
    ```

1. 将“准备数据”和“评分数据”标题下的代码复制到 `run` 函数中。

    `run` 函数应类似于以下代码（请记得删除设置变量 `raw_data` 和 `request_headers` 的语句，稍后在调用 `run` 函数时将使用这些变量）：

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

创建 `run` 函数后，将“准备数据”和“评分数据”标题下的所有代码替换为以下代码：

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```
上述代码设置变量 `raw_data` 和 `request_header`，使用 `raw_data` 和 `request_header` 调用 `run` 函数，并输出预测结果。

重构后，`experimentation/Diabetes Ridge Regression Scoring.ipynb` 应类似于以下代码（不包括 Markdown）：

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>在 Python 文件中合并相关函数
第三步，需要将相关函数合并到 Python 文件中，以方便重复使用代码。 在本部分，你将为以下笔记本创建 Python 文件：
- 糖尿病岭回归训练笔记本 (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- 糖尿病岭回归评分笔记本 (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>为“糖尿病岭回归训练”笔记本创建 Python 文件
在命令提示符下运行以下使用 nbconvert 包和 `experimentation/Diabetes Ridge Regression Training.ipynb` 路径的语句，将笔记本转换为可执行脚本：

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

将笔记本转换为 `train.py` 后，删除所有注释。 `train.py` 文件应类似于以下代码：

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():
    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)

main()
```

MLOpsPython 存储库的 `diabetes_regression/training` 目录中的 `train.py` 文件支持命令行参数（即 `build_id`、`model_name` 和 `alpha`）。 可将命令行参数支持添加到 `train.py` 文件，以支持动态模型名称和 `alpha` 值，但不添加此项支持也能使代码成功执行。

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>为“糖尿病岭回归评分”笔记本创建 Python 文件
在命令提示符下运行以下使用 nbconvert 包和 `experimentation/Diabetes Ridge Regression Scoring.ipynb` 路径的语句，将笔记本转换为可执行脚本：

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

将笔记本转换为 `score.py` 后，删除所有注释。 `score.py` 文件应类似于以下代码：

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

需要修改 `train_model` 函数来实例化全局变量模型，使该变量在整个脚本中可见。 在 `init` 函数的开头添加以下语句：

```python
global model
```

添加上述语句后，`init` 函数应类似于以下代码：

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>为每个 Python 文件创建单元测试
第四步，需要为每个 Python 文件创建单元测试，使代码更可靠且更易于维护。 在本部分，你将为 `train.py` 中的某个函数创建单元测试。

`train.py` 包含两个函数：`train_model` 和 `main`。 每个函数都需要一个单元测试，但在本教程中，我们只使用 Pytest 框架为 `train_model` 函数创建一个单元测试。  Pytest 不是仅有的一个 Python 单元测试框架，但它是最常用的框架之一。 有关详细信息，请访问 [Pytest](https://pytest.org)。

单元测试通常包含三个主要操作：
- 排列对象 - 创建并设置所需的对象
- 处理对象
- 断言预期结果

传递 `data` 和 `alpha` 值时，通常需要断言 `train_model`。 预期结果是应调用 `Ridge.train` 和 `Ridge.predict` 函数。 由于机器学习训练方法的运行速度通常不快，因此我们将模拟调用 `Ridge.train`。 由于 `Ridge.train` 的返回值是模拟对象，因此我们还将模拟 `Ridge.predict`。 结合通过模拟和 Pytest 框架调用的 `Ridge.train` 和 `Ridge.predict` 函数的预期结果测试传递 `data` 和 `alpha` 值的 `train_model` 单元测试应类似于以下代码：

```python
import pytest
from code.training.train import train_model

class TestTrain:

    @staticmethod
    def test_train_model(mocker):
        # Arrange
        test_data = {"train": {"X": [[1, 2, 3]], "y": [0]},
                     "test": {"X": [[4, 5, 6]], "y": [0]}}
        test_alpha = 0.5
        mock_ridge_fit = mocker.patch('Ridge.fit')
        mock_ridge_predict = mocker.patch(Ridge.predict')

        # Act
        train_model(test_data, test_alpha)

        # Assert
        mock_ridge_fit.assert_called()
        mock_ridge_predict.assert_called()
```

## <a name="use-your-own-model-with-mlopspython-code-template"></a>将自己的模型与 MLOpsPython 代码模板配合使用
如果你前面一直在遵循本指南中的步骤，则现在已获得一组与 MLOpsPython 存储库中提供的训练/评分/测试脚本相关联的脚本。  根据前面所述的结构，下面逐步说明了需要执行哪些操作，才能对你自己的机器学习项目使用这些文件：  

1.  遵循入门指南
2.  替换训练代码
3.  替换评分代码
4.  更新评估代码

### <a name="follow-the-getting-started-guide"></a>遵循入门指南
需要遵循入门指南来获取用于执行 MLOpsPython 的支持性基础结构和管道。  我们建议先部署原有的 MLOpsPython 代码，然后再放入自己的代码，以确保结构和管道工作正常。  熟悉存储库的代码结构也很有帮助。

### <a name="replace-training-code"></a>替换训练代码
要使解决方案能够与你自己的代码配合工作，需要替换用于训练模型的代码，并删除或替换相应的单元测试。  具体而言，需要执行以下步骤：

1. 替换 `diabetes_regression\training\train.py`。 此脚本在本地或 Azure 机器学习计算中训练模型。
1. 删除或替换 `tests/unit/code_test.py` 中的训练单元测试

### <a name="replace-score-code"></a>替换评分代码
要使模型能够提供实时推理功能，需要替换评分代码。 MLOpsPython 模板使用评分代码来部署模型，以针对 ACI、AKS 或 Web 应用执行实时评分。  若要保留评分，请替换 `diabetes_regression/scoring/score.py`。

### <a name="update-evaluation-code"></a>更新评估代码
MLOpsPython 模板使用 evaluate_model 脚本根据均方根误差来比较最近已训练模型的性能和当前生产模型的性能。 如果最近已训练模型的性能优于当前生产模型，则管道将继续运行。 否则，管道将会停止。 若要保留评估，请将 `diabetes_regression/evaluate/evaluate_model.py` 中 `mse` 的所有实例替换为所需的指标。 

若要消除评估，请将 `.pipelines\diabetes_regression-variables` 中的 DevOps 管道变量 `RUN_EVALUATION` 设置为 `false`。

## <a name="next-steps"></a>后续步骤

了解如何从试验代码转换为生产代码后，请使用以下链接来了解如何监视试验运行以及作为 Web 服务部署的模型：

> [!div class="nextstepaction"]
> [监视 Azure 机器学习试验运行和指标](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
> [监视机器学习 Web 服务终结点并从中收集数据](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insight)
