---
title: 教程 - 在会话和设备间共享 Azure 空间定位点 | Microsoft Docs
description: 在本教程中，你会了解如何使用后端服务在 Unity 中的 Android/iOS 设备之间共享 Azure 空间定位点标识符。
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: ff9868dd7347812eb6ef566288ec364bc89b6955
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629307"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>教程：在会话和设备间共享 Azure 空间定位点

本教程介绍如何在一个会话期间使用 [Azure 空间定位点](../overview.md)来创建定位点，然后在相同或不同设备上查找它们。 也可由多个设备同时在相同位置查找这些相同的定位点。

![持久性](./media/persistence.gif)

Azure 空间定位点是一种跨平台开发人员服务。通过该服务，可以使用能够随时间推移跨设备保存自己位置的对象创建混合的现实体验。 完成后，会具有可以部署到两个或更多设备的应用。 由一个实例创建的 Azure 空间定位点可以共享给其他人。

将了解如何执行以下操作：

> [!div class="checklist"]
> * 在 Azure 中部署可以用于共享定位点（将它们存储在内存中一段时间）的 ASP.NET Core Web 应用。
> * 配置来自快速入门的 Unity 示例中的 AzureSpatialAnchorsLocalSharedDemo 场景，以利用共享定位点 Web 应用。
> * 部署到一个或多个设备并运行。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

值得注意到，虽然在本教程中会使用 Unity 和 ASP.NET Core Web 应用，不过这只是为了演示有关如何在其他设备间共享 Azure 空间定位点标识符的示例。 可以使用其他语言和后端技术实现相同目的。 此外，本教程中使用的 ASP.NET Core Web 应用依赖于 .NET Core 2.2 SDK。 它可在常规 Azure Web 应用（适用于 Windows）上运行良好，但当前不适用于 Linux 的 Azure Web 应用。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>在 Unity 中打开示例项目

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>部署共享定位点服务

打开 Visual Studio，然后在 `Sharing\SharingServiceSample` 文件夹中打开项目。

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你在 Azure 中部署了 ASP.NET Core Web 应用，然后配置并部署了 Unity 应用。 你使用应用创建了空间定位点，然后使用 ASP.NET Core Web 应用与其他设备共享它们。

若要详细了解如何改进 ASP.NET Core Web 应用，使其使用 Cosmos DB 存储共享的空间定位点标识符，请继续阅读下一教程。 Azure Cosmos DB 将为 ASP.NET Core Web 应用带来持久性。 这样应用就可以在今天创建一个定位点，然后过几天再回来查找它，只需使用存储在 Web 应用中的定位点标识符即可。

> [!div class="nextstepaction"]
> [教程：使用 Azure Cosmos DB 存储定位点](./tutorial-use-cosmos-db-to-store-anchors.md)
