---
title: PowerShell 示例 - 在应用程序代理应用程序中替换证书
description: PowerShell 示例，批量替换跨 Azure 活动目录 （Azure AD） 应用程序代理应用程序的证书。
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
ms.openlocfilehash: 91aafe00856f37e9d7e6b3babafecd2cbdf5f856
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75481992"
---
# <a name="get-all-application-proxy-applications-published-with-the-identical-certificate-and-replace-it"></a>使用相同的证书发布所有应用程序代理应用程序并替换它

此 PowerShell 脚本示例允许您批量替换使用相同证书发布的所有 Azure 活动目录 （Azure AD） 应用程序代理应用程序。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

此示例需要用于图形模块 （AzureAD）[的 AzureAD V2 电源外壳](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)，或[用于图形模块预览版本的 AzureAD V2 电源外壳](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)（AzureAD 预览版）。

## <a name="sample-script"></a>示例脚本

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-replace-cert.ps1 "Get all Application Proxy applications published with the identical certificate and replace it")]

## <a name="script-explanation"></a>脚本说明

| 命令 | 说明 |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | 获取服务主体。 |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | 获取 Azure AD 应用程序。 |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | 检索为 Azure AD 中的应用程序代理配置的应用程序。 |
|[Set-AzureADApplicationProxyApplicationCustomDomainCertificate](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplicationproxyapplicationcustomdomaincertificate?view=azureadps-2.0) | 将证书分配给为 Azure AD 中的应用程序代理配置的应用程序。 此命令上载证书并允许应用程序使用自定义域。 |

## <a name="next-steps"></a>后续步骤

有关 Azure AD 电源外壳模块的详细信息，请参阅[Azure AD PowerShell 模块概述](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)。

有关应用程序代理的其他 PowerShell 示例，请参阅[Azure AD 应用程序代理的 Azure AD PowerShell 示例](../application-proxy-powershell-samples.md)。