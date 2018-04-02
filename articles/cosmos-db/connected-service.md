---
title: Azure Cosmos DB 的 Visual Studio 连接服务
description: 可让开发人员轻松连接其 Azure Cosmos DB 帐户，并通过 Visual Studio 连接服务管理资源
services: cosmos-db
documentationcenter: ''
author: jejiang
manager: DJ
+tags: cosmos-db
editor: Jenny Jiang
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jejiang
ms.openlocfilehash: 93be368d34f02e64d11abe9a04b11272ce18124d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="azure-cosmos-db-visual-studio-connected-service-preview"></a>Azure Cosmos DB：Visual Studio 连接服务（预览）

Visual Studio 连接服务可让开发人员轻松连接其 Azure Cosmos DB 帐户并管理其资源。

还可以使用连接服务中的数据资源管理器创建存储过程、UDF 和触发器以执行服务器端业务逻辑。 数据资源管理器公开 API 中提供的所有内置编程数据访问，但可以使用它轻松访问数据。

## <a name="prerequisites"></a>先决条件

请确保具有以下项：

* 有效的 Azure 帐户。 如果没有，可以注册 [免费帐户](https://azure.microsoft.com/free/)。 
* 一个 Azure Cosmos DB 帐户。 如果没有帐户，请遵循[创建 Azure Cosmos DB 帐户](create-sql-api-dotnet.md)中的步骤在 Azure 门户中创建一个帐户，或参阅[在连接服务工具中创建 Azure Cosmos DB 帐户](#Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool)。 
* 如果想要出于开发目的使用本地环境，可以使用 [Azure Cosmos DB 模拟器](local-emulator.md)。 该环境可以模拟 Azure Cosmos DB 服务。
* [Visual Studio](http://www.visualstudio.com/)。
* 最新的 Azure Cosmos DB 连接服务软件。 可按以下屏幕截图所示，从 Visual Studio Marketplace 下载 Azure Cosmos DB 连接服务。 在计算机中打开 **Visual Studio**。 在“工具”菜单中选择“扩展和更新...”，再选择“联机” / “Visual Studio Marketplace”。 输入 **cosmosdb** 搜索该软件。

    也可以从 [Visual Studio Marketplace](https://go.microsoft.com/fwlink/?linkid=858709) 安装 Azure Cosmos DB 连接服务。

    ![连接服务下载软件的屏幕截图 (.png)](./media/connected-service/connected-service-downloadbits.png) 

下载 Azure Cosmos DB 连接服务扩展后，请关闭 Visual Studio 以安装该扩展。

## <a id="SetupVS"></a>设置 Visual Studio 解决方案
1. 在计算机上打开 Visual Studio。
2. 在“文件”菜单中，选择“新建”，并选择“项目”。
3. 在“新建项目”对话框中，选择“Visual C#” / “控制台应用(.NET Framework)”或选择“WPF 应用(.NET Framework)”，为项目命名，单击“确定”。

    ![“新建项目”窗口屏幕截图](./media/connected-service/connected-service-new-project.png)
    
## <a name="add-connected-service-and-add-account"></a>添加连接服务和帐户
1. 在解决方案资源管理器中，右键单击项目节点，选择“添加” / “连接服务”。 或者单击“项目”菜单，选择“添加连接服务”。

    ![“添加连接服务”窗口的屏幕截图](./media/connected-service/connected-service-add-connectedservice-rightclick.png)
2. 在“连接服务”页中，单击“连接服务” / “Azure Cosmos DB”打开“Azure Cosmos DB”页。

    ![“Azure Cosmos DB”窗口的屏幕截图](./media/connected-service/connected-service-choose-azure-cosmosdb.png)
3. 单击向下箭头进行首次登录或添加帐户。 登录后，空白区域中会显示所有 Azure Cosmos DB 帐户。 选择一个要添加到项目中的 Azure Cosmos DB 帐户。

    ![登录选项和列出的数据库帐户窗口屏幕截图](./media/connected-service/connected-service-add-db-account.png)
4. 添加 Azure Cosmos DB 帐户后，会在项目中添加一个 Azure Cosmos DB 帐户连接服务文件夹。 可以重复步骤 1 到 3 添加多个 Azure Cosmos DB 帐户。

    ![连接服务文件夹窗口的屏幕截图](./media/connected-service/connected-service-add-connectedservice-folder.png)

5. 添加 Azure Cosmos DB 连接服务后，请修改项目，以便能够通过以下方式之一访问 Azure Cosmos DB：

    * 已安装 Azure Cosmos DB 客户端所需的某些 Nuget 包。 可以在包配置文件中看到这些包。 

        ![新的 Azure Cosmos DB 包配置](./media/connected-service/connected-service-packages-config.png)   
    
    * Azure Cosmos DB 连接 URI 和密钥已添加到项目配置文件（在本例中为 App.config）。 

        ![新的 Azure Cosmos DB 应用配置](./media/connected-service/connected-service-app-config.png) 

## <a name="open-azure-cosmos-db-explorer"></a>打开 Azure Cosmos DB 资源管理器
1. 右键单击项目节点，选择“打开 Cosmos DB 资源管理器...”。

    ![尝试打开数据资源管理器窗口的屏幕截图](./media/connected-service/connected-service-right-click-open-data-exporer.png)
2. 在“选择 Cosmos DB 帐户”页中，单击下拉列表选择一个 Azure Cosmos DB 帐户。

    ![“已添加帐户连接服务”窗口的屏幕截图](./media/connected-service/connected-service-open-explorer.png)
3. 单击“打开”，随即会显示数据资源管理器窗口。

## <a id="Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool"></a>在连接服务工具中创建 Azure Cosmos DB 帐户
1. 在“连接服务”页上的窗格左下角，单击“创建新的 Cosmos DB 帐户”打开“创建 Cosmos DB 帐户”页。

    ![打开“创建 Azure Cosmos DB 帐户”入口点窗口的屏幕截图](./media/connected-service/connected-service-click-new-db-account.png)
2. 在“创建 Cosmos DB 帐户”页上，为此 Azure Cosmos DB 帐户指定所需的配置。

    参考以下屏幕截图中的信息，填写“创建 Cosmos DB 帐户”页上的字段。 
 
    ![新 Azure Cosmos DB 页](./media/connected-service/connected-service-create-new-account.png)        
3. 单击“创建”  以创建帐户。

## <a name="use-data-explorer"></a>使用数据资源管理器

打开数据资源管理器后，可执行以下操作：
* 创建和删除数据库
* 创建和删除集合
* 创建、删除和筛选文档
* 创建和删除存储过程
* 创建和删除触发器与用户定义的函数以执行服务器端业务逻辑。 

![新 Azure Cosmos DB 页](./media/connected-service/connected-service-dataexplorerui.png)

## <a name="demo"></a>演示

观看以下视频，了解如何在 Visual Studio 中使用 Azure Cosmos DB 连接服务：[在 Visual Studio 中使用 Azure Cosmos DB 连接服务](https://go.microsoft.com/fwlink/?linkid=858711)

## <a name="next-steps"></a>后续步骤
本文档已介绍以下操作：

> [!div class="checklist"]
> * 创建 Azure Cosmos DB 帐户
> * 添加连接服务和帐户
> * 打开 Azure Cosmos DB 资源管理器
> * 使用数据资源管理器

使用 Azure Cosmos DB 帐户启动并运行连接服务后，请继续阅读以下教程之一，以开始开发解决方案：

* [在 .NET 中使用 SQL API 进行开发](tutorial-develop-sql-api-dotnet.md)。
* [Azure Cosmos DB：SQL API 入门教程](sql-api-get-started.md)。
* 希望使用 Azure Cosmos DB 执行规模和性能测试？ 请参阅[使用 Azure Cosmos DB 执行性能和缩放测试](performance-testing.md)。
* 了解如何[监视 Azure Cosmos DB 帐户](monitor-accounts.md)。

