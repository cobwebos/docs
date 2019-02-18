---
title: Azure PowerShell 脚本示例 - 将自定义 SSL 证书绑定到 Web 应用 | Microsoft 文档
description: Azure PowerShell 脚本示例 - 将自定义 SSL 证书绑定到 Web 应用
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 5de9890369b6f99cec3b271117fd4d0cfe70001d
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2019
ms.locfileid: "56104094"
---
# <a name="bind-a-custom-ssl-certificate-to-a-web-app-using-powershell"></a>使用 PowerShell 将自定义 SSL 证书绑定到 Web 应用

此示例脚本在应用服务中创建一个 Web 应用及其相关资源，然后将自定义域名的 SSL 证书绑定到该应用。 

必要时，请使用 [Azure PowerShell 指南](/powershell/azure/overview)中的说明安装 Azure PowerShell，并运行 `Connect-AzAccount` 创建与 Azure 的连接。 同时，请确保：

- 已使用 `az login` 命令创建与 Azure 的连接。
- 可以访问域注册机构的 DNS 配置页。
- 有要上传和绑定的 SSL 证书的有效 .PFX 文件及其密码。

## <a name="sample-script"></a>示例脚本

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、Web 应用以及所有相关资源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | 创建应用服务计划。 |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | 创建 Web 应用。 |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | 修改应用服务计划以更改其定价层。 |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | 修改 Web 应用的配置。 |
| [New-AzWebAppSSLBinding](/powershell/module/az.websites/new-azwebappsslbinding) | 为 Web 应用创建 SSL 证书绑定。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure PowerShell 示例](../samples-powershell.md)中找到 Azure 应用服务 Web 应用的其他 Azure Powershell 示例。
