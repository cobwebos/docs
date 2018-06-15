---
title: 适用于 Azure 的 MongoDB、Angular 和 Node 教程 | Microsoft Docs
description: 了解如何通过 Angular 和 Node 在 Azure Cosmos DB 上创建 MongoDB 应用，所使用的 API 与本视频教程系列中用于 MongoDB 的 API 完全相同。
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
ms.openlocfilehash: 24470534d9e7c626c99a4992c558efb95552fada
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763215"
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db"></a>通过 Angular 和 Azure Cosmos DB 创建 MongoDB 应用 

本教程包含多个部分，演示了如何通过 Express、Angular 和 Node.js（MEAN 堆栈）创建新的 [MongoDB API](mongodb-introduction.md) 应用，然后将其连接到 Azure Cosmos DB 数据库。 Azure Cosmos DB 支持 MongoDB 客户端连接，因此可以用 Azure Cosmos DB 来代替 MongoDB，所用代码与用于 MongoDB 应用的代码相同，而优势则会增强。 Azure Cosmos DB 的优势包括：简化云部署、缩放、安全、全局复制数据、多模型支持，以及超快的读取和写入速度。 

Azure Cosmos DB 是 Microsoft 提供的全球分布式多模型数据库服务。 可以通过它来快速创建和查询文档、键/值和图形数据库，它们都受益于 Azure Cosmos DB 核心的全球分布和水平缩放功能。 

本教程分为多个部分，涵盖以下任务：

> [!div class="checklist"]
> * [通过 Angular CLI 创建 Node.js Express 应用](tutorial-develop-mongodb-nodejs-part2.md)
> * [通过 Angular 生成 UI](tutorial-develop-mongodb-nodejs-part3.md)
> * [使用 Azure CLI 创建 Azure Cosmos DB 帐户](tutorial-develop-mongodb-nodejs-part4.md) 
> * [使用 Mongoose 连接到 Azure Cosmos DB](tutorial-develop-mongodb-nodejs-part5.md)
> * [向应用添加 Post、Put 和 Delete 函数](tutorial-develop-mongodb-nodejs-part6.md)

想要通过 React 生成这个相同的应用？ 请参阅 [React 教程视频系列](tutorial-develop-mongodb-react.md)。

## <a name="video-walkthrough"></a>视频演练

> [!VIDEO https://www.youtube.com/embed/vlZRP0mDabM]

## <a name="finished-project"></a>完成的项目 

本教程介绍生成应用程序的各个步骤。 若要下载完成的项目，可从 GitHub 上的 [angular-cosmosdb 存储库](https://github.com/Azure-Samples/angular-cosmosdb)获取完成的应用程序。

## <a name="next-steps"></a>后续步骤

在本教程的此部分，你已完成以下操作：

> [!div class="checklist"]
> * 大致了解使用 Azure Cosmos DB 创建 MEAN.js 应用的步骤。 

你可以转到本教程的下一部分，了解如何创建 Node.js Express 应用。

> [!div class="nextstepaction"]
> [通过 Angular CLI 创建 Node.js Express 应用](tutorial-develop-mongodb-nodejs-part2.md)
