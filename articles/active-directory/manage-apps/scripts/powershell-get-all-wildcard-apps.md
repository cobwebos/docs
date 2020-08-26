---
title: PowerShell 示例 - 列出使用通配符的应用程序代理应用
description: PowerShell 示例，其中列出了正在使用通配符的所有 Azure Active Directory (Azure AD) 应用程序代理应用程序。
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 1b41f6c960c19a6569e95814fd2bf5b1234a338c
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88511161"
---
# <a name="get-all-application-proxy-apps-using-wildcard-publishing"></a>获取所有使用通配符发布的应用程序代理应用

在该 PowerShell 脚本示例中，可找到正在使用通配符发布的所有 Azure Active Directory (Azure AD) 应用程序代理应用程序。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

此示例需要[适用于 Graph 的 AzureAD V2 PowerShell 模块](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) 或[适用于 Graph 的 AzureAD V2 PowerShell 模块预览版](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview)。

## <a name="sample-script"></a>示例脚本

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-wildcard-apps.ps1 "Get all Application Proxy apps using wildcards")]

## <a name="script-explanation"></a>脚本说明

| Command | 说明 |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | 获取服务主体。 |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | 获取 Azure AD 应用程序。 |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | 检索为 Azure AD 中的应用程序代理配置的应用程序。 |

## <a name="next-steps"></a>后续步骤

要详细了解 Azure AD PowerShell 模块，请参阅 [Azure AD PowerShell 模块概述](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)。

有关应用程序代理的其他 PowerShell 示例，请参阅 [Azure AD 应用程序代理的 Azure AD PowerShell 示例](../application-proxy-powershell-samples.md)。