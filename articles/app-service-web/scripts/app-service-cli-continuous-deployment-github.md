---
title: "Azure CLI 脚本示例 - 从 GitHub 使用连续部署创建 Web 应用 | Microsoft 文档"
description: "Azure CLI 脚本示例 - 从 GitHub 使用连续部署创建 Web 应用"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/19/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: a12085a7a8146c22d6b079381542d4fe3a8e6e87
ms.contentlocale: zh-cn
ms.lasthandoff: 06/20/2017

---

# 从 GitHub 使用连续部署创建 Web 应用
<a id="create-a-web-app-with-continuous-deployment-from-github" class="xliff"></a>

此示例脚本使用其相关资源，在应用服务中创建 Web 应用，然后在 Git 存储库中设置连续部署。 有关不进行连续部署的 GitHub 部署，请参阅[从 GitHub 创建 Web 应用并部署代码](app-service-cli-deploy-github.md)。 此示例需要：

* 包含应用程序代码且你对其拥有管理权限的 GitHub 存储库。
* GitHub 帐户的[个人访问令牌(PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## 示例脚本
<a id="sample-script" class="xliff"></a>

[!code-azurecli-interactive[主要](../../../cli_scripts/app-service/deploy-github-continuous/deploy-github-continuous.sh?highlight=3-4 "从 GitHub 使用连续部署创建 Web 应用")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## 脚本说明
<a id="script-explanation" class="xliff"></a>

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 创建应用服务计划。 |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#create) | 创建 Azure Web 应用。 |
| [az webapp deployment source config](https://docs.microsoft.com/cli/azure/webapp/deployment/source#config) | 将 Azure Web 应用与 Git 或 Mercurial 存储库相关联。 |
| [az webapp browse](https://docs.microsoft.com/cli/azure/webapp#browse) | 在浏览器中打开 Azure Web 应用。 |

## 后续步骤
<a id="next-steps" class="xliff"></a>

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure 应用服务文档](../app-service-cli-samples.md)中找到其他应用服务 CLI 脚本示例。

