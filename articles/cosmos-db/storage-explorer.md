---
title: 在 Azure 存储资源管理器中管理 Azure Cosmos DB
description: 了解如何在 Azure 存储资源管理器中管理 Azure Cosmos DB。
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: omafnan
editor: ''
tags: Azure Cosmos DB
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2018
ms.author: jejiang
ms.openlocfilehash: 18f580f1eae31c9bf3626e100217467bb48ca881
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="manage-azure-cosmos-db-in-azure-storage-explorer-preview"></a>在 Azure 存储资源管理器（预览版）中管理 Azure Cosmos DB

通过在 Azure 存储资源管理器中使用 Azure Cosmos DB，用户可以管理 Azure Cosmos DB 实体、操作数据、更新存储过程和触发器以及其他 Azure 实体（如存储 blob 和队列）。 现在可以使用相同工具在一个位置管理不同 Azure 实体。 目前，Azure 存储资源管理器支持 SQL、MongoDB、Graph 和表帐户。

在本文中，你可以了解如何使用存储资源管理器管理 Azure Cosmos DB。


## <a name="prerequisites"></a>先决条件

适用于 SQL API<!--or MongoDB API--> 的 Azure Cosmos DB 帐户。 如果你没有帐户，则可以按照 [Azure Cosmos DB：使用 .NET 和 Azure 门户生成 SQL API Web 应用](create-sql-api-dotnet.md)中所述，在 Azure 门户中创建一个。

## <a name="installation"></a>安装

在此处安装最新的 Azure 存储资源管理器版本：[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)，现在我们支持 Windows、Linux 和 MAC 版本。

## <a name="connect-to-an-azure-subscription"></a>连接到 Azure 订阅

1. 安装 **Azure 存储资源管理器**之后，单击左侧的**插件**图标，如下图中所示：
       
   ![插件图标](./media/storage-explorer/plug-in-icon.png)
 
2. 选择“添加 Azure 帐户”，然后单击“登录”。

   ![连接到 Azure 订阅](./media/storage-explorer/connect-to-azure-subscription.png)

2. 在“Azure 登录”对话框中，选择“登录”，然后输入 Azure 凭据。

    ![登录](./media/storage-explorer/sign-in.png)

3. 从列表中选择你的订阅，然后单击“应用”。

    ![应用](./media/storage-explorer/apply-subscription.png)

    资源管理器窗格会更新，并显示所选订阅中的帐户。

    ![帐户列表](./media/storage-explorer/account-list.png)

    现已成功将 **Cosmos DB 帐户**连接到 Azure 订阅。

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>使用连接字符串连接到 Azure Cosmos DB

连接到 Azure Cosmos DB 的一种替代方法是使用连接字符串。 按照以下步骤可使用连接字符串进行连接。

1. 在左侧树中找到“本地和附加”，右键单击“Cosmos DB 帐户”，然后选择“连接到 Cosmos DB...”

    ![通过连接字符串连接到 Cosmos DB](./media/storage-explorer/connect-to-db-by-connection-string.png)

2. 目前仅支持 SQL 和表 API。 选择“API”，粘贴**连接字符串**，输入**帐户标签**，单击“下一步”查看摘要，然后单击“连接”以连接 Azure Cosmos DB 帐户。 有关检索连接字符串的信息，请参阅[获取连接字符串](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string)。

    ![连接字符串](./media/storage-explorer/connection-string.png)

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>使用本地模拟器连接到 Azure Cosmos DB
使用以下步骤通过模拟器连接到 Azure Cosmos DB（目前仅支持 SQL 帐户）。
1. 安装并启动模拟器。 有关如何安装模拟器的信息，请参阅 [Cosmos DB 模拟器](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator)
2. 在左侧树中找到“本地和附加”，右键单击“Cosmos DB 帐户”，然后选择“连接到 Cosmos DB 模拟器...”

    ![通过模拟器连接到 Cosmos DB](./media/storage-explorer/emulator-entry.png)

3. 目前仅支持 SQL API。 粘贴**连接字符串**，输入**帐户标签**，单击“下一步”查看摘要，然后单击“连接”以连接 Azure Cosmos DB 帐户。 有关检索连接字符串的信息，请参阅[获取连接字符串](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string)。

    ![通过模拟器对话框连接到 Cosmos DB](./media/storage-explorer/emulator-dialog.png)



## <a name="azure-cosmos-db-resource-management"></a>Azure Cosmos DB 资源管理

你可以通过执行以下操作来管理 Azure Cosmos DB 帐户：
* 在 Azure 门户中打开帐户
* 将资源添加到快速访问列表
* 搜索和刷新资源
* 创建和删除数据库
* 创建和删除集合
* 创建、编辑、删除和筛选文档
* 管理存储过程、触发器和用户定义的函数

### <a name="quick-access-tasks"></a>快速访问任务

通过在资源管理器窗格中右键单击订阅，可以执行许多快速操作任务：

* 右键单击 Azure Cosmos DB 帐户或数据库，可以选择“在门户中打开”，然后在 Azure 门户上通过浏览器管理资源。

     ![在门户中打开](./media/storage-explorer/open-in-portal.png)

