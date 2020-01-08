---
title: PowerShell 示例-将用户分配到应用程序代理应用
description: 将用户分配到 Azure Active Directory （Azure AD）应用程序代理应用程序的 PowerShell 示例。
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
ms.openlocfilehash: 42667ebdfc37b679d56421e3a82d4a6aaaed8fdf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483331"
---
# <a name="assign-a-user-to-a-specific-azure-ad-application-proxy-application"></a>将用户分配到特定 Azure AD 应用程序代理应用程序

使用此 PowerShell 脚本示例，可以将用户分配到特定的 Azure AD 应用程序代理应用程序。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

此示例需要用于图形模块（AzureAD）的[AzureAD V2 powershell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)或[graph 模块预览版本](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)（AzureADPreview）的 AzureAD v2 powershell。

## <a name="sample-script"></a>示例脚本

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-user-to-app.ps1 "Assign a user to an application")]

## <a name="script-explanation"></a>脚本说明

| 命令 | 说明 |
|---|---|
| [新-New-azureaduserapproleassignment](https://docs.microsoft.com/powershell/module/AzureAD/new-azureaduserapproleassignment?view=azureadps-2.0) | 将用户分配到应用程序角色。 |

## <a name="next-steps"></a>后续步骤

有关 Azure AD PowerShell 模块的详细信息，请参阅[Azure AD powershell 模块概述](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)。

有关应用程序代理的其他 PowerShell 示例，请参阅[Azure AD 应用程序代理的 Azure AD powershell 示例](../application-proxy-powershell-samples.md)。