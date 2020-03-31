---
title: 使用 Azure 数据工厂中的 Azure 数据资源管理器控制命令
description: 本主题介绍如何使用 Azure 数据工厂中的 Azure 数据资源管理器控制命令
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: 20da2d54ea54674656b2c1006d094c63133baf79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "72264490"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>使用 Azure 数据工厂命令活动运行 Azure 数据资源管理器控制命令

[Azure 数据工厂](/azure/data-factory/) (ADF) 是基于云的数据集成服务，可用于对数据执行一系列活动。 使用 ADF 可以创建数据驱动式工作流用于协调和自动化数据移动与数据转换。 使用 Azure 数据工厂中的“Azure 数据资源管理器命令”活动，可以在 ADF 工作流中运行 [Azure 数据资源管理器控制命令](/azure/kusto/concepts/#control-commands)。**** 本文介绍如何使用 Lookup 活动和 ForEach 活动创建包含 Azure 数据资源管理器命令活动的管道。

## <a name="prerequisites"></a>先决条件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* [Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)
* 数据源。
* 一个[数据工厂](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>创建新管道

1. 选择“创作”铅笔图标。**** 
1. 通过选择**+** 然后从下拉列表中选择**管道**来创建新管道。

   ![创建新管道](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>创建 Lookup 活动

[Lookup 活动](/azure/data-factory/control-flow-lookup-activity)可以从 Azure 数据工厂支持的任何数据源检索数据集。 Lookup 活动的输出可以在 ForEach 或其他活动中使用。

1. 在“活动”窗格中的“常规”下，选择“Lookup”活动。************ 将其拖放到右侧的主画布中。
 
    ![选择 Lookup 活动](media/data-factory-command-activity/select-activity.png)

1. 画布现在包含创建的 Lookup 活动。 使用画布下面的选项卡更改任何相关参数。 在“常规”中，将活动重命名。**** 

    ![编辑 Lookup 活动](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > 单击画布中的空白区域可查看管道属性。 使用“常规”选项卡可将管道重命名。**** 示例中的管道命名为 *pipeline-4-docs*。

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>在 Lookup 活动中创建 Azure 数据资源管理器数据集

1. 在“设置”中，选择预先创建的 Azure 数据资源管理器“源数据集”，或选择“+ 新建”以创建新的数据集。************
 
    ![在 Lookup 设置中添加数据集](media/data-factory-command-activity/lookup-settings.png)

1. 在“新建数据集”窗口中选择“Azure 数据资源管理器(Kusto)”数据集。******** 选择“继续”以添加新数据集。****

   ![选择新数据集](media/data-factory-command-activity/select-new-dataset.png) 

1. 新 Azure 数据资源管理器数据集参数将显示在“设置”中。**** 若要更新参数，请选择“编辑”。****

    ![Azure 数据资源管理器数据集的 Lookup 设置](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. 主画布中将打开新选项卡“AzureDataExplorerTable”。**** 
    * 选择“常规”并编辑数据集名称。**** 
    * 选择“连接”以编辑数据集属性。**** 
    * 从下拉列表中选择“链接服务”，或选择“+ 新建”以创建新的链接服务。********

    ![编辑 Azure 数据资源管理器数据集属性](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. 创建新的链接服务时，将打开“新建链接服务(Azure 数据资源管理器)”页：****

    ![ADX - 新建链接服务](media/data-factory-command-activity/adx-new-linked-service.png)

   * 选择 Azure 数据资源管理器链接服务的**名称**。 根据需要添加**说明**。
   * 在“通过集成运行时进行连接”中，根据需要更改当前设置。**** 
   * 在“帐户选择方法”中，使用以下两种方法之一选择群集：**** 
        * 选中“从 Azure 订阅”单选按钮，并选择你的 **Azure 订阅**帐户。**** 然后选择你的**群集**。 请注意，下拉列表中只会列出属于该用户的群集。
        * 应选择“手动输入”单选按钮并输入你的**终结点**（群集 URL）。****
    * 指定**租户**。
    * 输入**服务主体 ID**。 根据所用命令所需的权限级别，主体 ID 必须拥有足够的权限。
    * 选择“服务主体密钥”按钮并输入**服务主体密钥**。****
    * 从下拉菜单中选择你的**数据库**。 或者，选中“编辑”复选框并输入你的数据库名称。****
    * 选择“测试连接”以测试创建的链接服务连接。**** 如果可以连接到设置，会出现绿色的勾选标记“连接成功”。****
    * 选择“完成”以完成链接服务的创建过程。****

1. 设置链接服务后，在**AzureDataExplorerTable** > **连接**中，添加**表**名称。 选择“预览数据”，确保数据正确呈现。****

   数据集现已准备就绪，接下来可以继续编辑管道。

### <a name="add-a-query-to-your-lookup-activity"></a>将查询添加到 Lookup 活动

1. 在**管道 4 文档** > **设置**中，在**查询**文本框中添加查询，例如：

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. 根据需要更改“查询超时”或“不截断”和“仅第一行”属性。************ 在此流程中，我们保留了默认的“查询超时”，并取消选中了相应的复选框。**** 

    ![Lookup 活动的最终设置](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>创建 For-Each 活动 

[For-Each](/azure/data-factory/control-flow-for-each-activity) 活动用于循环访问集合，并在循环中执行指定的活动。 

1. 现在，将 For-Each 活动添加到管道。 此活动将处理 Lookup 活动返回的数据。 
    * 在“活动”窗格中的“迭代和条件”下，选择“ForEach”活动并将其拖放到画布中。************
    * 在画布中 Lookup 活动的输出与 ForEach 活动的输入之间绘制一条线，以将其连接起来。

        ![ForEach 活动](media/data-factory-command-activity/for-each-activity.png)

1.  在画布中选择 ForEach 活动。 在下面的“设置”选项卡中：****
    * 选中“顺序”复选框以按顺序处理 Lookup 结果，或将其保留为未选中状态以创建并行处理。****
    * 设置“批计数”。****
    * 在“项”中，提供对输出值的以下引用：*@activity('Lookup1').output.value*****

       ![ForEach 活动设置](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>在 ForEach 活动中创建 Azure 数据资源管理器命令活动

1. 在画布中双击 ForEach 活动，在新画布中将其打开，以指定 ForEach 中的活动。
1. 在“活动”窗格中的“Azure 数据资源管理器”下，选择“Azure 数据资源管理器命令”活动，并将其拖放到画布中。************

    ![Azure 数据资源管理器命令活动](media/data-factory-command-activity/adx-command-activity.png)

1.  在“连接”选项卡中，选择前面创建的同一链接服务。****

    ![Azure 数据资源管理器命令活动连接选项卡](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. 在“命令”选项卡中提供以下命令：****

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    该**命令**指示 Azure 数据资源管理器将给定查询的结果以压缩格式导出到 Blob 存储中。 该命令以异步方式运行（使用 async 修饰符）。
    查询将寻址 Lookup 活动结果中每一行的数据库列。 “命令超时”可保持不变。****

    ![命令活动](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > 命令活动具有以下限制：
    > * 大小限制：1 MB 响应大小
    > * 时间限制：20 分钟（默认值），1 小时（最大值）。
    > * 如果需要，可以使用 [AdminThenQuery](/azure/kusto/management/index#combining-queries-and-control-commands) 将查询追加到结果，以减少最终的大小/时间。

1.  管道现已准备就绪。 可以单击管道名称返回到主管道视图。

    ![Azure 数据资源管理器命令管道](media/data-factory-command-activity/adx-command-pipeline.png)

1. 在发布管道之前选择“调试”。**** 可以在“输出”选项卡中监视管道进度。****

    ![Azure 数据资源管理器命令活动输出](media/data-factory-command-activity/command-activity-output.png)

1. 可以依次选择“全部发布”和“添加触发器”来运行管道。******** 

## <a name="control-command-outputs"></a>控制命令输出

下面详细说明了命令活动输出的结构。 此输出可由管道中的下一个活动使用。

### <a name="returned-value-of-a-non-async-control-command"></a>非异步控制命令的返回值

在非异步控制命令中，返回值的结构类似于 Lookup 活动结果的结构。 `count` 字段指示返回的记录数。 固定数组字段 `value` 包含记录列表。 

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
 
### <a name="returned-value-of-an-async-control-command"></a>异步控制命令的返回值

在异步控制命令中，活动在后台轮询操作表，直到异步操作完成或超时。因此，返回的值将包含给定`.show operations OperationId`**的"操作 Id"** 属性的结果。 请检查“状况”和“状态”属性的值，以确认操作是否成功完成。********

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

* 了解如何[使用 Azure 数据工厂将数据复制到 Azure 数据资源管理器](data-factory-load-data.md)。
* 了解如何[使用 Azure 数据工厂模板从数据库批量复制到 Azure 数据资源管理器](data-factory-template.md)。
