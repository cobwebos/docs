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
ms.openlocfilehash: e8a60d5d90b684698d6fcb612278bcae6d4ed08e
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882314"
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

## <a name="download-the-sample-project"></a>下载示例项目

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>部署共享定位点服务

## <a name="visual-studiotabvs"></a>[Visual Studio](#tab/VS)

打开 Visual Studio，然后在 `Sharing\SharingServiceSample` 文件夹中打开项目。

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-codetabvsc"></a>[Visual Studio Code](#tab/VSC)

在 VS Code 中部署服务之前，需要创建资源组和应用服务计划。

### <a name="sign-in-to-azure"></a>登录到 Azure

导航到 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>并登陆到 Azure 订阅。

### <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

在“资源组”  旁边，选择“新建”  。

将资源组命名为 **myResourceGroup**，然后选择“确定”  。

### <a name="create-an-app-service-plan"></a>创建应用服务计划

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

在“托管计划”旁边  ，选择“新建”  。

在“配置托管计划”对话框中，使用以下设置： 

| 设置 | 建议的值 | 说明 |
|-|-|-|
|应用服务计划| MySharingServicePlan | 应用服务计划的名称。 |
| 位置 | 美国西部 | 托管 Web 应用的数据中心。 |
| 大小 | 免费 | 确定托管功能的[定价层](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 |

选择“确定”  。

打开 Visual Studio Code，然后在 `Sharing\SharingServiceSample` 文件夹中打开项目。 按照<a href="https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">本教程</a>通过 Visual Studio Code 部署共享服务。 可以按照“使用 Visual Studio Code 打开”部分中的步骤进行操作。 请不要按照上面步骤中的说明创建另一个 mvc 项目，因为你已有需要部署和发布的项目 - SharingServiceSample。

---

## <a name="deploy-the-sample-app"></a>部署示例应用

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你在 Azure 中部署了 ASP.NET Core Web 应用，然后配置并部署了 Unity 应用。 你使用应用创建了空间定位点，然后使用 ASP.NET Core Web 应用与其他设备共享它们。

可以改进 ASP.NET Core Web 应用，使其使用 Azure Cosmos DB 来保存共享的空间定位点标识符的存储。 添加 Azure Cosmos DB 支持可让 ASP.NET Core Web 应用立即创建一个定位点，然后过几天再回来查找它，只需使用存储在 Web 应用中的定位点标识符即可。

> [!div class="nextstepaction"]
> [使用 Azure Cosmo DB 存储定位点](./tutorial-use-cosmos-db-to-store-anchors.md)

