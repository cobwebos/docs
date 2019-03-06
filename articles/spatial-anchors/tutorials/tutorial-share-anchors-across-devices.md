---
title: 教程 - 使用 Azure 空间定位点在会话和设备间进行共享 | Microsoft Docs
description: 在本教程中，你会了解如何使用后端服务在 Unity 中的设备之间共享 Azure 空间定位点标识符。
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: df7f8aa5b49e3fe17be3b17a6e0f5d8861b26253
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753431"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors"></a>教程：使用 Azure 空间定位点在会话和设备间进行共享

本教程会演示如何使用 [Azure 空间定位点](../overview.md)：

1. 在一个会话中创建定位点，然后在相同或不同设备上的另一个会话中查找它们。 例如，在另一天。
2. 创建可以由多个设备同时在相同位置查找的定位点。

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

## <a name="deploy-your-sharing-anchors-service"></a>部署共享定位点服务

打开 Visual Studio，然后在 `Sharing\SharingServiceSample` 文件夹中打开项目。

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你在 Azure 中部署了 ASP.NET Core Web 应用，然后配置并部署了 Unity 应用。 你使用应用创建了空间定位点，然后使用 ASP.NET Core Web 应用与其他设备共享它们。 若要了解有关如何改进 ASP.NET Core Web 应用，以便它使用 Cosmos DB 存储共享空间定位点的详细信息，请继续阅读下一个教程。

> [!div class="nextstepaction"]
> [教程：使用 Cosmos DB 存储定位点](./tutorial-use-cosmos-db-to-store-anchors.md)
