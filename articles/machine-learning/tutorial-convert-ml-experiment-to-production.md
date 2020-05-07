---
title: 将笔记本代码转换为 Python 脚本
titleSuffix: Azure Machine Learning
description: 使用 MLOpsPython 代码模板将机器学习试验笔记本转换为生产就绪代码。 然后，可以测试、部署和自动执行该代码。
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 04/30/2020
ms.openlocfilehash: a0b66f233de9e1bfdc6d011b65489884a1049a12
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559677"
---
# <a name="tutorial-convert-ml-experiments-to-production-python-code"></a>教程：将 ML 试验转换为生产 Python 代码

本教程介绍如何使用 MLOpsPython 代码模板和 Azure 机器学习，将 Juptyer 笔记本转换为 Python 脚本，更方便地进行测试和自动化操作。 通常，此过程用于从 Juptyer 笔记本获取试验/训练代码，并将其转换为 Python 脚本。 然后，可以在生产环境中使用这些脚本进行测试和 CI/CD 自动化。 

机器学习项目要求提供试验，在其中可以使用真实数据集通过 Jupyter Notebook 等灵活工具测试假设条件。 准备好将模型投入生产后，应将模型代码放在生产代码存储库中。 在某些情况下，模型代码必须转换为 Python 脚本才能放到生产代码存储库中。 本教程提供有关如何将试验代码导出到 Python 脚本的建议方法。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 清理不必要的代码
> * 将 Jupyter Notebook 代码重构为函数
> * 为相关任务创建 Python 脚本
> * 创建单元测试

## <a name="prerequisites"></a>先决条件

