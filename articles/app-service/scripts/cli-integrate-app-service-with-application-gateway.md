---
title: Azure CLI 脚本示例 - 将应用服务与应用程序网关集成 | Microsoft Docs
description: Azure CLI 脚本示例 - 将应用服务与应用程序网关集成
services: appservice
documentationcenter: appservice
author: madsd
manager: ccompy
editor: ''
tags: azure-service-management
ms.assetid: 6c16d6f8-3c08-4a59-858e-684a2ceccb5f
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: ee5e50bdba0a798d335641dc8a0c7ae69832d8f6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979503"
---
# <a name="integrate-app-service-with-application-gateway-using-cli"></a>使用 CLI 将应用服务与应用程序网关集成

此示例脚本将创建一个 Azure 应用服务 Web 应用、Azure 虚拟网络和应用程序网关。 然后，它将 Web 应用的流量限制为只能源自应用程序网关子网。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，需要 Azure CLI 2.0.74 版或更高版本。 要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/integrate-with-app-gateway/integrate-with-app-gateway.sh "Integrate with Application Gateway")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、应用服务应用、Cosmos DB 和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [`az group create`](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) | 创建用于存储所有资源的资源组。 |
| [`az network vnet create`](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create) | 创建虚拟网络。 |
| [`az network public-ip create`](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) | 创建公共 IP 地址。 |
| [`az network public-ip show`](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show) | 显示公共 IP 地址的详细信息。 |
| [`az appservice plan create`](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | 创建应用服务计划。 |
| [`az webapp create`](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | 创建应用服务 Web 应用。 |
| [`az webapp show`](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-show) | 显示应用服务 Web 应用的详细信息。 |
| [`az webapp config access-restriction add`](https://docs.microsoft.com/cli/azure/webapp/config/access-restriction?view=azure-cli-latest#az-webapp-config-access-restriction-add) | 将访问限制添加到应用服务 Web 应用。 |
| [`az network application-gateway create`](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-create) | 创建应用程序网关。 |
| [`az network application-gateway http-settings update`](https://docs.microsoft.com/cli/azure/network/application-gateway/http-settings?view=azure-cli-latest#az-network-application-gateway-http-settings-update) | 更新应用程序网关 HTTP 设置。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
