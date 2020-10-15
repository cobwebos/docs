---
title: 教程：开始分析存储帐户中的数据
description: 本教程介绍如何分析位于存储帐户中的数据。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: a0d5c758873413e549b31e3ec4cc41791fc8c371
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89667406"
---
# <a name="analyze-data-in-a-storage-account"></a>分析存储帐户中的数据

本教程介绍如何分析位于存储帐户中的数据。

## <a name="overview"></a>概述

到目前为止，我们介绍了数据驻留在工作区的数据库中的各种情况。 现在，我们将介绍如何使用存储帐户中的文件。 对于这一情况，我们将使用在创建工作区时所指定的工作区和容器的主存储帐户。

* 存储帐户的名称为：contosolake
* 存储帐户中的容器的名称：users

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>在存储帐户中创建 CSV 和 Parquet 文件

在笔记本中运行以下代码。 这会在存储帐户中创建 CSV 文件和 parquet 文件。

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyze-data-in-a-storage-account"></a>分析存储帐户中的数据

1. 在 Synapse Studio 中，转到“数据”中心，然后选择“已关联” 。
1. 转到“存储帐户” > “myworkspace (主 - contosolake)” 。
1. 选择“用户(主)”。 应会看到 NYCTaxi 文件夹。 在内部应该会看到名为 PassengerCountStats.csv 和 PassengerCountStats.parquet 的两个文件夹 。
1. 打开 PassengerCountStats.parquet 文件夹。 在文件夹内，你将看到名称类似于 `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet` 的 Parquet 文件。
1. 右键单击“.parquet”，然后选择“新建笔记本” 。 它会创建一个包含如下所示的单元的笔记本：

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. 运行该单元。
1. 右键单击内部的 parquet 文件，然后选择“新建 SQL 脚本” > “选择前 100 行” 。 它会创建如下所示的 SQL 脚本：

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

    在脚本窗口中，“连接到”字段将设置为“按需 SQL” 。

1. 运行该脚本。



## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用管道安排活动](get-started-pipelines.md)
