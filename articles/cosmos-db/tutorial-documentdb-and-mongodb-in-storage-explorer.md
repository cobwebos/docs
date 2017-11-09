---
title: "在 Azure 存储资源管理器中管理 Azure Cosmos DB"
description: "了解如何在 Azure 存储资源管理器中管理 Azure Cosmos DB。"
Keywords: "Azure Cosmos DB, Azure 存储资源管理器, DocumentDB, MongoDB, DocumentDB"
services: cosmos-db
documentationcenter: 
author: Jiaj-Li
manager: omafnan
editor: 
tags: Azure Cosmos DB
ms.assetid: 
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: Jiaj-Li
ms.openlocfilehash: e695cdd7c51e18a386764ab8444d3336366ae265
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2017
---
# <a name="manage-azure-cosmos-db-in-azure-storage-explorer-preview"></a>在 Azure 存储资源管理器（预览版）中管理 Azure Cosmos DB

通过在 Azure 存储资源管理器中使用 Azure Cosmos DB，用户可以管理 Azure Cosmos DB 实体、操作数据、更新存储过程和触发器以及其他 Azure 实体（如存储 blob 和队列）。 现在可以使用相同工具在一个位置管理不同 Azure 实体。 当前，Azure 存储资源管理器支持 SQL (DocumentDB) 和 MongoDB 帐户。

在本文中，你可以了解如何使用存储资源管理器管理 Azure Cosmos DB。


## <a name="prerequisites"></a>先决条件

用于 SQL (DocumentDB) 或 MongoDB 数据库的 Azure Cosmos DB 帐户。 如果你没有帐户，则可以按照 [Azure Cosmos DB：使用 .NET 和 Azure 门户生成 DocumentDB API Web 应用](create-documentdb-dotnet.md)中所述，在 Azure 门户中创建一个。

## <a name="installation"></a>安装

在此处安装最新的 Azure 存储资源管理器版本：[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)，现在我们支持 Windows、Linux 和 MAC 版本。

## <a name="connect-to-an-azure-subscription"></a>连接到 Azure 订阅

1. 安装 **Azure 存储资源管理器**之后，单击左侧的**插件**图标，如下图中所示。
       
   ![插件图标](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/plug-in-icon.png)
 
2. 选择“添加 Azure 帐户”，然后单击“登录”。

   ![连接到 Azure 订阅](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connect-to-azure-subscription.png)

2. 在“Azure 登录”对话框中，选择“登录”，然后输入 Azure 凭据。

    ![登录](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/sign-in.png)

3. 从列表中选择你的订阅，然后单击“应用”。

    ![应用](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/apply-subscription.png)

    资源管理器窗格会更新，并显示所选订阅中的帐户。

    ![帐户列表](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/account-list.png)

    你已成功将 **Azure Cosmos DB 帐户**连接到你的 Azure 订阅。

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>使用连接字符串连接到 Azure Cosmos DB

连接到 Azure Cosmos DB 的一种替代方法是使用连接字符串。 按照以下步骤可使用连接字符串进行连接。

1. 在左侧树中找到“本地和附加”，右键单击“Azure Cosmos DB 帐户”，然后选择“连接到 Azure Cosmos DB...”

    ![通过连接字符串连接到 Azure Cosmos DB](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connect-to-db-by-connection-string.png)

2. 为你的帐户类型选择相应“默认体验”（“DocumentDB”或“MongoDB”），粘贴你的“连接字符串”，然后单击“确定”以连接 Azure Cosmos DB 帐户。 有关检索连接字符串的信息，请参阅[获取连接字符串](https://docs.microsoft.com/en-us/azure/cosmos-db/manage-account#get-the--connection-string)。

    ![连接字符串](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connection-string.png)

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

     ![在门户中打开](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/open-in-portal.png)

* 还可以将 Azure Cosmos DB 帐户、数据库、集合添加到“快速访问”。
* 通过“从此处搜索”可以在所选路径下进行关键字搜索。

    ![从此处搜索](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>数据库和集合管理
#### <a name="create-a-database"></a>创建数据库 
右键单击 Azure Cosmos DB 帐户，选择“创建数据库”，输入数据库名称，然后按 **Enter** 以完成。

![创建数据库](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>删除数据库
右键单击数据库，单击“删除数据库”，然后在弹出窗口中单击“是”。 数据库节点会删除，并且 Azure Cosmos DB 帐户会自动刷新。

![删除数据库 1](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-database1.png)  

![删除数据库 2](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>创建集合
右键单击数据库，选择“创建集合”，然后提供以下信息，如“集合 ID”、“存储容量”等。单击“确定”完成。 有关分区键设置的信息，请参阅[分区设计](partition-data.md#designing-for-partitioning)。

如果在创建集合时使用分区键，则一旦创建完成，便无法对集合更改分区键值。

![创建集合 1](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-collection.png)

![创建集合 2](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-collection2.png) 

#### <a name="delete-a-collection"></a>删除集合
右键单击集合，单击“删除集合”，然后在弹出窗口中单击“是”。 

集合节点会删除，并且数据库会自动刷新。  

![删除集合](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-collection.png) 

### <a name="document-management"></a>文档管理

#### <a name="create-and-modify-documents"></a>创建和修改文档
若要创建新文档，请打开左窗口中的“文档”，单击“新建文档”，在右窗格中编辑内容，然后单击“保存”。 还可以更新现有文档，然后单击“保存”。 可以通过单击“放弃”来放弃更改。

![文档](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/document.png)

#### <a name="delete-a-document"></a>删除文档
单击“删除”按钮以删除所选文档。
#### <a name="query-for-documents"></a>查询文档
通过输入 [SQL 查询](documentdb-sql-query.md)来编辑文档筛选器，然后单击“应用”。

![筛选器](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/filter.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>管理存储过程、触发器和 UDF
* 若要创建存储过程，请在左侧树中，右键单击“存储过程”，选择“创建存储过程”，在左侧输入名称，在右侧窗口中输入存储过程脚本，然后单击“创建”。 
* 还可以双击，进行更新，然后单击“更新”保存，从而编辑现有存储过程，或单击“放弃”以取消更改。

![存储过程](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/stored-procedure.png)

* 用于**触发器**和 **UDF** 的操作与用于**存储过程**的操作类似。

## <a name="next-steps"></a>后续步骤

* 观看以下视频，了解如何在 Azure 存储资源管理器中使用 Azure Cosmos DB：[在 Azure 存储资源管理器中使用 Azure Cosmos DB](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be)。
* 在[存储资源管理器（预览版）入门](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer)中了解有关存储资源管理器和连接更多服务的详细信息。

