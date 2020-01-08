---
title: PowerShell 示例-将应用程序代理应用移到另一个组
description: Azure Active Directory （Azure AD）应用程序代理 PowerShell 示例，用于将当前分配给连接器组的所有应用程序移到不同的连接器组。
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
ms.openlocfilehash: f6dccdaa96dadb061b168bbdf6148ed4d759776c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75481979"
---
# <a name="move-all-apps-assigned-to-a-connector-group-to-another-connector-group"></a>将分配给连接器组的所有应用移动到另一个连接器组

此 PowerShell 脚本示例将当前分配给连接器组的所有 Azure Active Directory （Azure AD）应用程序代理应用程序移到不同的连接器组。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

此示例需要用于图形模块（AzureAD）的[AzureAD V2 powershell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)或[graph 模块预览版本](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)（AzureADPreview）的 AzureAD v2 powershell。

## <a name="sample-script"></a>示例脚本

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/move-all-apps-to-a-connector-group.ps1 "Move all apps assigned to a connector group to another connector group")]

## <a name="script-explanation"></a>脚本说明

| 命令 | 说明 |
|---|---|
|[Get-azureadserviceprincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | 获取服务主体。 |
|[Get-azureadapplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | 获取 Azure AD 应用程序。 |
| [Get-azureadapplicationproxyconnectorgroup](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | 检索所有连接器组的列表，如果指定，则检索指定连接器组的详细信息。 |
| [Get-azureadapplicationproxyconnectorgroup](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplicationproxyapplicationconnectorgroup?view=azureadps-2.0) | 将给定的连接器组分配给指定的应用程序。|

## <a name="next-steps"></a>后续步骤

有关 Azure AD PowerShell 模块的详细信息，请参阅[Azure AD powershell 模块概述](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)。

有关应用程序代理的其他 PowerShell 示例，请参阅[Azure AD 应用程序代理的 Azure AD powershell 示例](../application-proxy-powershell-samples.md)。