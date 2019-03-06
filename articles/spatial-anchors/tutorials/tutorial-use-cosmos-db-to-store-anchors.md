---
title: 教程 - 使用 Azure 空间定位点和 Azure Cosmos DB 后端在会话和设备间进行共享 | Microsoft Docs
description: 在本教程中，你会了解如何使用后端服务和 Azure Cosmos DB 在 Unity 中的设备之间共享 Azure 空间定位点标识符。
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: f0cd42fc37727099ed95a1c6fc2d427b7862412e
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753428"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors-and-an-azure-cosmos-db-back-end"></a>教程：使用 Azure 空间定位点和 Azure Cosmos DB 后端在会话和设备间进行共享

本教程会演示如何使用 [Azure 空间定位点](../overview.md)：

1. 在一个会话中创建定位点，然后在相同或不同设备上的另一个会话中查找它们。 例如，在另一天。
2. 创建可以由多个设备同时在相同位置查找的定位点。

![持久性](./media/persistence.gif)

[Azure 空间定位点](../overview.md)是一种跨平台开发人员服务。通过该服务，可以使用能够随时间推移跨设备保存自己位置的对象创建混合的现实体验。 完成后，会具有可以部署到两个或更多设备的应用。 由一个实例创建的 Azure 空间定位点会使用 Cosmos DB 将其标识符共享给其他实例。

将了解如何执行以下操作：

> [!div class="checklist"]
> * 在 Azure 中部署可以用于共享定位点（将它们存储在 Cosmos DB 中）的 ASP.NET Core Web 应用。
> * 配置来自快速入门的 Unity 示例中的 AzureSpatialAnchorsLocalSharedDemo 场景，以利用共享定位点 Web 应用。
> * 部署到一个或多个设备并运行。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

值得注意到，虽然在本教程中会使用 Unity 和 Azure Cosmos DB，不过这只是为了演示有关如何在其他设备间共享 Azure 空间定位点标识符的示例。 可以使用其他语言和后端技术实现相同目的。 此外，本教程中使用的 ASP.NET Core Web 应用依赖于 .NET Core 2.2 SDK。 它可在常规 Azure Web 应用（适用于 Windows）上运行良好，但当前不适用于 Linux 的 Azure Web 应用。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>创建数据库帐户

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

记下 `Connection String`，因为会在以后使用它。

## <a name="deploy-your-sharing-anchors-service"></a>部署共享定位点服务

打开 Visual Studio，然后在 `Sharing\SharingServiceSample` 文件夹中打开项目。

### <a name="configure-the-service-so-that-it-uses-your-cosmos-db"></a>配置服务，以便它使用 Cosmos DB

在“解决方案资源管理器”中，打开 `SharingService\Startup.cs`。

找到文件顶部的 `#define INMEMORY_DEMO` 行，然后将它注释掉。保存文件。

在“解决方案资源管理器”中，打开 `SharingService\appsettings.json`。

找到 `StorageConnectionString` 属性，将值设置为在[创建数据库帐户步骤](#create-a-database-account)中记下的 `Connection String`。 保存文件。

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，使用了 Azure Cosmos DB 在设备间共享定位点标识符。 若要了解有关 Azure 空间定位点库的详细信息，请继续阅读我们有关如何创建并找到定位点的教程。

> [!div class="nextstepaction"]
> [使用 Azure 空间定位点创建并找到定位点](../create-locate-anchors-overview.md)