- 生成 [MLOpsPython 模板](https://github.com/microsoft/MLOpsPython/generate)并使用 `experimentation/Diabetes Ridge Regression Training.ipynb` 和 `experimentation/Diabetes Ridge Regression Scoring.ipynb` 笔记本。 从试验代码转换为生产代码时，这些笔记本将用作示例。 可在 [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation) 找到这些笔记本。
- 安装 `nbconvert`。 请仅按照[安装](https://nbconvert.readthedocs.io/en/latest/install.html)页上的“安装 nbconvert”部分中的安装说明进行操作。 

## <a name="remove-all-nonessential-code"></a>删除所有不必要的代码

试验期间编写的某些代码仅用于探索目的。 因此，将试验代码转换为生产代码的第一步是删除这些不必要的代码。 删除不必要的代码还使代码更易于维护。 在本部分，你将从 `experimentation/Diabetes Ridge Regression Training.ipynb` 笔记本中删除代码。 输出 `X` 和 `y` 形状的语句以及调用 `features.describe` 的单元仅用于数据探索目的，可将其删除。 删除不必要的代码后，`experimentation/Diabetes Ridge Regression Training.ipynb` 应类似于以下代码（不包括 Markdown）：

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
import pandas as pd

sample_data = load_diabetes()

df = pd.DataFrame(
    data=sample_data.data,
    columns=sample_data.feature_names)
df['Y'] = sample_data.target

X = df.drop('Y', axis=1).values
y = df['Y'].values

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

args = {
    "alpha": 0.5
}

reg_model = Ridge(**args)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg_model.predict(data["test"]["X"])
mse = mean_squared_error(preds, y_test)
metrics = {"mse": mse}
print(metrics)

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>将代码重构为函数

第二步，需要将 Jupyter 代码重构为函数。 将代码重构为函数可使单元测试变得更容易，并使代码更易于维护。 在本部分，你将重构：

- 糖尿病岭回归训练笔记本 (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- 糖尿病岭回归评分笔记本 (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>将“糖尿病岭回归训练”笔记本重构为函数

在 `experimentation/Diabetes Ridge Regression Training.ipynb` 中完成以下步骤：

1. 创建名为 `split_data` 的函数，以将数据帧拆分为测试数据和训练数据。 该函数应采用数据帧 `df` 作为参数，并返回包含键 `train` 和 `test` 的字典。

    将“Split Data into Training and Validation Sets”标题下的代码移到 `split_data` 函数中，并修改该函数以返回 `data` 对象。 

1. 创建名为 `train_model` 的函数，该函数采用 `data` 和 `args` 参数并返回已训练的模型。

    将“Training Model on Training Set”标题下的代码移到 `train_model` 函数中，并修改该函数以返回 `reg_model` 对象。  删除 `args` 字典，值将来自 `args` 参数。

1. 创建名为 `get_model_metrics` 的函数（该函数采用参数 `reg_model` 和 `data`），评估模型，然后返回已训练模型的指标字典。

    将“Validate Model on Validation Set”标题下的代码移到 `get_model_metrics` 函数中，并修改该函数以返回 `metrics` 对象。 

这三个函数应如下所示：

```python
# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics
```

仍在 `experimentation/Diabetes Ridge Regression Training.ipynb` 中，完成以下步骤：

1. 创建名为 `main` 的新函数，该函数不采用任何参数，且不返回任何内容。
1. 将“Load Data”标题下的代码移到 `main` 函数中。
1. 将新编写的函数的调用添加到 `main` 函数中：
    ```python
    # Split Data into Training and Validation Sets
    data = split_data(df)
    ```

    ```python
    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)
    ```

    ```python
    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)
    ```
1. 将“Save Model”标题下的代码移到 `main` 函数中。

`main` 函数应类似于以下代码：

```python
def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)
```

到此阶段，除了第一个单元中的 import 语句之外，笔记本中不应存在不在函数中的代码。

添加一个调用 `main` 函数的语句。

```python
main()
```

重构后，`experimentation/Diabetes Ridge Regression Training.ipynb` 应类似于以下代码（不包括 Markdown）：

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

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

在命令提示符下运行以下使用 `nbconvert` 包和 `experimentation/Diabetes Ridge Regression Training.ipynb` 路径的语句，将笔记本转换为可执行脚本：

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

将笔记本转换为 `train.py` 后，删除任何不需要的注释。 将位于文件末尾的 `main()` 调用替换为如以下代码所示的条件调用：

```python
if __name__ == '__main__':
    main()
```

`train.py` 文件应类似于以下代码：

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

if __name__ == '__main__':
    main()
```

现在，可以在终端中运行 `python train.py` 来调用 `train.py`。
还可以从其他文件调用 `train.py` 中的函数。

位于 MLOpsPython 存储库的 `diabetes_regression/training` 目录中的 `train_aml.py` 文件将调用在 Azure 机器学习试验运行上下文中的 `train.py` 中定义的函数。 还可以在单元测试中调用这些函数，本指南稍后将提供相关介绍。

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>为“糖尿病岭回归评分”笔记本创建 Python 文件

在命令提示符下运行使用 `nbconvert` 包和 `experimentation/Diabetes Ridge Regression Scoring.ipynb` 路径的以下语句，将笔记本转换为可执行脚本：

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

将笔记本转换为 `score.py` 后，删除任何不需要的注释。 `score.py` 文件应类似于以下代码：

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

`model` 变量需是全局性的，以便在整个脚本中可见。 在 `init` 函数的开头添加以下语句：

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

第四步，为 Python 函数创建单元测试。 单元测试可以避免代码遇到功能回归问题，并使其更易于维护。 在本部分中，你将为 `train.py` 中的函数创建单元测试。

`train.py` 包含多个函数，但在本教程中，我们只使用 Pytest 框架为 `train_model` 函数创建一个单元测试。 Pytest 不是仅有的一个 Python 单元测试框架，但它是最常用的框架之一。 有关详细信息，请访问 [Pytest](https://pytest.org)。

单元测试通常包含三个主要操作：

- 排列对象 - 创建并设置所需的对象
- 处理对象
- 断言预期结果

单元测试将调用 `train_model` 并使用某些硬编码的数据和参数，它验证 `train_model` 的运行方式是否符合预期，其验证方法是使用生成的已训练模型进行预测，并将预测结果与预期值进行比较。

```python
import numpy as np
from code.training.train import train_model


def test_train_model():
    # Arrange
    X_train = np.array([1, 2, 3, 4, 5, 6]).reshape(-1, 1)
    y_train = np.array([10, 9, 8, 8, 6, 5])
    data = {"train": {"X": X_train, "y": y_train}}

    # Act
    reg_model = train_model(data, {"alpha": 1.2})

    # Assert
    preds = reg_model.predict([[1], [2]])
    np.testing.assert_almost_equal(preds, [9.93939393939394, 9.03030303030303])
```

## <a name="next-steps"></a>后续步骤

现在，你已了解了如何从试验代码转换为生产代码，接下来请参阅以下链接来了解详细信息和后续步骤：

+ [MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md)：构建一个 CI/CD 管道，以使用 Azure Pipelines 和 Azure 机器学习来训练、评估和部署自己的模型
+ [监视 Azure ML 试验运行和指标](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
+ [监视 ML Web 服务终结点并从中收集数据](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
