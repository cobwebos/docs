---
title: 在 Azure Service Fabric 网格应用程序中扩展服务| Microsoft Docs
description: 了解如何使用 Azure CLI 在 Service Fabric 网格上运行的应用程序中独立扩展服务。
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: a4260fd808643971036ad87c01bd2fdec299ccc6
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089737"
---
# <a name="scale-services-within-an-application-running-on-service-fabric-mesh"></a>在 Service Fabric 网格上运行的应用程序中扩展服务

本文介绍如何在应用程序中独立扩展微服务。 在此示例中，视觉对象应用程序包含两个微服务：`web` 和 `worker`。 

`web` 服务是 ASP.NET Core 应用程序，其中一个网页在浏览器中显示三角形。 浏览器为 `worker` 服务的每个实例显示一个三角形。 

`worker` 服务根据预定义时间间隔在空间中移动三角形，然后将三角形位置发送到 `web` 服务。 它使用 DNS 来解析 `web` 服务的地址。

## <a name="set-up-service-fabric-mesh-cli"></a>设置 Service Fabric 网格 CLI 
可以使用 Azure Cloud Shell 或 Azure CLI 的本地安装完成此任务。 根据这些[说明](service-fabric-mesh-howto-setup-cli.md)安装 Azure Service Fabric 网格 CLI 扩展模块。

## <a name="sign-in-to-azure"></a>登录 Azure
登录到 Azure 并设置订阅。

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>创建资源组
创建要将应用程序部署到其中的资源组。 可以使用现有资源组并跳过此步骤。 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application-with-one-worker-service"></a>使用一个辅助角色服务部署应用程序
使用 `deployment create` 命令在资源组中创建应用程序。

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```
上面的命令使用 [mesh_rp.base.linux.json 模板](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json)部署 Linux 应用程序。 若要部署 Windows 应用程序，请使用 [mesh_rp.base.windows.json 模板](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.windows.json)。 Windows 容器映像大于 Linux 容器映像，可能需要更多时间进行部署。

几分钟后，命令应返回：

`visualObjectsApp has been deployed successfully on visualObjectsNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>打开应用程序
在应用程序成功部署后，获取服务终结点的公用 IP 地址并在浏览器中打开。 它显示一个网页，网页中一个三角形在空间中移动。

部署命令将返回服务终结点的公共 IP 地址。 （可选）还可以通过查询网络资源来查找服务终结点的公共 IP 地址。 
 
此应用程序的网络资源名称是 `visualObjectsNetwork`，使用以下命令提取与其相关的信息。 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name visualObjectsNetwork
```

## <a name="scale-worker-service"></a>扩展 `worker` 服务

缩放`worker`服务到三个实例使用以下命令。 

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```
上面的命令使用 [mesh_rp.scaleout.linux.json 模板](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json)部署 Linux 应用程序。 若要部署 Windows 应用程序，请使用 [mesh_rp.scaleout.windows.json 模板](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.windows.json)。 Windows 容器映像大于 Linux 容器映像，可能需要更多时间进行部署。

应用程序成功部署后，浏览器应显示一个网页，网页中三个三角形在空间中移动。

## <a name="delete-the-resources"></a>删除资源

为节省为预览计划分配的有限资源，请定期删除资源。 要删除与此示例相关的资源，请删除在其中部署的资源组。

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>后续步骤
- 在 [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) 上查看视觉对象示例应用程序。
- 要详细了解 Service Fabric 资源模型，请参阅 [Service Fabric 网格资源模型](service-fabric-mesh-service-fabric-resources.md)。
- 若要详细了解 Service Fabric 网格，请阅读 [Service Fabric 网格概述](service-fabric-mesh-overview.md)。