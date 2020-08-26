---
title: PowerShell 示例 - 列出具有策略的所有应用程序代理应用
description: PowerShell 示例，其中列出了你的目录中具有生存期令牌策略的所有 Azure Active Directory (Azure AD) 应用程序代理应用程序。
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
ms.openlocfilehash: aa66b842007d9471828171c44c2dcb7505e8b4d7
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88506849"
---
# <a name="get-all-application-proxy-apps-with-a-token-lifetime-policy"></a>获取具有令牌生存期策略的所有应用程序代理应用

在该 PowerShell 脚本示例中，可找到你的目录中具有生存期令牌策略的所有 Azure Active Directory (Azure AD) 应用程序代理应用程序，还可找到该策略的相关详细信息。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

此示例需要[适用于 Graph 的 AzureAD V2 PowerShell 模块预览版](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview)。

## <a name="sample-script"></a>示例脚本

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-with-policy.ps1 "Get all Application Proxy apps with a token lifetime policy")]

## <a name="script-explanation"></a>脚本说明

| Command | 说明 |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | 获取服务主体。 |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | 获取 Azure AD 应用程序。 |
|[Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) | 获取 Azure AD 中的策略。 |
|[Get-AzureADServicePrincipalPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview) | 获取 Azure AD 中服务主体的策略。 |


## <a name="next-steps"></a>后续步骤

要详细了解 Azure AD PowerShell 模块，请参阅 [Azure AD PowerShell 模块概述](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)。

有关应用程序代理的其他 PowerShell 示例，请参阅 [Azure AD 应用程序代理的 Azure AD PowerShell 示例](../application-proxy-powershell-samples.md)。