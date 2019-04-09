---
title: 教程 - 使用 Azure Cosmos DB 后端在会话和设备间共享 Azure 空间定位点 | Microsoft Docs
description: 在本教程中，你会了解如何使用后端服务和 Azure Cosmos DB 在 Unity 中的 Android/iOS 设备间共享 Azure 空间定位点标识符。
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: d955654eee1d02994f303b6270b156eb9a61c29b
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "58915248"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>教程：使用 Azure Cosmos DB 后端在会话和设备间共享 Azure 空间定位点

本教程介绍如何在一个会话期间使用 [Azure 空间定位点](../overview.md)来创建定位点，然后在另一会话期间在相同或不同设备上查找它们。 例如，第二个会话可能是在不同的日期。 也可由多个设备同时在相同位置查找这些相同的定位点。

![演示对象持久性的 GIF](./media/persistence.gif)

[Azure 空间定位点](../overview.md)是一种跨平台开发人员服务，可以用于通过随时间推移跨设备保存自己位置的对象来创建混合的现实体验。 完成后，会具有可以部署到两个或更多设备的应用。 由一个实例创建的空间定位点会使用 Azure Cosmos DB 将其标识符与其他实例共享。

将了解如何执行以下操作：

> [!div class="checklist"]
> * 在 Azure 中部署可以用于共享定位点（将它们存储在 Azure Cosmos DB 中）的 ASP.NET Core Web 应用。
> * 配置来自 Azure 快速入门的 Unity 示例中的 AzureSpatialAnchorsLocalSharedDemo 场景，以利用共享定位点 Web 应用。
> * 将应用部署到一个或多个设备并运行。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

值得注意到，虽然在本教程中会使用 Unity 和 Azure Cosmos DB，不过这只是为了提供有关如何在设备间共享空间定位点标识符的示例。 可以使用其他语言和后端技术实现相同目的。 此外，本教程中使用的 ASP.NET Core Web 应用需要 .NET Core 2.2 SDK。 它可在适用于 Windows 的 Web 应用上正常运行，但当前无法在适用于 Linux 的 Web 应用上运行。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>创建数据库帐户

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

复制 `Connection String`，因为你会需要它。

## <a name="open-the-sample-project-in-unity"></a>在 Unity 中打开示例项目

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>部署共享定位点服务

打开 Visual Studio，然后在 `Sharing\SharingServiceSample` 文件夹中打开项目。

### <a name="configure-the-service-to-use-your-azure-cosmos-db-database"></a>将服务配置为使用 Azure Cosmos DB 数据库

在“解决方案资源管理器”中打开 `SharingService\Startup.cs`。

找到文件顶部的 `#define INMEMORY_DEMO`，然后将该行注释掉。保存文件。

在“解决方案资源管理器”中打开 `SharingService\appsettings.json`。

找到 `StorageConnectionString` 属性，将值设置为与在[创建数据库帐户步骤](#create-a-database-account)中复制的 `Connection String` 值相同。 保存文件。

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，使用了 Azure Cosmos DB 在设备间共享定位点标识符。 若要深入了解如何在新 Android 应用中使用 Azure 空间定位点，请继续学习下一教程。

> [!div class="nextstepaction"]
> [启动新的 Android 应用](./tutorial-new-android-app.md)
