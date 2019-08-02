---
title: 浏览和转换数据 (Dataset 类)
titleSuffix: Azure Machine Learning service
description: 使用摘要统计信息浏览数据并通过数据清理、转换和特征工程准备数据
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/23/2019
ms.openlocfilehash: f680a1cb15edf0141897c74da3b7c7afa01acae0
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699121"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>利用 Dataset 类浏览和准备数据 (预览版)

了解如何在[AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)中利用 azureml 数据集包浏览和准备数据。 [数据集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)类 (预览版) 可让你通过提供如下所述的功能来浏览和准备你的数据: 采样、摘要统计信息和智能转换。 转换步骤保存在[数据集定义](how-to-manage-dataset-definitions.md)中, 能够以高度可伸缩性的方式处理不同架构的多个大文件。

> [!Important]
> 某些数据集类 (预览版) 与[dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)包 (GA) 有依赖关系。 尽管可以使用 GA'ed[数据准备函数](how-to-transform-data.md)直接执行转换函数, 但如果要生成新的解决方案, 我们建议使用本文中所述的数据集包包装。 Azure 机器学习数据集 (预览版) 可让你不仅转换数据, 而且还允许[快照数据](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py)和存储已[版本控制的数据集定义](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py)。 数据集是数据准备 SDK 的下一个版本, 它提供了用于在 AI 解决方案中管理数据集的扩展功能。

## <a name="prerequisites"></a>先决条件

若要浏览和准备你的数据, 你将需要:

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

* Azure 机器学习服务工作区。 请参阅[创建 Azure 机器学习服务工作区](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)。

