---
title: 使用 Azure 机器学习数据准备 SDK 转换数据 - Python
description: 了解如何使用 Azure 机器学习数据准备 SDK 转换和清理数据。 转换方法可用于添加列、筛选掉不需要的行或列，以及估算缺失值。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 388957fc5dac5cdab5bee34a4431eaa524e76a48
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999896"
---
# <a name="transform-data-with-the-azure-machine-learning-data-prep-sdk"></a>使用 Azure 机器学习数据准备 SDK 转换数据

为了方便用户清理数据，[Azure 机器学习数据准备 SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py) 提供各种转换方法。 使用这些方法，可以轻松地添加列、筛选掉不需要的行或列，以及估算缺失值。

目前的方法适用于执行以下任务：
- [使用表达式添加列](#add-column-using-expression)
- [估算缺失值](#impute-missing-values)
- [按示例派生列](#derive-column-by-example)
- [筛选](#filtering)
- [自定义 Python 转换](#custom-python-transforms)

## <a name="add-column-using-an-expression"></a>使用表达式添加列

Azure 机器学习数据准备 SDK 包含 `substring` 表达式，可用于根据现有列计算值，再将计算出的值放入新列中。 下面的示例先加载数据，再尝试将输入数据添加到相应列中。

```
import azureml.dataprep as dprep

# loading data
dataflow = dprep.read_csv(path=r'data\crime0-10.csv')
dataflow.head(3)
```

||ID|案例号|日期|街区|IUCR|主要类型|Description|地址说明|逮捕|国内|...|病房|社区范围|FBI 代码|X 坐标|Y 坐标|年龄|更新时间|纬度|经度|位置|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
|0|10140490|HY329907|07/05/2015 11:50:00 PM|050XX N NEWLAND AVE|0820|THEFT|$500 AND UNDER|STREET|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 |12:42:46 PM|41.973309466|-87.800174996|(41.973309466, -87.800174996)|
|1|10139776|HY329265|07/05/2015 11:30:00 PM|011XX W MORSE AVE|0460|BATTERY|SIMPLE|STREET|false|是|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017, -87.65955018)|
|2|10140270|HY329253|07/05/2015 11:20:00 PM|121XX S FRONT AVE|0486|BATTERY|DOMESTIC BATTERY SIMPLE|STREET|false|是|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|



使用 `substring(start, length)` 表达式从“案例号”列中提取前缀，再将相应数据放入新列“案例类别”中。

```
substring_expression = dprep.col('Case Number').substring(0, 2)
case_category = dataflow.add_column(new_column_name='Case Category',
                                    prior_column='Case Number',
                                    expression=substring_expression)
case_category.head(3)
```

||ID|案例号|案例类别|日期|街区|IUCR|主要类型|Description|地址说明|逮捕|...|病房|社区范围|FBI 代码|X 坐标|Y 坐标|年龄|更新时间|纬度|经度|位置|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|HY|07/05/2015 11:50:00 PM|050XX N NEWLAND AVE|0820|THEFT|$500 AND UNDER|STREET|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 |12:42:46 PM|41.973309466|-87.800174996|(41.973309466, -87.800174996)|
|1|10139776|HY329265|HY|07/05/2015 11:30:00 PM|011XX W MORSE AVE|0460|BATTERY|SIMPLE|STREET|false|是|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017, -87.65955018)|
|2|10140270|HY329253|HY|07/05/2015 11:20:00 PM|121XX S FRONT AVE|0486|BATTERY|DOMESTIC BATTERY SIMPLE|STREET|false|是|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|



使用 `substring(start)` 表达式仅从“案例号”列中提取数字，再将相应数据转换为数字数据类型并放入新列“案例 ID”中。
```
substring_expression2 = dprep.col('Case Number').substring(2)
case_id = dataflow.add_column(new_column_name='Case Id',
                              prior_column='Case Number',
                              expression=substring_expression2)
case_id = case_id.to_number('Case Id')
case_id.head(3)
```

||ID|案例号|案例 ID|日期|街区|IUCR|主要类型|Description|地址说明|逮捕|...|病房|社区范围|FBI 代码|X 坐标|Y 坐标|年龄|更新时间|纬度|经度|位置|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|329907.0|07/05/2015 11:50:00 PM|050XX N NEWLAND AVE|0820|THEFT|$500 AND UNDER|STREET|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 |12:42:46 PM|41.973309466|-87.800174996|(41.973309466, -87.800174996)|
|1|10139776|HY329265|329265.0|07/05/2015 11:30:00 PM|011XX W MORSE AVE|0460|BATTERY|SIMPLE|STREET|false|是|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017, -87.65955018)|
|2|10140270|HY329253|329253.0|07/05/2015 11:20:00 PM|121XX S FRONT AVE|0486|BATTERY|DOMESTIC BATTERY SIMPLE|STREET|false|是|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|

## <a name="impute-missing-values"></a>估算缺失值

Azure 机器学习数据准备 SDK 可估算指定列中的缺失值。 下面的示例先加载纬度和经度值，再尝试估算输入数据中的缺失值。

```
import azureml.dataprep as dprep

# loading input data
df = dprep.read_csv(r'data\crime0-10.csv')
df = df.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
df = df.to_number(['Latitude', 'Longitude'])
df.head(5)
```

||ID|逮捕|纬度|经度|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|NaN|NaN|
|3|10139885|false|41.902152|-87.754883|
|4|10140379|false|41.885610|-87.657009|

第三条记录缺少纬度和经度值。 若要估算这些缺失值，可使用 `ImputeMissingValuesBuilder` 学习固定程序。 它可使用计算得出的 `MIN`、`MAX` 或 `MEAN` 值或使用 `CUSTOM` 值来估算列。 如果指定 `group_by_columns`，将使用每组计算得出的 `MIN`、`MAX` 和 `MEAN` 按组估算缺失值。

首先，快速检查“纬度”列的 `MEAN` 值。
```
df_mean = df.summarize(group_by_columns=['Arrest'],
                       summary_columns=[dprep.SummaryColumnsValue(column_id='Latitude',
                                                                 summary_column_name='Latitude_MEAN',
                                                                 summary_function=dprep.SummaryFunction.MEAN)])
df_mean = df_mean.filter(dprep.col('Arrest') == 'false')
df_mean.head(1)
```

||逮捕|Latitude_MEAN|
|-----|-----|----|
|0|false|41.878961|

由于纬度的 `MEAN` 值没问题，因此可用它来估算纬度。 对于缺失的经度值，将根据外部知识使用 42 进行估算。


```
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id='Latitude',
                                          impute_function=dprep.ReplaceValueFunction.MEAN)
# impute with custom value 42
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=42)
# get instance of ImputeMissingValuesBuilder
impute_builder = df.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
# call learn() to learn a fixed program to impute missing values
impute_builder.learn()
# call to_dataflow() to get a data flow with impute step added
df_imputed = impute_builder.to_dataflow()

# check impute result
df_imputed.head(5)
```

||ID|逮捕|纬度|经度|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|41.878961|42.000000|
|3|10139885|false|41.902152|-87.754883|
|4|10140379|false|41.885610|-87.657009|

如以上结果所示，缺少的纬度是使用 `Arrest=='false'` 组的 `MEAN` 值进行估算。 缺少的经度是使用 42 进行估算。
```
imputed_longitude = df_imputed.to_pandas_dataframe()['Longitude'][2]
assert imputed_longitude == 42
```

## <a name="derive-column-by-example"></a>按示例派生列
Azure 机器学习数据准备 SDK 中的更高级工具之一是，能够使用所需结果的示例派生列。 这样一来，只需为 SDK 提供示例，它便能生成可实现预期派生的代码。

```
import azureml.dataprep as dprep
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/BostonWeather.csv')
df = dataflow.head(10)
df
```
||DATE|REPORTTPYE|HOURLYDRYBULBTEMPF|HOURLYRelativeHumidity|HOURLYWindSpeed|
|----|----|----|----|----|----|
|0|1/1/2015 0:54|FM-15|22|50|10|
|1|1/1/2015 1:00|FM-12|22|50|10|
|2|1/1/2015 1:54|FM-15|22|50|10|
|3|1/1/2015 2:54|FM-15|22|50|11|
|4|1/1/2015 3:54|FM-15|24|46|13|
|5|1/1/2015 4:00|FM-12|24|46|13|
|6|1/1/2015 4:54|FM-15|22|52|15|
|7|1/1/2015 5:54|FM-15|23|48|17|
|8|1/1/2015 6:54|FM-15|23|50|14|
|9|1/1/2015 7:00|FM-12|23|50|14|

可以看到，此文件相当简单。 不过，假设需要将此文件与日期和时间格式为“Mar 10, 2018 | 2AM-4AM”的数据集联接起来。

可以将数据转换为所需的格式。

```
builder = dataflow.builders.derive_column_by_example(source_columns=['DATE'], new_column_name='date_timerange')
builder.add_example(source_data=df.iloc[1], example_value='Jan 1, 2015 12AM-2AM')
builder.preview() 
```

||DATE|date_timerange|
|----|----|----|
|0|1/1/2015 0:54|Jan 1, 2015 12AM-2AM|
|1|1/1/2015 1:00|Jan 1, 2015 12AM-2AM|
|2|1/1/2015 1:54|Jan 1, 2015 12AM-2AM|
|3|1/1/2015 2:54|Jan 1, 2015 2AM-4AM|
|4|1/1/2015 3:54|Jan 1, 2015 2AM-4AM|
|5|1/1/2015 4:00|Jan 1, 2015 4AM-6AM|
|6|1/1/2015 4:54|Jan 1, 2015 4AM-6AM|
|7|1/1/2015 5:54|Jan 1, 2015 4AM-6AM|
|8|1/1/2015 6:54|Jan 1, 2015 6AM-8AM|
|9|1/1/2015 7:00|Jan 1, 2015 6AM-8AM|

上面的代码先创建派生列的生成器。 你提供了要考虑的源列数组 (`DATE`)，以及要添加的新列的名称。

然后，你传入了第二行（索引 1），并提供了派生列的预期值，作为第一个示例。

最后，你调用了 `builder.preview()`，并能在源列旁边看到派生列。 虽然格式没问题，但日期值都是“Jan 1, 2015”。

现在，传入向下查看行需要跳过 (`skip`) 最上面几行对应的数字。

```
preview_df = builder.preview(skip=30)
preview_df
```

||DATE|date_timerange|
|-----|-----|-----|
|30|11/1/2015 22:54|Jan 1, 2015 10PM-12AM|
|31|11/1/2015 23:54|Jan 1, 2015 10PM-12AM|
|32|11/1/2015 23:59|Jan 1, 2015 10PM-12AM|
|33|11/2/2015 0:54|Feb 1, 2015 12AM-2AM|
|34|11/2/2015 1:00|Feb 1, 2015 12AM-2AM|
|35|11/2/2015 1:54|Feb 1, 2015 12AM-2AM|
|36|11/2/2015 2:54|Feb 1, 2015 2AM-4AM|
|37|11/2/2015 3:54|Feb 1, 2015 2AM-4AM|
|38|11/2/2015 4:00|Feb 1, 2015 4AM-6AM|
|39|11/2/2015 4:54|Feb 1, 2015 4AM-6AM|

此时可以发现已生成程序存在下列问题：仅根据你上面提供的一个示例，派生程序选择了将日期分析为“日/月/年”，但这并不符合本示例的需求。

若要解决此问题，需要提供另一个示例。

```
builder.add_example(source_data=preview_df.iloc[3], example_value='Jan 2, 2015 12AM-2AM')
preview_df = builder.preview(skip=30, count=10)
preview_df
```

||DATE|date_timerange|
|-----|-----|-----|
|30|1/1/2015 22:54|Jan 1, 2015 10PM-12AM|
|31|1/1/2015 23:54|Jan 1, 2015 10PM-12AM|
|32|1/1/2015 23:59|Jan 1, 2015 10PM-12AM|
|33|1/2/2015 0:54|Jan 2, 2015 12AM-2AM|
|34|1/2/2015 1:00|Jan 2, 2015 12AM-2AM|
|35|1/2/2015 1:54|Jan 2, 2015 12AM-2AM|
|36|1/2/2015 2:54|Jan 2, 2015 2AM-4AM|
|37|1/2/2015 3:54|Jan 2, 2015 2AM-4AM|
|38|1/2/2015 4:00|Jan 2, 2015 4AM-6AM|
|39|1/2/2015 4:54|Jan 2, 2015 4AM-6AM|


现在，行将“1/2/2015”正确处理为“Jan 2, 2015”。不过，如果向下查看派生列，可能会发现最后的几个值在派生列中没有任何转换值。 若要解决此问题，需要为第 66 行提供另一个示例。

```
builder.add_example(source_data=preview_df.iloc[66], example_value='Jan 29, 2015 8PM-10PM')
builder.preview(count=10)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|Jan 1, 2015 10PM-12AM|
|1|1/1/2015 23:54|Jan 1, 2015 10PM-12AM|
|2|1/1/2015 23:59|Jan 1, 2015 10PM-12AM|
|3|1/2/2015 0:54|Jan 2, 2015 12AM-2AM|
|4|1/2/2015 1:00|Jan 2, 2015 12AM-2AM|
|5|1/2/2015 1:54|Jan 2, 2015 12AM-2AM|
|6|1/2/2015 2:54|Jan 2, 2015 2AM-4AM|
|7|1/2/2015 3:54|Jan 2, 2015 2AM-4AM|
|8|1/2/2015 4:00|Jan 2, 2015 4AM-6AM|
|9|1/2/2015 4:54|Jan 2, 2015 4AM-6AM|

虽然一切看似没问题，但会发现这并不完全符合预期。 为了生成正确的格式，需要用“|”将日期和时间隔开。

若要解决此问题，可添加另一个示例。 这一次，不是从预览版中传入一行，而是对 `source_data` 参数构造列名和值的字典。

```
builder.add_example(source_data={'DATE': '11/11/2015 0:54'}, example_value='Nov 11, 2015 | 12AM-2AM')
builder.preview(count=10)
```
||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|无|
|1|1/1/2015 23:54|无|
|2|1/1/2015 23:59|无|
|3|1/2/2015 0:54|无|
|4|1/2/2015 1:00|无|
|5|1/2/2015 1:54|无|
|6|1/2/2015 2:54|无|
|7|1/2/2015 3:54|无|
|8|1/2/2015 4:00|无|
|9|1/2/2015 4:54|无|

这显然产生了负面影响，因为现在只有与提供的示例完全匹配的行，才会在派生列中有任何值。

接下来看看提供的示例：
```
examples = builder.list_examples()
examples
```

| |DATE|示例|example_id|
| -------- | -------- | -------- | -------- |
|0|1/1/2015 1:00|Jan 1, 2015 12AM-2AM|-1|
|1|1/2/2015 0:54|Jan 2, 2015 12AM-2AM|-2|
|2|1/29/2015 20:54|Jan 29, 2015 8PM-10PM|-3|
|3|11/11/2015 0:54|Nov 11, 2015 \| 12AM-2AM|-4|

可以看到，提供的示例不一致。 若要解决此问题，需要将前三个示例替换为正确示例（在日期和时间之间添加“|”）。

为此，可删除不正确的示例（要么从 pandas DataFrame 传入 `example_row`，要么传入 `example_id` 值），再重新添加新修改的示例。

```
builder.delete_example(example_id=-1)
builder.delete_example(example_row=examples.iloc[1])
builder.delete_example(example_row=examples.iloc[2])
builder.add_example(examples.iloc[0], 'Jan 1, 2015 | 12AM-2AM')
builder.add_example(examples.iloc[1], 'Jan 2, 2015 | 12AM-2AM')
builder.add_example(examples.iloc[2], 'Jan 29, 2015 | 8PM-10PM')
builder.preview()
```

| | DATE | date_timerange |
| -------- | -------- | -------- |
| 0 | 1/1/2015 0:54 | Jan 1, 2015 \| 12AM-2AM |
| 1 | 1/1/2015 1:00 | Jan 1, 2015 \| 12AM-2AM |
| 2 | 1/1/2015 1:54 | Jan 1, 2015 \| 12AM-2AM |
| 3 | 1/1/2015 2:54 | Jan 1, 2015 \| 2AM-4AM |
| 4 | 1/1/2015 3:54 | Jan 1, 2015 \| 2AM-4AM |
| 5 | 1/1/2015 4:00 | Jan 1, 2015 \| 4AM-6AM|
| 6 | 1/1/2015 4:54 | Jan 1, 2015 \| 4AM-6AM|
| 7 | 1/1/2015 5:54 | Jan 1, 2015 \| 4AM-6AM|
| 8 | 1/1/2015 6:54 | Jan 1, 2015 \| 6AM-8AM|
| 9 | 1/1/2015 7:00 | Jan 1, 2015 \| 6AM-8AM|

现在数据没问题，终于可以对生成器调用 `to_dataflow()` 了，这会返回包含相应派生列的数据流。

```
dataflow = builder.to_dataflow()
df = dataflow.to_pandas_dataframe()
df
```

## <a name="filtering"></a>筛选

SDK 包含方法 `Dataflow.drop_columns` 和 `Dataflow.filter`，可用于筛选掉列或行。

### <a name="initial-setup"></a>初始设置
```
import azureml.dataprep as dprep
from datetime import datetime
dataflow = dprep.read_csv(path='https://dprepdata.blob.core.windows.net/demo/green-small/*')
dataflow.head(5)
```
||lpep_pickup_datetime|Lpep_dropoff_datetime|Store_and_fwd_flag|RateCodeID|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|无|无|无|无|无|无|无|无|无|无|无|无|无|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|N|1|0|0|0|0|1|.00|0|0|21.25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|N|1|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|N|5|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|N|1|0|0|0|0|1|.00|0|0|3.25|

### <a name="filtering-columns"></a>筛选列

若要筛选列，请使用 `Dataflow.drop_columns`。 此方法需要使用要删除的列的列表，或更复杂的参数 `ColumnSelector`。

#### <a name="filtering-columns-with-list-of-strings"></a>使用字符串列表筛选列

在下面的示例中，`drop_columns` 需要使用字符串列表。 每个字符串应与要删除的相应列完全匹配。

``` 
dataflow = dataflow.drop_columns(['Store_and_fwd_flag', 'RateCodeID'])
dataflow.head(5)
```
||lpep_pickup_datetime|Lpep_dropoff_datetime|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|无|无|无|无|无|无|无|无|无|无|无|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|0|0|0|0|1|.00|0|0|21.25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|0|0|0|0|1|.00|0|0|3.25|

#### <a name="filtering-columns-with-regex"></a>使用正则表达式筛选列
也可以使用 `ColumnSelector` 表达式，删除与正则表达式匹配的列。 下面的示例删除与表达式 `Column*|.*longitude|.*latitude` 匹配的所有列。

```
dataflow = dataflow.drop_columns(dprep.ColumnSelector('Column*|.*longitud|.*latitude', True, True))
dataflow.head(5)
```
||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|无|无|无|无|无|无|无|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|1|.00|0|0|21.25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|1|.00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|1|.00|0|0|3.25|

## <a name="filtering-rows"></a>筛选行

若要筛选行，请使用 `DataFlow.filter`。 此方法需要使用 Azure 机器学习数据准备 SDK 表达式作为参数，并返回包含表达式评估为 True 的行的新数据流。 表达式使用表达式生成器（`col`、`f_not`、`f_and`、`f_or`）和常规运算符（>、<、>=、<=、==、!=）生成。

### <a name="filtering-rows-with-simple-expressions"></a>使用简单表达式筛选行

使用表达式生成器 `col`，将列名指定为字符串参数 `col('column_name')`，并结合以下标准运算符（>、<、>=、<=、==、!=）之一，生成表达式（如 `col('Tip_amount') > 0`）。 最后，将生成的表达式传入 `Dataflow.filter` 函数中。

在下面的示例中，`dataflow.filter(col('Tip_amount') > 0)` 返回新数据流，其中包含 `Tip_amount` 值大于 0 的行。

> [!NOTE] 
> `Tip_amount` 先转换为数字，这便于生成将它与其他数字值进行比较的表达式。

```
dataflow = dataflow.to_number(['Tip_amount'])
dataflow = dataflow.filter(dprep.col('Tip_amount') > 0)
dataflow.head(5)
```
||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-01 19:33:28|2013-08-01 19:35:21|5|.00|0.08|0|4.58|
|1|2013-08-05 13:16:38|2013-08-05 13:18:24|1|.00|0.30|0|3.8|
|2|2013-08-05 14:11:42|2013-08-05 14:12:47|1|.00|1.05|0|4.55|
|3|2013-08-05 14:15:56|2013-08-05 14:18:04|5|.00|2.22|0|5.72|
|4|2013-08-05 14:42:14|2013-08-05 14:42:38|1|.00|0.88|0|4.38|

### <a name="filtering-rows-with-complex-expressions"></a>使用复杂表达式筛选行

若要使用复杂表达式进行筛选，请结合使用一个或多个简单表达式与表达式生成器 `f_not`、`f_and` 或 `f_or`。

在下面的示例中，`Dataflow.filter` 返回新数据流，其中包含 `'Passenger_count'` 小于 5 且 `'Tolls_amount'` 大于 0 的行。

```
dataflow = dataflow.to_number(['Passenger_count', 'Tolls_amount'])
dataflow = dataflow.filter(dprep.f_and(dprep.col('Passenger_count') < 5, dprep.col('Tolls_amount') > 0))
dataflow.head(5)
```
||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-08 12:16:00|2013-08-08 12:16:00|1.0|.00|2.25|5.00|19.75|
|1|2013-08-12 14:43:53|2013-08-12 15:04:50|1.0|5.28|6.46|5.33|32.29|
|2|2013-08-12 19:48:12|2013-08-12 20:03:42|1.0|5.50|1.00|10.66|30.66|
|3|2013-08-13 06:11:06|2013-08-13 06:30:28|1.0|9.57|7.47|5.33|44.8|
|4|2013-08-16 20:33:50|2013-08-16 20:48:50|1.0|5.63|3.00|5.33|27.83|

还可以结合使用多个表达式生成器来创建嵌套表达式，从而筛选行。

> [!NOTE]
> `lpep_pickup_datetime` 和 `Lpep_dropoff_datetime` 先转换为日期/时间，这便于生成将它与其他日期/时间值进行比较的表达式。

```
dataflow = dataflow.to_datetime(['lpep_pickup_datetime', 'Lpep_dropoff_datetime'], ['%Y-%m-%d %H:%M:%S'])
dataflow = dataflow.to_number(['Total_amount', 'Trip_distance'])
mid_2013 = datetime(2013,7,1)
dataflow = dataflow.filter(
    dprep.f_and(
        dprep.f_or(
            dprep.col('lpep_pickup_datetime') > mid_2013,
            dprep.col('Lpep_dropoff_datetime') > mid_2013),
        dprep.f_and(
            dprep.col('Total_amount') > 40,
            dprep.col('Trip_distance') < 10)))
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-13 06:11:06+00:00|2013-08-13 06:30:28+00:00|1.0|9.57|7.47|5.33|44.80|
|1|2013-08-23 12:28:20+00:00|2013-08-23 12:50:28+00:00|2.0|8.22|8.08|5.33|40.41|
|2|2013-08-25 09:12:52+00:00|2013-08-25 09:34:34+00:00|1.0|8.80|8.33|5.33|41.66|
|3|2013-08-25 16:46:51+00:00|2013-08-25 17:13:55+00:00|2.0|9.66|7.37|5.33|44.20|
|4|2013-08-25 17:42:11+00:00|2013-08-25 18:02:57+00:00|1.0|9.60|6.87|5.33|41.20|

## <a name="custom-python-transforms"></a>自定义 Python 转换 

在某些情况下，最简单的做法是编写一些 Python 代码。 SDK 提供了三个可用的扩展点。

- 新脚本列
- 新脚本筛选器
- 转换分区

纵向扩展和横向扩展运行时支持所有扩展。 使用这些扩展点的关键优势是，无需拉取所有数据，即可创建数据框。 自定义 Python 代码像其他转换一样按分区批量运行，通常是并行的。

### <a name="initial-data-preparation"></a>初始数据准备

首先，从 Azure Blob 加载一些数据。

```
import azureml.dataprep as dprep
col = dprep.col

df = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv', skip_rows=1)
df.head(5)
```
| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Hale County|10171002158| |
|1|ALABAMA|1|101710|Hale County|10171002162| |
|2|ALABAMA|1|101710|Hale County|10171002156| |
|3|ALABAMA|1|101710|Hale County|10171000588|2|
|4|ALABAMA|1|101710|Hale County|10171000589| |

剪裁数据集，并执行一些基本转换。

```
df = df.keep_columns(['stnam', 'leanm10', 'ncessch', 'MAM_MTH00numvalid_1011'])
df = df.replace_na(columns=['leanm10', 'MAM_MTH00numvalid_1011'], custom_na_list='.')
df = df.to_number(['ncessch', 'MAM_MTH00numvalid_1011'])
df.head(5)
```
| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|1.017100e+10|无|
|1|ALABAMA|Hale County|1.017100e+10|无|
|2|ALABAMA|Hale County|1.017100e+10|无|
|3|ALABAMA|Hale County|1.017100e+10|2|
|4|ALABAMA|Hale County|1.017100e+10|无|

使用筛选器查找空值。 你将会发现一些空值，所以现在填充这些缺失值。

```
df.filter(col('MAM_MTH00numvalid_1011').is_null()).head(5)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|1.017100e+10|无|
|1|ALABAMA|Hale County|1.017100e+10|无|
|2|ALABAMA|Hale County|1.017100e+10|无|
|3|ALABAMA|Hale County|1.017100e+10|无|
|4|ALABAMA|Hale County|1.017100e+10|无|

### <a name="transform-partition"></a>转换分区

可使用便捷的 pandas 函数，将所有空值替换为 0。 下面的代码按分区运行，而不是一次性对所有数据集运行。 也就是说，对于大型数据集，下面的代码可能会在运行时处理数据时按分区并行运行。

```
df = df.transform_partition("""
def transform(df, index):
    df['MAM_MTH00numvalid_1011'].fillna(0,inplace=True)
    return df
""")
h = df.head(5)
h
```
||stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|1.017100e+10|0.0|
|1|ALABAMA|Hale County|1.017100e+10|0.0|
|2|ALABAMA|Hale County|1.017100e+10|0.0|
|3|ALABAMA|Hale County|1.017100e+10|2.0|
|4|ALABAMA|Hale County|1.017100e+10|0.0|

### <a name="new-script-column"></a>新脚本列

可使用 Python 代码新建包含县名和州名的列，其中州名采用首字母大写形式。 为此，请对数据流使用 `new_script_column()` 方法。

```
df = df.new_script_column(new_column_name='county_state', insert_after='leanm10', script="""
def newvalue(row):
    return row['leanm10'] + ', ' + row['stnam'].title()
""")
h = df.head(5)
h
```
||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|Hale County, Alabama|1.017100e+10|0.0|
|1|ALABAMA|Hale County|Hale County, Alabama|1.017100e+10|0.0|
|2|ALABAMA|Hale County|Hale County, Alabama|1.017100e+10|0.0|
|3|ALABAMA|Hale County|Hale County, Alabama|1.017100e+10|2.0|
|4|ALABAMA|Hale County|Hale County, Alabama|1.017100e+10|0.0|
### <a name="new-script-filter"></a>新脚本筛选器

现在，生成 Python 表达式，将数据集筛选为仅包含新 `county_state` 列内没有“Hale”的行。 若要保留行，表达式返回 `True`；若要删除行，表达式返回 `False`。

```
df = df.new_script_filter("""
def includerow(row):
    val = row['county_state']
    return 'Hale' not in val
""")
h = df.head(5)
h
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|1.0|
|1|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|0.0|
|2|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|0.0|
|3|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|0.0|
|4|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|0.0|