* 还可以将 Azure Cosmos DB 帐户、数据库、集合添加到“快速访问”。
* 通过“从此处搜索”可以在所选路径下进行关键字搜索。

    ![从此处搜索](./media/storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>数据库和集合管理
#### <a name="create-a-database"></a>创建数据库 
-   右键单击 Azure Cosmos DB 帐户，选择“创建数据库”，输入数据库名称，然后按 **Enter** 以完成。
       
    ![创建数据库](./media/storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>删除数据库
- 右键单击数据库，单击“删除数据库”，然后在弹出窗口中单击“是”。 数据库节点会删除，并且 Azure Cosmos DB 帐户会自动刷新。

    ![删除数据库 1](./media/storage-explorer/delete-database1.png)  

    ![删除数据库 2](./media/storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>创建集合
1. 右键单击数据库，选择“创建集合”，然后提供以下信息，如“集合 ID”、“存储容量”等。单击“确定”完成。 

    ![创建集合 1](./media/storage-explorer/create-collection.png)

    ![创建集合 2](./media/storage-explorer/create-collection2.png) 

2. 选择“无限制”以便能够指定分区键，然后单击“确定”完成操作。

    如果在创建集合时使用分区键，则一旦创建完成，便无法对集合更改分区键值。 有关分区键设置的信息，请参阅[分区设计](partition-data.md#designing-for-partitioning)。

    ![分区键](./media/storage-explorer/partitionkey.png)

#### <a name="delete-a-collection"></a>删除集合
- 右键单击集合，单击“删除集合”，然后在弹出窗口中单击“是”。 

    集合节点会删除，并且数据库会自动刷新。

    ![删除集合](./media/storage-explorer/delete-collection.png) 

### <a name="document-management"></a>文档管理

#### <a name="create-and-modify-documents"></a>创建和修改文档
- 若要创建新文档，请打开左窗口中的“文档”，单击“新建文档”，在右窗格中编辑内容，然后单击“保存”。 还可以更新现有文档，然后单击“保存”。 可以通过单击“放弃”来放弃更改。

    ![文档](./media/storage-explorer/document.png)

#### <a name="delete-a-document"></a>删除文档
- 单击“删除”按钮以删除所选文档。

#### <a name="query-for-documents"></a>查询文档
- 通过输入 [SQL 查询](sql-api-sql-query.md)来编辑文档筛选器，然后单击“应用”。

    ![文档筛选器](./media/storage-explorer/document-filter.png)



### <a name="graph-management"></a>图形管理

#### <a name="create-and-modify-vertex"></a>创建和修改顶点
1. 若要创建新顶点，请在左窗口中打开“图形”，单击“新建顶点”，编辑内容，然后单击“确定”。    
2. 若要修改现有顶点，请在右窗格中单击铅笔图标。   

    ![图形](./media/storage-explorer/vertex.png)

#### <a name="delete-a-graph"></a>创建图形
- 若要删除某个顶点，单击顶点名称旁边的回收站图标。

#### <a name="filter-for-graph"></a>筛选图形
- 通过输入 [gremlin 查询](gremlin-support.md)来编辑图形筛选器，然后单击“应用筛选器”。

    ![图形筛选器](./media/storage-explorer/graph-filter.png)

### <a name="table-management"></a>表管理

#### <a name="create-and-modify-table"></a>创建和修改表
1. 若要创建新表，请在左窗口中打开“实体”，单击“添加”，在“添加实体”对话框中编辑内容，单击“添加属性”按钮添加属性，然后单击“插入”。
2. 若要修改表，请单击“编辑”，修改内容，然后单击“更新”。

    ![表](./media/storage-explorer/table.png)

#### <a name="import-and-export-table"></a>导入和导出表
1. 若要导入，请单击“导入”按钮并选择现有的表。
2. 若要导出，请单击“导出”按钮并选择目标。

    ![表导入和导出](./media/storage-explorer/table-import-export.png)

#### <a name="delete-entities"></a>删除实体
- 选择实体，然后单击“删除”按钮。

    ![表删除](./media/storage-explorer/table-delete.png)

#### <a name="query-table"></a>查询表
- 单击“查询”按钮，输入查询条件，然后单击“执行查询”按钮。 单击“关闭查询”按钮关闭“查询”窗格。

    ![表查询](./media/storage-explorer/table-query.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>管理存储过程、触发器和 UDF
* 若要创建存储过程，请在左侧树中，右键单击“存储过程”，选择“创建存储过程”，在左侧输入名称，在右侧窗口中输入存储过程脚本，然后单击“创建”。 
* 还可以双击，进行更新，然后单击“更新”保存，从而编辑现有存储过程，或单击“放弃”以取消更改。

    ![存储过程](./media/storage-explorer/stored-procedure.png)
* 适用于**触发器**和 **UDF** 的操作与**存储过程**类似。

## <a name="next-steps"></a>后续步骤

* 观看以下视频，了解如何在 Azure 存储资源管理器中使用 Azure Cosmos DB：[在 Azure 存储资源管理器中使用 Azure Cosmos DB](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be)。
* 在[存储资源管理器（预览版）入门](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)中了解有关存储资源管理器和连接更多服务的详细信息。

