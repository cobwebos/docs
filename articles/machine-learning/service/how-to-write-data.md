---
title: 使用 Azure 机器学习数据准备 SDK 写入数据 - Python
description: 了解如何使用 Azure 机器学习数据准备 SDK 写入数据。 可在数据流中的任何位置写出数据，也可以将数据写入我们支持的任何位置（本地文件系统、Azure Blob 存储和 Azure Data Lake Storage）的文件中。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: da5823473b7f69fa0a6c65d5ea7319bfd2e92720
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946759"
---
# <a name="write-data-with-the-azure-machine-learning-data-prep-sdk"></a>使用 Azure 机器学习数据准备 SDK 写入数据
可在数据流中的任何位置写出数据。 系统将这些写入操作视为步骤添加到生成的数据流中，并在数据流每次运行时运行它们。 由于管道中不限制写步骤的数量，因此很容易写出中间结果以供故障排除或由其他管道获取。 请注意，每个写步骤的运行都会导致完全拉取数据流中的数据，这一点非常重要。 例如，具有三个写步骤的数据流将读取并处理数据集中的每个记录三次。

## <a name="writing-to-files"></a>写入文件
通过 [Azure 机器学习数据准备 SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py)，可将数据写入我们支持的任何位置（本地文件系统、Azure Blob 存储和 Azure Data Lake Storage）的文件中。 数据写入多个分区文件以实现并行写入。 写入完成后，还会生成名为 SUCCESS 的标记文件。 这有助于确定中间写入的完成时间，而无需等待整个管道的完成。

在 Spark 中运行数据流时，必须写入空文件夹。 尝试运行对现有文件夹的写入将失败。 确保目标文件夹为空，或为每次运行使用不同的目标位置，否则写入将失败。

Azure 机器学习数据准备 SDK 支持以下文件格式：
-   带分隔符的文件（CSV、TSV 等）
-   Parquet 文件

对于此示例，首先将数据加载到数据流中。 我们将通过不同格式重复使用此数据。

```

import azureml.dataprep as dprep
t = dprep.smart_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(10)
```   

|   |  Column1 |    Column2 | Column3 | Column4  |Column5   | Column6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   无|       否|     否  |   ENRS    |NaN    |   NaN |   NaN|    
|   1|      10003.0 |   99999.0 |   无|       否|     否  |   ENSO|       NaN|        NaN |NaN|   
|   2|  10010.0|    99999.0|    无|   否| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    无|   否| 否| |   NaN|    NaN|    NaN|
|4| 10014.0|    99999.0|    无|   否| 否| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    无|   否| 否| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   无|   否| 否|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    无|   否| 否| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    无|   否| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    无|   否| SV|     |77000.0|   15500.0|    120.0|

## <a name="delimited-files"></a>带分隔符的文件
下面的行使用写入步骤创建新数据流，但实际写入尚未发生。 当数据流运行时，将执行写入。

```
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))
```
现在运行数据流，这将运行写入操作。
```
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```
|   |  Column1 |    Column2 | Column3 | Column4  |Column5   | Column6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   ERROR |       否|     否  |   ENRS    |ERROR    |   ERROR |   ERROR|    
|   1|      10003.0 |   99999.0 |   ERROR |       否|     否  |   ENSO|       ERROR|        ERROR |ERROR|   
|   2|  10010.0|    99999.0|    ERROR |   否| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    ERROR |   否| 否| |   ERROR|    ERROR|    ERROR|
|4| 10014.0|    99999.0|    ERROR |   否| 否| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    ERROR |   否| 否| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   ERROR |   否| 否|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    ERROR |   否| 否| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    ERROR |   否| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    ERROR |   否| SV|     |77000.0|   15500.0|    120.0|

由于未正确分析数字，数值列中写入的数据包含多个错误。 默认情况下，写入 CSV 时，这些 NULL 值将替换为字符串“ERROR”。 可在 write 调用中添加参数，并指定一个字符串用于表示 NULL 值。

```
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```
|   |  Column1 |    Column2 | Column3 | Column4  |Column5   | Column6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   BadData |       否|     否  |   ENRS    |BadData    |   BadData |   BadData|    
|   1|      10003.0 |   99999.0 |   BadData |       否|     否  |   ENSO|       BadData|        BadData |BadData|   
|   2|  10010.0|    99999.0|    BadData |   否| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    BadData |   否| 否| |   BadData|    BadData|    BadData|
|4| 10014.0|    99999.0|    BadData |   否| 否| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    BadData |   否| 否| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   BadData |   否| 否|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    BadData |   否| 否| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    BadData |   否| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    BadData |   否| SV|     |77000.0|   15500.0|    120.0|
## <a name="parquet-files"></a>Parquet 文件

 与上面的 `write_to_csv` 函数类似，`write_to_parquet` 返回一个带有 Parquet 写入步骤的新数据流，将在数据流运行时执行该步骤。

```
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```
 我们现在运行数据流，这将执行写入操作。

```
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(10)
```
|   |  Column1 |    Column2 | Column3 | Column4  |Column5   | Column6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   MiscreantData |       否|     否  |   ENRS    |MiscreantData    |   MiscreantData |   MiscreantData|    
|   1|      10003.0 |   99999.0 |   MiscreantData |       否|     否  |   ENSO|       MiscreantData|        MiscreantData |MiscreantData|   
|   2|  10010.0|    99999.0|    MiscreantData |   否| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    MiscreantData |   否| 否| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0|    99999.0|    MiscreantData |   否| 否| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    MiscreantData |   否| 否| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   MiscreantData |   否| 否|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    MiscreantData |   否| 否| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    MiscreantData |   否| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    MiscreantData |   否| SV|     |77000.0|   15500.0|    120.0|
