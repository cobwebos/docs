---
title: "Azure CLI 脚本示例 - 将自定义 SSL 证书绑定到 Function App | Microsoft Docs"
description: "Azure CLI 脚本示例 - 将自定义 SSL 证书绑定到 Azure 中的 Function App"
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 04/10/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 76602f63f8b7fd7adc38353756387484f0af6493
ms.contentlocale: zh-cn
ms.lasthandoff: 09/09/2017

---
# <a name="bind-a-custom-ssl-certificate-to-a-function-app"></a>将自定义 SSL 证书绑定到 Function App

此示例脚本在应用服务中创建一个 Function App 及其相关资源，然后将自定义域名的 SSL 证书绑定到该应用。 在此示例中，需要以下项：

* 对域注册机构的 DNS 配置页的访问权限。
* 要上传和绑定的 SSL 证书的有效 .PFX 文件及其密码。

若要绑定 SSL 证书，必须在应用服务计划（而不是消耗计划）中创建 Function App。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | 创建绑定 SSL 证书所需的应用服务计划。 |
| [az functionapp create]() | 创建 Function App。 |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#az_appservice_web_config_hostname_add) | 将自定义域映射到 Function App。 |
| [az appservice web config ssl upload](https://docs.microsoft.com/cli/azure/appservice/web/config/ssl#az_appservice_web_config_ssl_upload) | 将 SSL 证书上传到 Function App。 |
| [az appservice web config ssl bind](https://docs.microsoft.com/en-us/cli/azure/appservice/web/config/ssl#az_appservice_web_config_ssl_bind) | 将上传的 SSL 证书绑定到 Function App。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure 应用服务文档]()中找到其他应用服务 CLI 脚本示例。

