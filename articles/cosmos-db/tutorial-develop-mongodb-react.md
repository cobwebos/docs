---
title: 适用于 Azure 的 MongoDB、React 和 Node.js 教程 | Microsoft Docs
description: 了解如何通过 React 和 Node.js 在 Azure Cosmos DB 上创建 MongoDB 应用，所使用的 API 与本视频教程系列中用于 MongoDB 的 API 完全相同。
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
editor: ''
ms.assetid: ''
ms.service: cosmos-db
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: c2cb4b30cb056303c07d5e82861e4022489c7072
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-mongodb-app-with-react-and-azure-cosmos-db"></a>通过 React 和 Azure Cosmos DB 创建 MongoDB 应用  

本视频教程包含多个部分，演示了如何使用 React 前端创建 hero 跟踪应用。 该应用使用适用于服务器的 Node 和 Express，通过 [MongoDB API](mongodb-introduction.md) 连接到 Azure Cosmos DB，然后将 React 前端连接到应用的服务器部分。 本教程还演示了如何在 Azure 门户中对 Azure Cosmos DB 进行点击缩放，以及如何将应用部署到 Internet，方便每个人跟踪其最喜欢的 hero。 

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 支持 MongoDB 客户端连接，因此可以用 Azure Cosmos DB 来代替 MongoDB，所用代码与用于 MongoDB 应用的代码相同，而优势则会增强，例如简化云部署、缩放，大大加快读写速度。  

本教程分为多个部分，涵盖以下任务：

> [!div class="checklist"]
> * 介绍
> * 设置项目
> * 通过 React 生成 UI
> * 使用 Azure 门户创建 Azure Cosmos DB 帐户
> * 使用 Mongoose 连接到 Azure Cosmos DB
> * 向应用添加响应、创建、更新和删除操作

想要通过 Angular 生成这个相同的应用？ 请参阅 [Angular 教程视频系列](tutorial-develop-mongodb-nodejs.md)。

## <a name="prerequisites"></a>先决条件
* [Node.js](https://www.nodejs.org)

### <a name="finished-project"></a>完成的项目
从 [GitHub](https://github.com/Azure-Samples/react-cosmosdb) 获取完成的应用程序。

## <a name="introduction"></a>介绍 

在此视频中，Burke Holland 简单介绍了 Azure Cosmos DB，并详细讲解了在此视频系列中创建的应用。 

> [!VIDEO https://www.youtube.com/embed/58IflnJbYJc]

## <a name="project-setup"></a>项目设置

此视频介绍如何在同一项目中设置 Express 和 React。 Burke 随后详细介绍了项目中的代码。

> [!VIDEO https://www.youtube.com/embed/ytFUPStJJds]

## <a name="build-the-ui"></a>生成 UI

此视频介绍如何使用 React 创建应用程序的用户界面 (UI)。 

> [!NOTE]
> 可以在 [react-cosmosdb GitHub 存储库](https://github.com/Azure-Samples/react-cosmosdb/blob/master/src/index.css)中找到此视频中引用的 CSS。

> [!VIDEO https://www.youtube.com/embed/SzHzX0fTUUQ]

## <a name="connect-to-azure-cosmos-db"></a>连接到 Azure Cosmos DB

此视频介绍如何在 Azure 门户中创建 Azure Cosmos DB 帐户，安装 MongoDB 和 Mongoose 包，然后使用 Azure Cosmos DB 连接字符串将应用连接到新创建的帐户。 

> [!VIDEO https://www.youtube.com/embed/0U2jV1thfvs]

## <a name="read-and-create-heroes-in-the-app"></a>在应用中读取和创建 hero

此视频介绍如何在 Cosmos DB 数据库中读取 hero 和创建 hero，以及如何使用 Postman 和 React UI 测试这些方法。 

> [!VIDEO https://www.youtube.com/embed/AQK9n_8fsQI] 

## <a name="delete-and-update-heroes-in-the-app"></a>在应用中删除和更新 hero

此视频介绍如何在应用中删除和更新 hero，以及如何在 UI 中显示更新。 

> [!VIDEO https://www.youtube.com/embed/YmaGT7ztTQM] 

## <a name="complete-the-app"></a>完成应用

此视频介绍如何完成应用，以及如何将 UI 挂接到后端 API。 

> [!VIDEO https://www.youtube.com/embed/TcSm2ISfTu8]

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用，请使用以下步骤删除本教程在 Azure 门户中创建的所有资源。 

1. 在 Azure 门户的左侧菜单中，单击“资源组”，并单击已创建资源的名称。 
2. 在资源组页上单击“删除”，在文本框中键入要删除的资源的名称，并单击“删除”。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 通过 React、Node、Express 和 Azure Cosmos DB 创建应用 
> * 创建 Azure Cosmos DB 帐户
> * 将应用连接到 Azure Cosmos DB 帐户
> * 使用 Postman 测试应用
> * 运行应用程序并向数据库添加 hero

本教程系列会添加另一个视频，请返回观看，了解如何部署应用程序以及如何以全局方式复制数据。

你可以继续学习下一教程，了解如何将 MongoDB 数据导入 Azure Cosmos DB 中。  

> [!div class="nextstepaction"]
> [将 MongoDB 数据导入 Azure Cosmos DB](mongodb-migrate.md)
 
