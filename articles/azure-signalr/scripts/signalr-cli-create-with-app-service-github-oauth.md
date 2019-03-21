---
title: Azure CLI 脚本示例 - 创建使用 SignalR 服务和 GitHub 身份验证的 Web 应用
description: Azure CLI 脚本示例 - 创建使用 SignalR 服务和 GitHub 身份验证的 Web 应用
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/22/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: 84020448019867744d08806acbbd47adbc1a83e3
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57546461"
---
# <a name="create-a-web-app-that-uses-signalr-service-and-github-authentication"></a>创建使用 SignalR 服务和 GitHub 身份验证的 Web 应用

此示例脚本会创建新的 Azure SignalR 服务资源，用于将实时内容更新推送到客户端。 此脚本还会添加新的 Web 应用和应用服务计划，以托管使用 SignalR 服务的 ASP.NET Core Web 应用。 使用应用设置将 Web 应用配置为连接到新的 SignalR 服务资源，并使用 [GitHub 身份验证](https://developer.github.com/v3/guides/basics-of-authentication/)进行身份验证。 Web 应用还配置为使用本地 Git 存储库部署资源。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>示例脚本

此脚本使用适用于 Azure CLI 的 signalr 扩展。 使用此示例脚本前，执行以下命令，安装适用于 Azure CLI 的 signalr 扩展：

```azurecli-interactive
az extension add -n signalr
```

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-with-app-service-github-oauth/create-signalr-with-app-service-github-oauth.sh "Create a new SignalR Service and Web App configured to use SignalR, GitHub OAuth, and local Git repository deployment source.")]

记下为新资源组生成的实际名称。 它将在输出中显示。 如果要删除所有组资源，将使用该资源组名称。

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

表中的每条命令均链接到特定于命令的文档。 此脚本使用以下命令：

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az signalr create](/cli/azure/ext/signalr/signalr#ext-signalr-az-signalr-create) | 创建 Azure SignalR 服务资源。 |
| [az signalr key list](/cli/azure/ext/signalr/signalr/key#ext-signalr-az-signalr-key-list) | 列出密钥，使用 SignalR 推送实时内容更新时，应用程序将使用这些密钥。 |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | 创建用于托管 Web 应用的 Azure 应用服务计划。 |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | 使用应用服务托管计划创建 Azure Web 应用。 |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | 为 Web 应用添加新的应用设置。 这些应用设置用于存储 SignalR 连接字符串和 GitHub OAuth 应用密钥。 |
| [az webapp deployment user set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) | 更新部署凭据。 |
| [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-local-git) | 获取 git 存储库终结点的 URL 用于为 web 应用部署克隆和推送。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可在 [Azure SignalR 服务文档](../signalr-reference-cli.md)中找到其他 Azure SignalR 服务 CLI 脚本示例。
