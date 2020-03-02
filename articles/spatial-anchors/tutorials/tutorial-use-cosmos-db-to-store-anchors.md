---
title: 教程：使用 Azure Cosmos DB 共享定位点
description: 在本教程中，你会了解如何使用后端服务和 Azure Cosmos DB 在 Unity 中的 Android/iOS 设备间共享 Azure 空间定位点标识符。
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 71b3027d86400d6921895f86e257ddff2961f91f
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77615154"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>教程：使用 Azure Cosmos DB 后端在会话和设备间共享 Azure 空间定位点

本教程是[在会话和设备之间共享 Azure 空间定位点的续篇。](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md) 本教程将指导你完成添加更多功能的过程，从而使 Azure Cosmos DB 充当后端存储，同时在会话和设备之间共享 Azure 空间定位点。

![演示对象持久性的 GIF](./media/persistence.gif)

值得注意到，虽然在本教程中会使用 Unity 和 Azure Cosmos DB，不过这只是为了提供有关如何在设备间共享空间定位点标识符的示例。 可以使用其他语言和后端技术实现相同目的。 此外，本教程中使用的 ASP.NET Core Web 应用需要 .NET Core 2.2 SDK。 它可在适用于 Windows 的 Web 应用上正常运行，但当前无法在适用于 Linux 的 Web 应用上运行。

## <a name="create-a-database-account"></a>创建数据库帐户

将 Azure Cosmos 数据库添加到之前创建的资源组。

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

复制 `Connection String`，因为你会需要它。

## <a name="make-minor-changes-to-the-sharingservice-files"></a>对 SharingService 文件做一些小修改

在“解决方案资源管理器”  中打开 `SharingService\Startup.cs`。

找到文件顶部的 `#define INMEMORY_DEMO`，然后将该行注释掉。保存文件。

在“解决方案资源管理器”  中打开 `SharingService\appsettings.json`。

找到 `StorageConnectionString` 属性，将值设置为与在[创建数据库帐户步骤](#create-a-database-account)中复制的 `Connection String` 值相同。 保存文件。

你可重新发布共享服务并运行示例应用。

## <a name="troubleshooting"></a>故障排除

### <a name="unity-20193"></a>Unity 2019.3

由于中断性变更，目前不支持 Unity 2019.3。 请使用 Unity 2019.1 或 2019.2。

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，使用了 Azure Cosmos DB 在设备间共享定位点标识符。 若要详细了解如何在新的 Unity HoloLens 应用中使用 Azure 空间定位点，请继续学习下一教程。

> [!div class="nextstepaction"]
> [启动新的 Unity HoloLens 应用](./tutorial-new-unity-hololens-app.md)
