---
title: "Azure CLI 脚本示例 - 将自定义 SSL 证书绑定到 Web 应用 | Microsoft 文档"
description: "Azure CLI 脚本示例 - 将自定义 SSL 证书绑定到 Web 应用"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: a28bb7b8d30ebe6589dfb792e9294a180544569f
ms.lasthandoff: 03/11/2017

---

# <a name="bind-a-custom-ssl-certificate-to-a-web-app"></a>将自定义 SSL 证书绑定到 Web 应用

此示例脚本使用其相关资源在应用服务中创建 Web 应用，然后将自定义域名的 SSL 证书绑定到该应用。 

在运行此脚本之前，请确保具备以下条件：

- 已使用 `az login` 命令创建与 Azure 的连接。
- 你可以访问域注册机构的 DNS 配置页。
- 你有要上载和绑定的 SSL 证书的有效 .PFX 文件及其密码。

此示例在 Bash shell 中正常工作。 有关在 Windows 客户端上运行 Azure CLI 脚本的选项，请参阅[在 Windows 中运行 Azure CLI](../../virtual-machines/virtual-machines-windows-cli-options.md)。

## <a name="sample-script"></a>示例脚本

[!code-azurecli[主要](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "将自定义 SSL 证书绑定到 Web 应用")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 创建应用服务计划。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | 创建 Azure Web 应用。 |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | 更新应用服务计划以缩放其定价层。 |
| [az appservice web config ssl upload](https://docs.microsoft.com/cli/azure/appservice/web/config/ssl#upload) | 将 SSL 证书上载到 Web 应用。 |
| [az appservice web config ssl bind](https://docs.microsoft.com/en-us/cli/azure/appservice/web/config/ssl#bind) | 将上载的 SSL 证书绑定到 Web 应用。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure 应用服务文档](../app-service-cli-samples.md)中找到其他应用服务 CLI 脚本示例。