* 用于 Python 的 Azure 机器学习 SDK (版本1.0.21 或更高版本), 其中包含 azureml 数据集包。 若要安装或更新到最新版本的 SDK, 请参阅[安装或更新 sdk](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

* Azure 机器学习数据准备 SDK。 若要安装或更新到最新版本, 请参阅[安装或更新数据准备 SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install)。

* 下载下面的示例文件, 其中包含示例:[犯罪 .csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv)和[city](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json)。

## <a name="sampling"></a>采样

获取数据示例, 初步了解你的数据体系结构和内容。 此时, Dataset 类中[`sample()`](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-)的方法支持前 N 个、简单的随机和分层采样策略。

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>Top N 示例

对于前 N 个采样, 你的数据集的前 n 个记录是你的示例。 如果你只是想要了解数据记录的外观, 或查看数据中包含的字段, 这会很有帮助。

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||id|案例号|Date|阻止|IUCR|主要类型|...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|欺骗性做法|...
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD|890|THEFT|...
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO|1154|欺骗性做法|...
3|10519591|HZ261534|4/15/2016 9:00|113XX S PRAIRIE|1120|欺骗性做法|...
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE 平均|890|THEFT|...

### <a name="simple-random-sample"></a>简单随机示例

在简单随机采样中, 数据填充的每个成员都有可能被选为示例的一部分。 `simple_random`在示例策略中, 根据指定的概率选择数据集中的记录, 并返回修改后的数据集。 Seed 参数是可选的。

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||id|案例号|Date|阻止|IUCR|主要类型|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD|890|THEFT|...
1|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO|1154|欺骗性做法|...
2|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE 平均|890|THEFT|...

### <a name="stratified-sample"></a>分层示例

分层示例确保在示例中表示总体的某些组。 在示例策略中, 根据相似性将总体划分为阶层或子组, 并且根据`fractions`参数指示的阶层权重, 从每个阶层中随机选择记录。 `stratified`

在下面的示例中, 我们按指定的列对每个记录进行分组, 并在中`fractions`包括基于阶层 X 权重信息的已说记录。 如果未指定阶层或无法对记录分组, 则示例的默认权重为0。

```Python
# take 50% of records with `Primary Type` as `THEFT` and 20% of records with `Primary Type` as `DECEPTIVE PRACTICE` into sample Dataset
fractions = {}
fractions[('THEFT',)] = 0.5
fractions[('DECEPTIVE PRACTICE',)] = 0.2

sample_dataset = dataset.sample('stratified', {'columns': ['Primary Type'], 'fractions': fractions, 'seed': seed})

sample_dataset.to_pandas_dataframe()
```

||id|案例号|Date|阻止|IUCR|主要类型|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD|890|THEFT|...
1|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE 平均|890|THEFT|...
2|10535059|HZ278872|4/15/2016 4:30|004XX S KILBOURN|810|THEFT|...

## <a name="explore-with-summary-statistics"></a>浏览汇总统计信息

 与[`get_profile()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-)方法一起检测异常、缺失值或错误计数。 此函数获取数据的配置文件和摘要统计信息, 这反过来有助于确定要应用的必要数据准备操作。

```Python
dataset.get_profile()
```

||type|Min|Max|Count|缺失计数|非缺失计数|缺失百分比|错误计数|空计数|0.1% 分位|1% 分位|5% 分位|25% 分位|50% 分位|75% 分位|95% 分位|99% 分位|99.9% 分位|平均值|标准偏差|Variance|倾斜|峰度
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType.INTEGER|1.04986e+07|1.05351e+07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e+07|1.05259e+07|1.05351e+07|1.05351e+07|1.05351e+07|1.05195e+07|12302.7|1.51358e+08|-0.495701|-1.02814
案例号|FieldType.STRING|HZ239907|HZ278872|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Date|FieldType.DATE|2016-04-04 23:56:00 + 00:00|2016-04-15 17:00:00 + 00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
阻止|FieldType.STRING|004XX S KILBOURN|113XX S PRAIRIE|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10.0|0.0|10.0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
主要类型|FieldType.STRING|欺骗性做法|THEFT|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
描述|FieldType.STRING|虚假检查|超过 $500|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
地址说明|FieldType.STRING||学校, 公共, 大楼|10.0|0.0|10.0|0.0|0.0|1.0||||||||||||||
逮捕|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
国内|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
信号|FieldType.INTEGER|531|2433|10.0|0.0|10.0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
区|FieldType.INTEGER|5|24|10.0|0.0|10.0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6.94822|48.2778|0.0930109|-1.62325
病房|FieldType.INTEGER|1|48|10.0|0.0|10.0|0.0|0.0|0.0|1|5|1|9|22.5|40|48|48|48|24.5|16.2635|264.5|0.173723|-1.51271
社区范围|FieldType.INTEGER|4|77|10.0|0.0|10.0|0.0|0.0|0.0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379
FBI 代码|FieldType.INTEGER|6|11|10.0|0.0|10.0|0.0|0.0|0.0|6|6|6|6|11|11|11|11|11|9.4|2.36643|5.6|-0.702685|-1.59582
X 坐标|FieldType.INTEGER|1.16309e+06|1.18336e+06|10.0|7.0|3.0|0.7|0.0|0.0|1.16309e+06|1.16309e+06|1.16309e+06|1.16401e+06|1.16678e+06|1.17921e+06|1.18336e+06|1.18336e+06|1.18336e+06|1.17108e+06|10793.5|1.165e+08|0.335126|-2.33333
Y 坐标|FieldType.INTEGER|1.8315e+06|1.908e+06|10.0|7.0|3.0|0.7|0.0|0.0|1.8315e+06|1.8315e+06|1.8315e+06|1.83614e+06|1.85005e+06|1.89352e+06|1.908e+06|1.908e+06|1.908e+06|1.86319e+06|39905.2|1.59243e+09|0.293465|-2.33333
年|FieldType.INTEGER|2016|2016|10.0|0.0|10.0|0.0|0.0|0.0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
更新时间|FieldType.DATE|2016-05-11 15:48:00 + 00:00|2016-05-27 15:45:00 + 00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
纬度|FieldType.DECIMAL|41.6928|41.9032|10.0|7.0|3.0|0.7|0.0|0.0|41.6928|41.6928|41.6928|41.7057|41.7441|41.8634|41.9032|41.9032|41.9032|41.78|0.109695|0.012033|0.292478|-2.33333
经度|FieldType.DECIMAL|-87.6764|-87.6043|10.0|7.0|3.0|0.7|0.0|0.0|-87.6764|-87.6764|-87.6764|-87.6734|-87.6645|-87.6194|-87.6043|-87.6043|-87.6043|-87.6484|0.0386264|0.001492|0.344429|-2.33333
Location|FieldType.STRING||(41.903206037,-87.676361925)|10.0|0.0|10.0|0.0|0.0|7.0||||||||||||||

## <a name="impute-missing-values"></a>估算缺失值

在数据集、null 值、NaN 的和不包含任何内容的值都被视为缺少值。 这可能会影响机器学习模型的性能, 也可能导致无效结论。 插补法是解决缺失值的一种常用方法, 当您的缺失值记录百分比超出您不能删除的情况时, 此方法非常有用。

在上一节中生成的数据集配置文件中, `Latitude`我们`Longitude`看到和列的缺失值百分比较高。 在此示例中, 我们计算这两个列的平均值和归结缺失值。

首先, 获取数据集[`get_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-)的最新定义, 并将数据与[`keep_columns()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#keep-columns-columns--multicolumnselection--validate-column-exists--bool---false-----azureml-dataprep-api-dataflow-dataflow)削减, 这样我们只需查看要解决的列。

```Python
from azureml.core.dataset import Dataset
import azureml.dataprep as dprep

# get the latest definition of Dataset
ds_def = dataset.get_definition()

# keep useful columns for this example
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
ds_def.head(3)
```

||id|逮捕| 纬度|经度|
-|---------|-----|---------|----------|
|0|10498554|False|41.692834|-87.604319|
|1|10516598|False| 41.744107 |-87.664494|
|2|10519196|False| NaN|NaN|

接下来, [`summarize()`](/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-ml-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow--summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow)使用`MEAN`函数检查纬度列的值。 此函数接受 `group_by_columns` 参数中的列数组以指定聚合级别。 参数接受函数, 该函数指定当前列名称、新的计算字段名称和要执行的`SummaryFunction`。 `summary_columns` `SummaryColumnsValue`

```Python
lat_mean = ds_def.summarize(group_by_columns = ['Arrest'],
                            summary_columns = [dprep.SummaryColumnsValue(column_id = 'Latitude',
                                                                         summary_column_name = 'Latitude_MEAN',
                                                                         summary_function = dprep.SummaryFunction.MEAN)])
lat_mean = lat_mean.filter(lat_mean['Arrest'] == False)
lat_mean.head(1)
```

||逮捕|Latitude_MEAN|
--|-----|--------|
|0|False|41.780049|

验证归结值后, 使用[`ImputeMissingValuesBuilder`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py)来了解使用计算`MIN`、 `MAX`、 `MEAN`值或`CUSTOM`值 imputes 列的固定表达式。 如果指定 `group_by_columns`，将使用每组计算得出的 `MIN`、`MAX` 和 `MEAN` 按组估算缺失值。

接受 column_id 字符串, `ReplaceValueFunction`并指定归结类型。 [`ImputeColumnArguments`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) 对于缺少的经度值, 根据外部知识将其归结为-87。

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

可以`ImputeMissingValuesBuilder` [使用`Builders`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py)类[函数`impute_missing_values()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder)将归结步骤链接到一个对象。 `impute_columns` 参数接受 `ImputeColumnArguments` 对象的数组。

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

调用函数以存储归结步骤, 并使用[`to_dataflow()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow)将这些步骤应用到数据流对象。 [`learn()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none)

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

如以下输出表中所示, 缺少的纬度与`MEAN` `Arrest==False`组的值数据估算, 缺少的经度数据估算为-87。

||id|逮捕|纬度|经度
-|---------|-----|---------|----------
0|10498554|False|41.692834|-87.604319
1|10516598|False|41.744107|-87.664494
2|10519196|False|41.780049|-87.000000

用更新数据集定义, [`update_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-)以保留执行的转换步骤。

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||id|逮捕|纬度|经度
-|---------|-----|---------|----------
0|10498554|False|41.692834|-87.604319
1|10516598|False|41.744107|-87.664494
2|10519196|False|41.780049|-87.000000

## <a name="create-assertion-rules"></a>创建断言规则

通常, 在清理和准备数据时使用的数据只是生产所需的总数据的一个子集。 因此, 我们在清理过程中所做的一些假设可能会成为 false。 例如, 在连续更新的数据集中, 最初只包含一定范围内的数字的列可能会在以后的执行中包含更大范围的值。 这些错误通常会导致管道损坏或数据损坏。

数据集支持对数据创建断言, 这是在管道执行时计算的。 这些断言使我们能够验证我们对数据的假设是否继续准确, 如果不是, 则会相应地处理故障。

例如, 如果要将数据集中的`Latitude`和`Longitude`值限制为特定的数值范围, 则[`assert_value()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow)方法可确保这种情况总是如此。

```Python
from azureml.dataprep import value
from azureml.core.dataset import Dataset

# get the latest definition of the Dataset
ds_def = dataset.get_definition()

# set assertion rules for `Latitude` and `Longitude` columns
ds_def = ds_def.assert_value('Latitude', (value <= 90) & (value >= -90), error_code='InvalidLatitude')
ds_def = ds_def.assert_value('Longitude', (value <= 180) & (value >= -87), error_code='InvalidLongitude')

ds_def.get_profile()
```

||type|Min|Max|Count|缺失计数|非缺失计数|缺失百分比|错误计数|空计数|0.1% 分位|1% 分位|5% 分位|25% 分位|50% 分位|75% 分位|95% 分位|99% 分位|99.9% 分位|平均值|标准偏差|Variance|倾斜|峰度
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType.INTEGER|1.04986e+07|1.05351e+07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e+07|1.05259e+07|1.05351e+07|1.05351e+07|1.05351e+07|1.05195e+07|12302.7|1.51358e+08|-0.495701|-1.02814
逮捕|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
纬度|FieldType.DECIMAL|41.6928|41.9032|10.0|0.0|10.0|0.0|0.0|0.0|41.6928|41.7185|41.6928|41.78|41.78|41.78|41.9032|41.9032|41.9032|41.78|0.0517107|0.002674|0.837593|1.05
经度|FieldType.INTEGER|-87|-87|10.0|0.0|10.0|0.0|3.0|0.0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|NaN|NaN

从配置文件中可以看到, `Error Count` `Longitude`列的为3。 下面的代码筛选数据集、检索错误, 并查看导致断言失败的值。 从此处调整代码并适当地清理数据。

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>按示例派生列

数据集的更高级工具之一是能够使用所需结果的示例来派生列。 这使你可以为 SDK 生成一个示例, 因此它可以生成代码以实现预期的转换。

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||id|案例号|Date|阻止|...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD|...
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO|...

假设需要将日期和时间格式转换为 "2016-04-04 10PM-12AM"。 在参数中, 按以下格式在`example_data`参数中提供所需输出的示例: *(原始输出、所需输出)* 。 [`derive_column_by_example()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow)

以下代码提供了两个所需输出示例 ("2016-04-04 23:56:00"、"2016-04-04 10PM-12AM") 和 ("2016-04-15 17:00:00"、"2016-04-15 下午4点-下午")

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

在下表中, 请注意, 新列 Date_Time_Range 包含指定格式的记录。

||id|Date|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10PM-12AM
1|10516598|2016-04-15 17:00:00|2016-04-15 4PM-6PM
2|10519196|2016-04-15 10:00:00|2016-04-15 10AM-12PM

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>模糊分组

从不同源收集数据时, 您可能会遇到相同实体的拼写、大写或缩写。 通过 SDK 的模糊分组或文本聚类功能, 自动标准化和协调这些变体。

例如, 列`inspections.business.city`包含多个城市名称为 "旧金山" 的形式。

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|...|
-|-----|-------------------------|------------|--|---
0|16162|快速 Ezee 印度食物|3861 24 日 St|SF|...
1|67565|泰语面条咖啡馆的王|1541 TARAVAL St|旧金山|...
2|67565|泰语面条咖啡馆的王|1541 TARAVAL St|旧金山|...
3|68701|Grindz|832 Clement St|SF|...
4|69186|Premier 餐 & 事件, Inc。|1255 22 日 St|S.F.|...

让我们使用[`fuzzy_group_column()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow)方法添加具有自动检测到的规范形式的 "旧金山" 的列。 参数`source_column` 和`new_column_name`是必需的。 你还可以选择执行以下操作:

* 创建一个新列`similarity_score_column_name`, 其中显示了每对原始值和规范值之间的相似性分数。

* 提供一个`similarity_threshold`, 它是要组合在一起的值的最低相似性得分。

```Python
# get the latest Dataset definition
ds_def = dataset.get_definition()
ds_def = ds_def.fuzzy_group_column(source_column='inspections.business.city',
                                       new_column_name='city_grouped',
                                       similarity_threshold=0.8,
                                       similarity_score_column_name='similarity_score')
ds_def.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|city_grouped|similarity_score|...|
-|-----|-------------------------|------------|--|---|---|---
0|16162|快速 Ezee 印度食物|3861 24 日 St|SF|旧金山|0.814806|...
1|67565|泰语面条咖啡馆的王|1541 TARAVAL St|旧金山|旧金山|1.000000|...
2|67565|泰语面条咖啡馆的王|1541 TARAVAL St|旧金山|旧金山|1.000000|...
3|68701|Grindz|832 Clement St|SF|旧金山|0.814806|...
4|69186|Premier 餐 & 事件, Inc。|1255 22 日 St|S.F.|旧金山|0.814806|...

在生成的数据集定义中, 表示数据中 "旧金山" 的所有不同变体被规范化为同一字符串 "旧金山"。

将此模糊分组步骤保存到最新的数据`update_definition()`集定义中。

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>后续步骤

* 有关回归模型示例, 请参阅自动机器学习[教程](tutorial-auto-train-models.md)。

* 有关设计模式和用法示例, 请参阅 SDK[概述](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。

* 有关使用数据集的示例, 请参阅[示例笔记本](https://aka.ms/dataset-tutorial)。
