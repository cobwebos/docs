---
title: 适用于 Azure 的 MongoDB、Angular 和 Node 教程 - 第 4 部分 | Microsoft Docs
description: 本教程系列的第 4 部分，介绍如何通过 Angular 和 Node 在 Azure Cosmos DB 上创建 MongoDB 应用，所使用的 API 与用于 MongoDB 的 API 完全相同
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: e06a88ab54ffd87d1071136b53085c32bfcd8c78
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38667825"
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-4-create-an-azure-cosmos-db-account-using-the-azure-cli"></a>通过 Angular 和 Azure Cosmos DB 创建 MongoDB 应用 - 第 4 部分：使用 Azure CLI 创建 Azure Cosmos DB 帐户

本教程包含多个部分，演示了如何通过 Express、Angular 和 Azure Cosmos DB 数据库创建以 Node.js 编写的新 [MongoDB API](mongodb-introduction.md) 应用。

本教程的第 4 部分基于[第 3 部分](tutorial-develop-mongodb-nodejs-part3.md)，涵盖以下任务：

> [!div class="checklist"]
> * 使用 Azure CLI 创建 Azure 资源组
> * 使用 Azure CLI 创建 Azure Cosmos DB 帐户

## <a name="video-walkthrough"></a>视频演练

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>先决条件

开始教程的此部分之前，请确保已完成教程[第 3 部分](tutorial-develop-mongodb-nodejs-part3.md)的步骤。 

在此教程部分，你可以使用 Azure Cloud Shell（位于 Internet 浏览器中）或在本地安装的 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。 如果在本地使用 Azure CLI，请确保运行 Azure CLI 2.0 或更高版本。 在命令提示符处运行 `az --version` 可以查看版本。 

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> 本教程介绍生成应用程序的各个步骤。 若要下载完成的项目，可从 GitHub 上的 [angular-cosmosdb 存储库](https://github.com/Azure-Samples/angular-cosmosdb)获取完成的应用程序。

## <a name="create-an-azure-cosmos-db-account"></a>创建 Azure Cosmos DB 帐户

使用 [`az cosmosdb create`](/cli/azure/cosmosdb#az_cosmosdb_create) 命令创建 Azure Cosmos DB 帐户。

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

* 若要将自己的唯一 Azure Cosmos DB 帐户名称用于 `<cosmosdb-name>`，该名称在 Azure 的所有 Azure Cosmos DB 帐户名称中必须是唯一的。
* `--kind MongoDB` 设置允许 Azure Cosmos DB 进行 MongoDB 客户端连接。

完成该命令可能需要一到两分钟的时间。 完成后，Terminal 窗口会显示新数据库的相关信息。 

创建 Azure Cosmos DB 帐户以后，请执行以下操作：
1. 打开新的浏览器窗口，访问 [https://portal.azure.com](https://portal.azure.com)
1. 单击左侧栏中的 Azure Cosmos DB 徽标 ![Azure 门户中的 Azure Cosmos DB 图标](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-icon.png) ，然后就会显示你的所有 Azure Cosmos DB。
1. 单击刚创建的 Azure Cosmos DB 帐户，选择“概览”选项卡，向下滚动，以便查看数据库所在的映射。 

    ![Azure 门户中的新 Azure Cosmos DB 帐户](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-angular-portal.png)

4. 在左侧导航中向下滚动，单击“以全局方式复制数据”选项卡。此时会显示一个映射，可以在其中看到允许将数据复制到其中的不同区域。 例如，可以单击“澳大利亚东南部”或“澳大利亚东部”，然后将数据复制到澳大利亚。 若要详细了解全局复制，可参阅[如何使用 Azure Cosmos DB 在全球范围内分发数据](distribute-data-globally.md)。 至于现在，我们只需保留这一个实例，这样在需要复制时，我们就知道如何去做。

    ![Azure 门户中的新 Azure Cosmos DB 帐户](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-replicate-portal.png)

## <a name="next-steps"></a>后续步骤

在本教程的此部分，你已完成以下操作：

> [!div class="checklist"]
> * 使用 Azure CLI 创建 Azure 资源组
> * 使用 Azure CLI 创建 Azure Cosmos DB 帐户

你可以转到本教程的下一部分，了解如何使用 Mongoose 将 Azure Cosmos DB 连接到应用。

> [!div class="nextstepaction"]
> [使用 Mongoose 连接到 Azure Cosmos DB](tutorial-develop-mongodb-nodejs-part5.md)
