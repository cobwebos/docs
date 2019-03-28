---
title: 教程 - 缩放在 Azure Service Fabric 网格中运行的应用 | Microsoft Docs
description: 本教程介绍如何缩放在 Service Fabric 网格中运行的应用程序中的服务。
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
ms.openlocfilehash: 31b1e7a777c65a270b7b08673fcd73a5ab63f11f
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337759"
---
# <a name="tutorial-scale-an-application-running-in-service-fabric-mesh"></a>教程：缩放在 Service Fabric 网格中运行的应用程序

本教程是一个系列中的第二部分。 了解如何手动缩放[以前部署到 Service Fabric 网格](service-fabric-mesh-tutorial-template-deploy-app.md)的应用程序的服务实例数。 完成后，你的前端服务运行三个实例，数据服务运行两个实例。

本系列教程的第二部分将介绍如何：

> [!div class="checklist"]
> * 配置所需的服务实例数
> * 执行升级

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * [使用模板将应用程序部署到 Service Fabric 网格](service-fabric-mesh-tutorial-template-deploy-app.md)
> * 缩放在 Service Fabric 网格中运行的应用程序
> * [升级 Service Fabric 网格中运行的应用程序](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [删除应用程序](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前：

* 如果没有 Azure 订阅，可以在开始前[创建免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* [在本地安装 Azure CLI 和 Service Fabric 网格 CLI](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli)。

## <a name="manually-scale-your-services-in-or-out"></a>手动对服务进行横向缩放

将应用程序部署到 Service Fabric 网格的一个主要优势是能够轻松地对服务进行横向缩放。此功能应该用于处理服务上的各种数量的负载，或者用于改进可用性。

本教程使用“待办事项列表”示例作为示例，该示例[以前已部署](service-fabric-mesh-tutorial-template-deploy-app.md)，现在应该处于运行状态。 此应用程序有两个服务：WebFrontEnd 和 ToDoService。 每项服务在一开始部署时，副本计数为 1。  若要查看 WebFrontEnd 服务的正在运行的副本数，请运行以下命令：

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

若要查看 ToDoService 服务的正在运行的副本数，请运行以下命令：

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

在应用程序资源的部署模板中，每项服务都有一个 *replicaCount* 属性，该属性可以用来设置所需的服务部署次数。 应用程序由多项服务组成，每项服务都有唯一的 *replicaCount* 数，这些服务可以一起进行部署和管理。 若要缩放服务副本数，请在部署模板或参数文件中修改每项需要缩放的服务的 *replicaCount* 值。  然后，升级应用程序。

### <a name="modify-the-deployment-template-parameters"></a>修改部署模板参数

当模板中有希望在部署应用程序后进行更改的值，或者希望可以选择在每个部署的基础上进行更改时（如果计划重用此模板进行其他部署），最佳做法是参数化这些值。

以前，应用程序使用 [mesh_rp.windows.json 部署模板](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json)和 [mesh_rp.windows.parameter.json 参数](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json)文件进行部署。

在本地打开 [mesh_rp.windows.parameter.json 参数](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json)文件，将 *frontEndReplicaCount* 值设置为 3，将 *serviceReplicaCount* 值设置 为 2：

```json
      "frontEndReplicaCount":{
        "value": "3"
      },
      "serviceReplicaCount":{
        "value": "2"
      }
```

保存对参数文件所做的更改。  *frontEndReplicaCount* 和 *serviceReplicaCount* 参数在 [mesh_rp.windows.json 部署模板](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json)的 *parameters* 节中声明：

```json
"frontEndReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    },
    "serviceReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    }
```

WebFrontEnd 服务的 *replicaCount* 属性反映 *frontEndReplicaCount* 参数，ToDoService 服务的 *replicaCount* 属性反映 *serviceReplicaCount* 参数：

```json
    "services": [
    {
    "name": "WebFrontEnd",
    "properties": {
        "description": "WebFrontEnd description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('frontEndReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    },
    {
    "name": "ToDoService",
    "properties": {
        "description": "ToDoService description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('serviceReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    }
],
```

修改模板以后，请升级应用程序。

### <a name="upgrade-your-application"></a>升级应用程序

应用程序正在运行时，可以将其升级，方法是重新部署模板和更新的参数文件：

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

这样就会开始应用程序的滚动升级，你会看到服务实例数在数分钟内增加。  若要查看 WebFrontEnd 服务的正在运行的副本数，请运行以下命令：

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

若要查看 ToDoService 服务的正在运行的副本数，请运行以下命令：

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

## <a name="next-steps"></a>后续步骤

本教程的此部分介绍了如何：

> [!div class="checklist"]
> * 配置所需的服务实例数
> * 执行升级

转到下一教程：
> [!div class="nextstepaction"]
> [升级 Service Fabric 网格中运行的应用程序](service-fabric-mesh-tutorial-template-upgrade-app.md)
