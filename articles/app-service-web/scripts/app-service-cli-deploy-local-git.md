---
title: "Azure CLI 脚本示例 - 从本地 Git 存储库创建 Web 应用并部署代码 | Microsoft 文档"
description: "Azure CLI 脚本示例 - 从本地 Git 存储库创建 Web 应用并部署代码"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 048f98aa-f708-44cb-9b9e-953f67dc6da8
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 20ea69ea285fc97bbcf755ea1c40447745ff4be0
ms.lasthandoff: 03/11/2017

---

# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>从本地 Git 存储库创建 Web 应用并部署代码

此示例脚本使用其相关资源，在应用服务中创建 Web 应用，然后在本地 Git 存储库中部署 Web 应用代码。

运行此脚本前，请确保已使用 `az login` 命令创建与 Azure 的连接，并且已将应用程序代码提交到本地 Git 存储库。

此示例在 Bash shell 中正常工作。 有关在 Windows 客户端上运行 Azure CLI 脚本的选项，请参阅[在 Windows 中运行 Azure CLI](../../virtual-machines/virtual-machines-windows-cli-options.md)。

## <a name="sample-script"></a>示例脚本

[!code-azurecli[主要](../../../cli_scripts/app-service/deploy-local-git/deploy-local-git.sh?highlight=3-5 "从本地 Git 存储库创建 Web 应用并部署代码")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 创建应用服务计划。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | 创建 Azure Web 应用。 |
| [az appservice web deployment user set](https://docs.microsoft.com/cli/azure/appservice/web/deployment/user#set) | 为应用服务设置帐户级别部署凭据。 |
| [az appservice web source-control config-local-git](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config-local-git) | 为本地 Git 存储库创建源控件配置。 |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | 在浏览器中打开 Azure Web 应用。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure 应用服务文档](../app-service-cli-samples.md)中找到其他应用服务 CLI 脚本示例。
