---
title: 使用模板将应用部署到 Azure Service Fabric 网格 | Microsoft Docs
description: 了解如何使用 Azure CLI 通过模板将 .NET Core 应用程序部署到 Service Fabric 网格。
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
ms.date: 07/12/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 356e8019f9a4a64cb1c1c113d0f44990aa4e0f95
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038028"
---
# <a name="deploy-a-service-fabric-mesh-application-to-service-fabric-mesh-using-a-template"></a>使用模板将 Service Fabric 网格应用程序部署到 Service Fabric 网格
本文介绍如何使用模板将 .NET Core 应用程序部署到 Service Fabric 网格。 完成后，将生成一个投票应用程序，其中包含 ASP.NET Core Web 前端，用于将投票结果保存到群集的后端服务中。 前端使用 DNS 解析后端服务的地址。

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

## <a name="deploy-the-application"></a>部署应用程序
使用 `deployment create` 命令在资源组中创建应用程序。

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

上述命令使用 [mesh_rp.windows.json 模板](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json)部署 Windows 应用程序。 若要部署 Linux 应用程序，请使用 [mesh_rp.windows.json 模板](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.linux.json)。 Windows 容器映像大于 Linux 容器映像，可能需要更多时间进行部署。

几分钟后，命令应返回：

`VotingApp has been deployed successfully on VotingAppNetwork with public ip address <IP address>` 

## <a name="open-the-application"></a>打开应用程序
在应用程序成功部署后，获取服务终结点的公用 IP 地址并在浏览器中打开。 随即显示以下网页。 

![Voting 应用程序](./media/service-fabric-mesh-howto-deploy-app-template/VotingApplication.png)

现在可以向应用程序添加投票选项，然后进行投票或删除投票选项。

部署命令将返回服务终结点的公共 IP 地址。 （可选）还可以通过查询网络资源来查找服务终结点的公共 IP 地址。 

此应用程序的网络资源名称是 `VotingAppNetwork`，使用以下命令提取与其相关的信息。 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name VotingAppNetwork
```

## <a name="check-the-application-details"></a>查看应用程序详细信息
可以使用 `app show` 命令查看应用程序的状态。 已部署的应用程序的应用程序名称是“VotingApp”，请提取其详细信息。 

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name VotingApp
```

## <a name="list-the-deployed-applications"></a>列出已部署的应用程序
可以使用“app list”命令获取订阅中部署的应用程序的列表。 

```azurecli-interactive
az mesh app list -o table
```

## <a name="clean-up-resources"></a>清理资源
当不再需要应用程序及其相关资源时，请删除包含这些内容的资源组。 

```azurecli-interactive
az group delete --resource-group myResourceGroup  
``` 

## <a name="next-steps"></a>后续步骤
- 在 [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) 上查看投票示例应用程序。
- 若要详细了解 Service Fabric 网格，请阅读 [Service Fabric 网格概述](service-fabric-mesh-overview.md)。


