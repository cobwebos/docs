---
title: '如何使用 WhiteNoise 包 (预览保留数据隐私) '
titleSuffix: Azure Machine Learning
description: 了解如何使用 WhiteNoise 包将有关差分隐私的最佳做法应用于 Azure 机器学习模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: slbird
author: slbird
ms.reviewer: luquinta
ms.date: 07/09/2020
ms.openlocfilehash: 355d96fe5a617effab89fbd038f7f1785215f88f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90897682"
---
# <a name="use-differential-privacy-in-azure-machine-learning-preview"></a>在 Azure 机器学习 (预览版中使用差异隐私) 



了解如何使用 WhiteNoise Python 包将有关差分隐私的最佳做法应用于 Azure 机器学习模型。

差分隐私是隐私的最高标准定义。 遵守此隐私定义的系统可针对各种数据重建和重新识别攻击（包括拥有辅助信息的攻击者发起的攻击）提供强大的防护保障。 详细了解[差分隐私的工作原理](./concept-differential-privacy.md)。

> [!NOTE]
> 请注意，我们正在重命名该工具包，并将在未来几周内引入新名称。 

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。
- [Python 3](https://www.python.org/downloads/)

## <a name="install-whitenoise-packages"></a>安装 WhiteNoise 包

### <a name="standalone-installation"></a>独立安装

这些库设计为在分布式 Spark 群集中运行，可以像安装任何其他包一样安装。

以下说明假设已将 `python` 和 `pip` 命令映射到 `python3` 和 `pip3`。

使用 pip 安装 [WhiteNoise Python 包](https://pypi.org/project/opendp-whitenoise/)。

`pip install opendp-whitenoise`

若要验证是否安装了这些包，请启动 Python 提示并键入：

```python
import opendp.whitenoise.core
import opendp.whitenoise.sql
```

如果导入成功，则表示这些库已安装且可供使用。

### <a name="docker-image"></a>Docker 映像

还可以将 WhiteNoise 包与 Docker 配合使用。

拉取 `opendp/whitenoise` 映像，以在包含 Spark、Jupyter 和示例代码的 Docker 容器中使用这些库。

```sh
docker pull opendp/whitenoise:privacy
```

拉取映像后，启动 Jupyter 服务器：

```sh
docker run --rm -p 8989:8989 --name whitenoise-run opendp/whitenoise:privacy
```

这会在 `localhost` 中的端口 `8989` 上使用密码 `pass@word99` 启动 Jupyter 服务器。 假设你已使用上述命令行启动了名为 `whitenoise-privacy` 的容器，现在可以运行以下命令，在 Jupyter 服务器中打开 bash 终端：

```sh
docker exec -it whitenoise-run bash
```

Docker 实例在关闭时会清除所有状态，因此，在运行中的实例上创建的所有笔记本都会丢失。 若要补救此问题，可以在启动容器时将一个本地文件夹装载到该容器：

```sh
docker run --rm -p 8989:8989 --name whitenoise-run --mount type=bind,source=/Users/your_name/my-notebooks,target=/home/privacy/my-notebooks opendp/whitenoise:privacy
```

在 *my-notebooks* 文件夹下创建的任何笔记本都将存储在本地文件系统中。

## <a name="perform-data-analysis"></a>执行数据分析

若要准备差分隐私版本，需要选择数据源、统计信息和一些隐私参数，以指示隐私保护级别。  

此示例引用了“加利福尼亚州公用微观数据”(PUMS)，其中提供了匿名的公民人口统计信息记录：

```python
import os
import sys
import numpy as np
import opendp.whitenoise.core as wn

data_path = os.path.join('.', 'data', 'PUMS_california_demographics_1000', 'data.csv')
var_names = ["age", "sex", "educ", "race", "income", "married", "pid"]
```

在此示例中，我们将计算年龄的平均值和方差。  我们使用 `epsilon` 总数 1.0（epsilon 是隐私参数，将隐私预算分散在我们要计算的两个数量之间）。 详细了解[隐私指标](concept-differential-privacy.md#differential-privacy-metrics)。

```python
with wn.Analysis() as analysis:
    # load data
    data = wn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = wn.dp_mean(data = wn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
    # get variance of age
    age_var = wn.dp_variance(data = wn.cast(data['age'], type="FLOAT"),
                             privacy_usage = {'epsilon': .35},
                             data_lower = 0.,
                             data_upper = 100.,
                             data_n = 1000
                            )
analysis.release()

print("DP mean of age: {0}".format(age_mean.value))
print("DP variance of age: {0}".format(age_var.value))
print("Privacy usage: {0}".format(analysis.privacy_usage))
```

结果如下所示：

```text
DP mean of age: 44.55598845931517
DP variance of age: 231.79044646429134
Privacy usage: approximate {
  epsilon: 1.0
}
```

对于此示例，需要注意几个重要事项。  首先，`Analysis` 对象表示一个数据处理图。  在此示例中，平均值和方差是从同一个源节点计算的。  但是，可以包含更复杂的表达式，用于以任意方式将输入与输出组合在一起。

该分析图包含用于指定年龄下限和上限的 `data_upper` 和 `data_lower` 元数据。  这些值用于精确校准干扰数据，以确保差分隐私。  在一些需要处理离群值或缺失值的情况下也会使用这些值。

最后，该分析图会跟踪支出的总隐私预算。

可以使用库来编写更复杂的分析图，其中包含多种机制、统计信息和实用函数：

| 统计信息    | 机制 | 实用程序  |
| ------------- |------------|------------|
| 计数         | 高斯   | 强制转换       |
| 直方图     | 几何  | 钳位   |
| 平均值          | 拉普拉斯    | 数字化   |
| 分位数     |            | 筛选器     |
| Sum           |            | 插补 |
| 方差/协方差 |      | 转换  |

有关更多详细信息，请参阅 [数据分析笔记本](https://github.com/opendifferentialprivacy/whitenoise-samples/blob/master/analysis/basic_data_analysis.ipynb) 。

## <a name="approximate-utility-of-differentially-private-releases"></a>差分隐私版本的大致效用

由于差分隐私是通过校准干扰数据运行的，因此版本的效用因隐私风险而异。  一般情况下，随着样本大小不断增大，为保护每个个体而需要的干扰数据会变得可忽略不计，但样本大小增大也会显著影响针对单个个体的版本结果准确度。  分析师可以查看版本的准确度信息，以确定该版本的作用如何：

```python
with wn.Analysis() as analysis:
    # load data
    data = wn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = wn.dp_mean(data = wn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
analysis.release()

print("Age accuracy is: {0}".format(age_mean.get_accuracy(0.05)))
```

该操作的结果应如下所示：

```text
Age accuracy is: 0.2995732273553991
```

此示例按上面所述计算平均值，并使用 `get_accuracy` 函数请求 `alpha` 为 0.05 时的准确度。 `alpha` 为 0.05 表示 95% 的间隔，因为在大约 95% 的时间内，发布的值都处于报告的准确度边界内。  在此示例中，报告的准确度为 0.3，即，在大约 95% 的时间内，发布的值将在宽度为 0.6 的间隔内。  不要将此值视为误差线，因为发布的值会按 `alpha` 指定的几率超出报告的准确度范围，而超出范围的值可能会在任一方向超出范围。

分析师可以在 `get_accuracy` 中查询 `alpha` 的不同值，以获取更窄或更宽的置信度间隔，且不会产生额外的隐私成本。

## <a name="generate-a-histogram"></a>生成直方图

内置的 `dp_histogram` 函数基于以下任何数据类型创建差分隐私直方图：

- 连续变量，其中的数字集必须分割为箱
- 布尔或二分变量，只能取两个值
- 分类变量，其中包含作为字符串枚举的相异类别

下面是为连续变量直方图指定箱的 `Analysis` 示例：

```python
income_edges = list(range(0, 100000, 10000))

with wn.Analysis() as analysis:
    data = wn.Dataset(path = data_path, column_names = var_names)

    income_histogram = wn.dp_histogram(
            wn.cast(data['income'], type='int', lower=0, upper=100),
            edges = income_edges,
            upper = 1000,
            null_value = 150,
            privacy_usage = {'epsilon': 0.5}
        )
```

由于个体在直方图箱之间以不相交的方式分区，因此，每个直方图只会产生隐私成本一次，即使直方图包含许多的箱，也是如此。

有关直方图的详细信息，请参阅[直方图笔记本](https://github.com/opendifferentialprivacy/whitenoise-samples/blob/master/analysis/histograms.ipynb)。

## <a name="generate-a-covariance-matrix"></a>生成协方差矩阵

WhiteNoise 通过其 `dp_covariance` 函数提供三种不同的功能：

- 两个向量之间的协方差
- 矩阵的协方差矩阵
- 一对矩阵的互协方差矩阵

下面是计算标量协方差的示例：

```python
with wn.Analysis() as analysis:
    wn_data = wn.Dataset(path = data_path, column_names = var_names)

    age_income_cov_scalar = wn.dp_covariance(
      left = wn.cast(wn_data['age'], 
      type = "FLOAT"), 
      right = wn.cast(wn_data['income'], 
      type = "FLOAT"), 
      privacy_usage = {'epsilon': 1.0},
      left_lower = 0., 
      left_upper = 100., 
      left_n = 1000, 
      right_lower = 0., 
      right_upper = 500_000.,
      right_n = 1000)
```

有关详细信息，请参阅[协方差笔记本](
https://github.com/opendifferentialprivacy/whitenoise-samples/blob/master/analysis/covariance.ipynb)

## <a name="next-steps"></a>后续步骤

- 浏览 [WhiteNoise 示例笔记本](https://github.com/opendifferentialprivacy/whitenoise-samples/tree/master/analysis)。