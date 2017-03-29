---
title: "Azure CLI 脚本示例 - 从 Visual Studio Team Services 使用连续部署创建 Web 应用 | Microsoft 文档"
description: "Azure CLI 脚本示例 - 从 Visual Studio Team Services 使用连续部署创建 Web 应用"
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 389d3bd3-cd8e-4715-a3a1-031ec061d385
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: a2009cb07ae2ae7cf716d77a7d9eac7482138ab1
ms.lasthandoff: 03/21/2017

---

# <a name="create-a-web-app-with-continuous-deployment-from-visual-studio-team-services"></a>从 Visual Studio Team Services 使用连续部署创建 Web 应用

此示例脚本使用其相关资源在应用服务中创建 Web 应用，然后在 Visual Studio Team Services 存储库中设置连续部署。 

必要时，请使用 [Azure CLI 安装指南](https://docs.microsoft.com/cli/azure/install-azure-cli)中的指令安装 Azure CLI。 同时，请确保：

- 已使用 `az login` 命令创建与 Azure 的连接。
- 应用程序代码位于你拥有的 Visual Studio Team Services 存储库中。
- 你已在 [Visual Studio Team Services 帐户中创建访问令牌](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate)。

此示例在 Bash shell 中正常工作。 有关在 Windows 客户端上运行 Azure CLI 脚本的选项，请参阅[在 Windows 中运行 Azure CLI](../../virtual-machines/virtual-machines-windows-cli-options.md)。

## <a name="create-app-sample"></a>创建应用示例

[!code-azurecli[主要](../../../cli_scripts/app-service/deploy-vsts-continuous/deploy-vsts-continuous.sh?highlight=3-4 "从 Visual Studio Team Services 使用连续部署创建 Web 应用")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 创建应用服务计划。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | 创建 Azure Web 应用。 |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | 将 Azure Web 应用与 Git 或 Mercurial 存储库相关联。 |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | 在浏览器中打开 Azure Web 应用。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure 应用服务文档](../app-service-cli-samples.md)中找到其他应用服务 CLI 脚本示例。
