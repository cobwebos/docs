---
title: 教程 - 清理 Azure Service Fabric 网格资源
description: 了解如何删除 Azure Service Fabric 网格资源，避免为不再使用的资源付费。
author: dkkapur
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: d5e33099d58c9cb4c110a78f2576d6eac83a7932
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "75351878"
---
# <a name="tutorial-remove-azure-resources"></a>教程：删除 Azure 资源

本教程是一个系列中的第五部分，介绍如何删除应用及其资源，以免为其付费。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 清理应用使用的资源，避免其产生费用。

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * [在 Visual Studio 中创建 Service Fabric 网格应用](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [调试在本地开发群集中运行的 Service Fabric 网格应用](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [部署 Service Fabric 网格应用](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [升级 Service Fabric 网格应用](service-fabric-mesh-tutorial-upgrade.md)
> * 清理 Service Fabric 网格资源

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前：

* 如果尚未部署待办事项应用，请按照[发布 Service Fabric 网格 Web 应用程序](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)中的说明操作。

## <a name="clean-up-resources"></a>清理资源

本教程到此结束。 使用完创建的资源时，请删除它们，以免为不再使用的资源付费。 这非常重要，因为网格是一种按秒计费的无服务器服务。 若要了解有关网格定价的详细信息，请查看 https://aka.ms/sfmeshpricing 。

Azure 提供的一项便利是：若创建的资源与特定资源组关联，则删除该资源组即可删除所有关联的资源。 这样，就无需逐个删除。

由于已创建一个新资源组来创建待办事项应用，因此可以安全删除此资源组，这会删除所有关联的资源。

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

或者，可以从[门户](../azure-resource-manager/management/manage-resource-groups-portal.md#delete-resource-groups)删除 sfmeshTutorial1RG 资源组。 

## <a name="next-steps"></a>后续步骤

将 Service Fabric 网格应用程序发布到 Azure 后，请尝试学习以下教程：

* 浏览[投票应用示例](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp)，查看有关服务到服务通信的另一个示例
* 要详细了解 Service Fabric 资源模型，请参阅 [Service Fabric 网格资源模型](service-fabric-mesh-service-fabric-resources.md)。
* 若要详细了解 Service Fabric 网格，请阅读 [Service Fabric 网格概述](service-fabric-mesh-overview.md)。
* 了解 [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)