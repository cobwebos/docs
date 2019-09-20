---
title: 使用 azure 数据工厂中的 Azure 数据资源管理器控制命令
description: 在本主题中，使用 azure 数据工厂中的 Azure 数据资源管理器控制命令
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: 316ddbf662a5418e54f37cb335475a86c50118c7
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71131436"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>使用 Azure 数据工厂命令活动运行 Azure 数据资源管理器控制命令

[Azure 数据工厂](/azure/data-factory/)（ADF）是一项基于云的数据集成服务，可让你对数据执行活动组合。 使用 ADF 创建数据驱动的工作流，用于协调和自动化数据移动和数据转换。 Azure 数据工厂中的**azure 数据资源管理器命令**活动使你能够在 ADF 工作流中运行[azure 数据资源管理器控制命令](/azure/kusto/concepts/#control-commands)。 本文介绍如何使用查找活动创建管道，以及如何创建包含 Azure 数据资源管理器 command 活动的 ForEach 活动。

## <a name="prerequisites"></a>先决条件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* [Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)
* 数据源。
* [数据工厂](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>创建新管道

1. 选择 "**创作**铅笔" 工具。 
1. 通过选择 **+** 并从下拉选择 "**管道**"，创建一个新管道。

   ![创建新管道](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>创建查找活动

1. 在 "**活动**" 窗格中的 "**常规**" 下，选择**查找**活动。 将其拖放到右侧的主画布中。
 
    ![选择查找活动](media/data-factory-command-activity/select-activity.png)

1. 画布现在包含您创建的查找活动。 使用画布下面的选项卡更改任何相关参数。 **通常**，重命名活动。 

    ![编辑查找活动](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > 单击空画布区域，查看管道属性。 使用 "**常规**" 选项卡可重命名管道。 管道的名称为 "*管道-4-文档*"。

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>在查找活动中创建 Azure 数据资源管理器数据集

1. 在 "**设置**" 中，选择预先创建的 Azure 数据资源管理器**源数据集**，或选择 " **+ 新建**" 以创建新的数据集。
 
    ![在查找设置中添加数据集](media/data-factory-command-activity/lookup-settings.png)

1. 从 "**新建数据集**" 窗口中选择 " **Azure 数据资源管理器（Kusto）** " 数据集。 选择 "**继续**" 以添加新的数据集。

   ![选择新数据集](media/data-factory-command-activity/select-new-dataset.png) 

1. 新的 Azure 数据资源管理器数据集参数在 "**设置**" 中可见。 若要更新参数，请选择 "**编辑**"。

    ![Azure 数据资源管理器 dataset 的查找设置](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. **AzureDataExplorerTable**新选项卡将在主画布中打开。 
    * 选择 "**常规**" 并编辑数据集名称。 
    * 选择 "**连接**" 以编辑数据集属性。 
    * 从下拉端选择**链接服务**，或选择 " **+ 新建**" 以创建新的链接服务。

    ![编辑 Azure 数据资源管理器数据集属性](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. 创建新的链接服务时，将打开 "**新建链接服务（Azure 数据资源管理器）** " 页：

    ![ADX - 新建链接服务](media/data-factory-command-activity/adx-new-linked-service.png)

   * 选择 Azure 数据资源管理器链接服务的**名称**。 如果需要，添加**说明**。
   * 如果需要，在 "**通过集成运行时连接**" 中更改当前设置。 
   * 在 "**帐户选择方法**" 中，使用以下两种方法之一选择群集： 
        * 选中“从 Azure 订阅”单选按钮，并选择你的 **Azure 订阅**帐户。 然后选择你的**群集**。 请注意，下拉列表中只会列出属于该用户的群集。
        * 请选择 "**手动输入**" 单选按钮，然后输入**终结点**（群集 URL）。
    * 指定**租户**。
    * 输入**服务主体 ID**。 主体 ID 必须具有足够的权限，具体取决于所使用的命令所需的权限级别。
    * 选择“服务主体密钥”按钮并输入**服务主体密钥**。
    * 从下拉菜单中选择你的**数据库**。 或者，选中“编辑”复选框并输入你的数据库名称。
    * 选择“测试连接”以测试创建的链接服务连接。 如果可以连接到设置，会出现绿色的勾选标记“连接成功”。
    * 选择“完成”以完成链接服务的创建过程。

1. 设置链接服务后，在 " **AzureDataExplorerTable** > **连接**" 中添加 "**表**名称"。 选择 "**预览数据**"，确保数据得到正确显示。

   数据集现已准备就绪，可以继续编辑管道。

### <a name="add-a-query-to-your-lookup-activity"></a>向查找活动添加查询

1. 在 "**管道-4-文档** > **设置**" 中，在 "**查询**" 文本框中添加查询，例如：

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. 根据需要更改**查询超时值**或**无截断**和**首行**属性。 在此流中，我们保留默认的**查询超时**值，并取消选中复选框。 

    ![查找活动的最终设置](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>为每个活动创建一个 

1. 接下来，将每个活动添加到管道。 此活动将处理从查找活动返回的数据。 
    * 在 "**活动**" 窗格中的 "**迭代 & 条件**" 下，选择**ForEach**活动，并将其拖放到画布中。
    * 在查找活动的输出和画布中的 ForEach 活动的输入之间绘制一条线以连接它们。

        ![ForEach 活动](media/data-factory-command-activity/for-each-activity.png)

1.  选择画布中的 ForEach 活动。 在下面的 "**设置**" 选项卡中：
    * 选中 "**顺序**" 复选框可按顺序处理查找结果，或将其保留为未选中状态以创建并行处理。
    * 设置**批处理计数**。
    * 在 "**项**" 中，提供对输出值的以下引用：  *@activity（' Lookup1 '）。*

       ![ForEach 活动设置](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>在 ForEach 活动中创建 Azure 数据资源管理器命令活动

1. 双击画布中的 ForEach 活动，在新画布中打开它以指定 ForEach 中的活动。
1. 在 "**活动**" 窗格中的 " **azure 数据资源管理器**" 下，选择 " **azure 数据资源管理器命令**" 活动并将其拖放到画布中。

    ![Azure 数据资源管理器命令活动](media/data-factory-command-activity/adx-command-activity.png)

1.  在 "**连接**" 选项卡中，选择以前创建的同一个链接服务。

    ![azure 数据资源管理器命令活动连接选项卡](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. 在 "**命令**" 选项卡中，提供以下命令：

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    此**命令**指示 Azure 数据资源管理器以压缩格式将给定查询的结果导出到 blob 存储中。 它以异步方式运行（使用 async 修饰符）。
    查询处理查找活动结果中每一行的数据库列。 **命令超时**可以保持不变。

    ![命令活动](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > 命令活动具有以下限制：
    > * 大小限制：1 MB 响应大小
    > * 时间限制：20分钟（默认值），1小时（最大值）。
    > * 如果需要，可以使用[AdminThenQuery](/azure/kusto/management/index#combining-queries-and-control-commands)将查询追加到结果，以减少生成的大小/时间。

1.  现在，管道已准备就绪。 您可以通过单击管道名称返回到 "主管道" 视图。

    ![Azure 数据资源管理器命令管道](media/data-factory-command-activity/adx-command-pipeline.png)

1. 在发布管道之前选择 "**调试**"。 可以在 "**输出**" 选项卡中监视管道进度。

    ![azure 数据资源管理器命令活动输出](media/data-factory-command-activity/command-activity-output.png)

1. 你可以**发布所有**，然后**添加触发器**来运行管道。 

## <a name="control-command-outputs"></a>控制命令输出

下面详细说明了命令活动输出的结构。 此输出可由管道中的下一个活动使用。

### <a name="returned-value-of-a-non-async-control-command"></a>非 async control 命令的返回值

在非 async control 命令中，返回值的结构类似于查找活动结果的结构。 `count`字段指示返回的记录数。 固定数组字段`value`包含一系列记录。 

```json
{ 
    "count": "2", 
    "value": [ 
        { 
            "ExtentId": "1b9977fe-e6cf-4cda-84f3-4a7c61f28ecd", 
            "ExtentSize": 1214.0, 
            "CompressedSize": 520.0 
        }, 
        { 
            "ExtentId": "b897f5a3-62b0-441d-95ca-bf7a88952974", 
            "ExtentSize": 1114.0, 
            "CompressedSize": 504.0 
        } 
    ] 
} 
```
 
### <a name="returned-value-of-an-async-control-command"></a>Async control 命令的返回值

在 async control 命令中，活动将轮询后台的操作表，直到异步操作完成或超时。因此，返回的值将包含给定**OperationId**属性`.show operations OperationId`的的结果。 检查 "**状态**" 和 "**状态**" 属性的值，验证操作是否成功完成。

```json
{ 
    "count": "1", 
    "value": [ 
        { 
            "OperationId": "910deeae-dd79-44a4-a3a2-087a90d4bb42", 
            "Operation": "TableSetOrAppend", 
            "NodeId": "", 
            "StartedOn": "2019-06-23T10:12:44.0371419Z", 
            "LastUpdatedOn": "2019-06-23T10:12:46.7871468Z", 
            "Duration": "00:00:02.7500049", 
            "State": "Completed", 
            "Status": "", 
            "RootActivityId": "f7c5aaaf-197b-4593-8ba0-e864c94c3c6f", 
            "ShouldRetry": false, 
            "Database": "MyDatabase", 
            "Principal": "<some principal id>", 
            "User": "<some User id>" 
        } 
    ] 
}
``` 

## <a name="next-steps"></a>后续步骤

* 了解如何[使用 Azure 数据工厂将数据复制到 azure 数据资源管理器](data-factory-load-data.md)。
* 了解如何使用[Azure 数据工厂模板从数据库大容量复制到 Azure 数据资源管理器](data-factory-template.md)。