---
title: 教程：创建 Synapse 工作区入门
description: 在本教程中，你将了解如何创建 Synapse 工作区、SQL 池和 Apache Spark 池。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 08/27/2020
ms.openlocfilehash: f5851174ba223bd0ab48512b9e75449dcc927a04
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91300099"
---
# <a name="prerequisites"></a>先决条件

若要完成本教程的所有步骤，你需要有权访问针对其分配了“所有者”角色的资源组。 在此资源组中创建 Synapse 工作区。

## <a name="create-a-synapse-workspace"></a>创建 Synapse 工作区

在本教程中，你将了解如何创建 Synapse 工作区、SQL 池和 Apache Spark 池。 

## <a name="create-a-synapse-workspace"></a>创建 Synapse 工作区

1. 打开 [Azure 门户](https://portal.azure.com)，然后在顶部搜索“Synapse”。
1. 在“服务”下的搜索结果中，选择“Azure Synapse Analytics (工作区预览版)” 。
1. 选择“添加”以创建工作区。
1. 在“基本信息”中选择工作区名称。 在本教程中，我们将使用 myworkspace。
1. 需要 ADLSGEN2 帐户才能创建工作区。 最简单的方法是创建一个新工作区。 如果要重复使用现有工作区，需要执行一些其他配置。 
1. 选项 1 创建新的 ADLSGEN2 帐户 
    1. 导航到“选择 Data Lake Storage Gen 2”。 
    1. 单击“新建”进行创建，并将其命名为 contosolake 。
    1. 单击“文件系统”并将其命名为 users 。
1. 选项 2 使用现有 ADLSGEN2 帐户。 请参阅本文档底部的“准备 ADLSGEN2 存储帐户”说明。
1. Azure Synapse 工作区将此存储帐户用作“主要”存储帐户，并使用容器来存储工作区数据。 工作区将数据存储在 Apache Spark 表中。 它将 Spark 应用程序日志存储在名为 /synapse/workspacename 的文件夹下。
1. 选择“查看 + 创建” > “创建”。 你的工作区将在几分钟内准备就绪。


## <a name="open-synapse-studio"></a>打开 Synapse Studio

创建 Azure Synapse 工作区后，可以通过两种方式打开 Synapse Studio：

* 在 [Azure 门户](https://portal.azure.com)中打开 Synapse 工作区。 在“概述”部分的顶部，选择“启动 Synapse Studio” 。
* 转到 `https://web.azuresynapse.net`，然后登录到工作区。

## <a name="create-a-sql-pool"></a>创建 SQL 池

1. 在 Synapse Studio 的左窗格中，选择“管理” > “SQL 池” 。
1. 选择“新建”，然后输入以下设置：

    |设置 | 建议的值 | 
    |---|---|---|
    |**SQL 池名称**| **SQLDB1**|
    |**性能级别**|**DW100C**|
    |||

1. 选择“查看 + 创建” > “创建”。 你的 SQL 池将在几分钟内准备就绪。 你的 SQL 池与另一个名称也为 SQLDB1 的 SQL 池数据库相关联。

只要 SQL 池处于活动状态，就会使用计费资源。 你可以在稍后暂停池以降低成本。

## <a name="create-an-apache-spark-pool"></a>创建 Apache Spark 池

1. 在 Synapse Studio 的左侧窗格中，选择“管理” > “Apache Spark 池” 。
1. 选择“新建”，然后输入以下设置：

    |设置 | 建议的值 | 
    |---|---|---|
    |**Apache Spark 池名称**|**Spark1**
    |**节点大小**| **小型**|
    |**节点数**| 将最小值设置为 3，最大值设置为 3|

1. 选择“查看 + 创建” > “创建”。 你的 Apache Spark 池将在几秒钟内准备就绪。

> [!NOTE]
> 尽管名称类似，但 Apache Spark 池与 SQL 池并不相同。 这只是一些基本元数据，用于告知 Synapse 工作区如何与 Spark 交互。

由于 Spark 池是元数据，因此无法启动或停止。

在 Azure Synapse 中执行 Spark 活动时，请指定要使用的 Spark 池。 该池告知 Azure Synapse 要使用的 Spark 资源数量。 只需支付所用资源的费用。 主动停止使用该池时，资源将自动超时并进行回收。

> [!NOTE]
> Spark 数据库是独立于 Spark 池创建的。 工作区始终具有名为“default”的 Spark 数据库。 可以创建其他 Spark 数据库。

## <a name="the-sql-on-demand-pool"></a>SQL 按需版本池

每个工作区都具有名为“SQL on-demand”的预构建池。 无法删除该池。 使用 SQL 按需版本池即可使用 SQL，无需创建或考虑如何在 Azure Synapse 中管理 SQL 池。

与其他类型的池不同，SQL 按需版本的计费基于运行查询所扫描的数据量，而不是执行查询所使用的资源数。

* SQL 按需版本具有自己的 SQL 按需版本数据库，这些数据库独立于任何 SQL 按需版本池而存在。
* 工作区始终只包含一个名为“SQL 按需版本”的 SQL 按需版本池。

## <a name="preparing-a-adlsgen2-storage-account"></a>准备 ADLSGEN2 存储帐户

### <a name="perform-the-following-steps-before-you-create-your-workspace"></a>在创建工作区之前，请执行以下步骤

1. 打开 [Azure 门户](https://portal.azure.com)。
1. 导航到现有存储帐户
1. 在左窗格中，选择“访问控制(IAM)”。 
1. 分配以下角色或确保其已经分配：
    * 为自己分配“所有者”角色。
    * 为自己分配“存储 Blob 数据所有者”角色。
1. 在左窗格中选择“容器”并创建容器。
1. 可以为此容器指定名称。 在本文档中，我们使用名称 users。
1. 接受默认设置“公共访问级别”，然后选择“创建” 。

### <a name="perform-the-following-steps-after-you-create-your-workspace"></a>在创建工作区后，请执行以下步骤

配置从工作区访问存储帐户的权限。 Azure Synapse 工作区的托管标识可能已具有对存储帐户的访问权限。 请执行以下步骤，以确保：

1. 打开 [Azure 门户](https://portal.azure.com)和为工作区所选的主存储帐户。
1. 从左窗格中选择“访问控制(标识和访问管理)”。
1. 分配以下角色或确保其已经分配。 我们对工作区标识和工作区使用相同的名称。
    * 对于存储帐户上的“存储 Blob 数据参与者”角色，请将 myworkspace 指定为工作区标识 。
    * 将 myworkspace 指定为工作区名称。

1. 选择“保存”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 SQL 池进行分析](get-started-analyze-sql-pool.md)
