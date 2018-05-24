---
title: Azure CLI 脚本示例 - 为实现应用程序的高可用性路由流量 | Microsoft 文档
description: Azure CLI 脚本示例 - 为实现应用程序的高可用性路由流量
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: jeconnoc
editor: tysonn
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: kumud
ms.openlocfilehash: 82264e51fcd64615a41f5fa652c4ad58ad9dabfb
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2018
ms.locfileid: "32313170"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-cli"></a>使用 Azure CLI 为实现应用程序的高可用性路由流量

此脚本将创建一个资源组、两个应用服务计划、两个 Web 应用、一个流量管理器配置文件和两个流量管理器终结点。 流量管理器将流量引导到一个区域（作为主要区域）中的应用程序；主要区域中的应用程序不可用时，引导到次要区域。 执行脚本前，必须将 MyWebApp、MyWebAppL1 和 MyWebAppL2 值更改为 Azure 内的唯一值。 运行脚本后，可以使用 URL mywebapp.trafficmanager.net 访问主要区域中的应用。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、应用服务应用以及所有相关资源。

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、Web 应用、流量管理器配置文件和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | 创建应用服务计划。 这与 Azure Web 应用的服务器场类似。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#az_appservice_web_create) | 创建应用服务计划中的 Azure Web 应用。 |
| [az network traffic-manager profile create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_create) | 创建 Azure 流量管理器配置文件。 |
| [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create) | 将终结点添加到 Azure 流量管理器配置文件。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure)。

可在 [Azure 网络文档](../cli-samples.md)中找到其他应用服务 CLI 脚本示例。
