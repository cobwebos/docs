---
title: "Azure CLI 脚本示例 - 将自定义域映射到 Web 应用 | Microsoft 文档"
description: "Azure CLI 脚本示例 - 将自定义域映射到 Web 应用"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 5ac4a680-cc73-4578-bcd6-8668c08802c2
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: b618e1e7095cf56a1f2f91a6bddee17d512f43c9
ms.lasthandoff: 03/11/2017

---

# <a name="map-a-custom-domain-to-a-web-app"></a>将自定义域映射到 Web 应用

此示例脚本使用其相关资源，在应用服务中创建 Web 应用，然后将 `www.<yourdomain>` 映射到它。 

运行此脚本前，请确保已使用 `az login` 命令创建与 Azure 的连接，并且你有权访问域注册机构的 DNS 配置页。

此示例在 Bash shell 中正常工作。 有关在 Windows 客户端上运行 Azure CLI 脚本的选项，请参阅[在 Windows 中运行 Azure CLI](../../virtual-machines/virtual-machines-windows-cli-options.md)。

## <a name="sample-script"></a>示例脚本

[!code-azurecli[主要](../../../cli_scripts/app-service/configure-custom-domain/configure-custom-domain.sh?highlight=3 "将自定义域映射到 Web 应用")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 创建应用服务计划。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | 创建 Azure Web 应用。 |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | 更新应用服务计划以缩放其定价层。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure 应用服务文档](../app-service-cli-samples.md)中找到其他应用服务 CLI 脚本示例。
