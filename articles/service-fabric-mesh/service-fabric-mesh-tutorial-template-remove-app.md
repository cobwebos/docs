---
title: 教程 - 删除 Azure Service Fabric 网格中运行的应用 | Microsoft Docs
description: 在本教程中，了解如何删除 Service Fabric 网格中运行的应用程序并删除资源。
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: adc5b96f29f610c63bcfa24a3b5f761c04d41d5b
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339663"
---
# <a name="tutorial-remove-an-application-and-resources"></a>教程：删除应用程序和资源

本教程是一个系列中的第四部分， 内容为学习如何删除[以前部署到 Service Fabric 网格](service-fabric-mesh-tutorial-template-deploy-app.md)的正在运行的应用程序。 

该系列的第 4 部分中介绍了如何：

> [!div class="checklist"]
> * 删除 Service Fabric 网格中运行的应用
> * 删除应用程序资源

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * [使用模板将应用程序部署到 Service Fabric 网格](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [扩展 Service Fabric 网格中运行的应用程序](service-fabric-mesh-tutorial-template-scale-services.md)
> * [升级 Service Fabric 网格中运行的应用程序](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * 删除应用程序

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前：

* 如果没有 Azure 订阅，可以在开始前[创建免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 打开 [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md) 或者[在本地安装 Azure CLI 和 Service Fabric 网格 CLI](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli)。

## <a name="delete-the-resource-group-and-all-the-resources"></a>删除资源组以及所有资源

如果不再需要，请删除创建的所有资源。 你之前[创建了一个新资源组](service-fabric-mesh-tutorial-template-deploy-app.md#create-a-container-registry)用于托管 Azure 容器注册表实例和 Service Fabric 网格应用程序资源。  可删除此资源组，这将删除所有相关资源。

```azurecli
az group delete --resource-group myResourceGroup
```

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="individually-delete-the-resources"></a>单独删除资源
还可单独删除 ACR 实例、Service Fabric 网格应用程序和网络资源。

删除 ACR 实例：

```azurecli
az acr delete --resource-group myResourceGroup --name myContainerRegistry
```

删除 Service Fabric 网格应用程序：

```azurecli
az mesh app delete --resource-group myResourceGroup --name todolistapp
```

删除网络：
```azurecli
az mesh network delete --resource-group myResourceGroup --name todolistappNetwork
```

## <a name="next-steps"></a>后续步骤

本教程的此部分介绍了如何：

> [!div class="checklist"]
> * 删除 Service Fabric 网格中运行的应用
> * 删除应用程序资源