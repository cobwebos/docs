---
title: PowerShell 示例-列出应用程序代理应用 & 组的用户
description: PowerShell 示例，列出分配给特定 Azure Active Directory （Azure AD）应用程序代理应用程序的所有用户和组。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: aab5ac5d4712dd9e72fc0b9665d740859b5a6775
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483383"
---
# <a name="display-users-and-groups-assigned-to-an-application-proxy-application"></a>显示分配给应用程序代理应用程序的用户和组

此 PowerShell 脚本示例列出了分配给特定 Azure Active Directory （Azure AD）应用程序代理应用程序的用户和组。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

此示例需要用于图形模块（AzureAD）的[AzureAD V2 powershell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)或[graph 模块预览版本](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)（AzureADPreview）的 AzureAD v2 powershell。

## <a name="sample-script"></a>示例脚本

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/display-users-group-of-an-app.ps1 "Display users and groups assigned to an Application Proxy application")]

## <a name="script-explanation"></a>脚本说明

| 命令 | 说明 |
|---|---|
| [Get-azureaduser](https://docs.microsoft.com/powershell/module/AzureAD/get-azureaduser?view=azureadps-2.0)| 获取用户。 |
| [Get-azureadgroup](https://docs.microsoft.com/powershell/module/AzureAD/get-azureadgroup?view=azureadps-2.0)| 获取组。 |
| [Get-azureadserviceprincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | 获取服务主体。 |
| [New-azureaduserapproleassignment](https://docs.microsoft.com/powershell/module/AzureAD/get-azureaduserapproleassignment?view=azureadps-2.0) | 获取用户应用程序角色分配。 |
| [Remove-azureadgroupapproleassignment](https://docs.microsoft.com/powershell/module/AzureAD/get-azureadgroupapproleassignment?view=azureadps-2.0) | 获取组应用程序角色分配。 |

## <a name="next-steps"></a>后续步骤

有关 Azure AD PowerShell 模块的详细信息，请参阅[Azure AD powershell 模块概述](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)。

有关应用程序代理的其他 PowerShell 示例，请参阅[Azure AD 应用程序代理的 Azure AD powershell 示例](../application-proxy-powershell-samples.md)。