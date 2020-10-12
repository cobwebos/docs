---
title: 使用 Azure AD Connect "不配置" 时禁用 PTA |Microsoft Docs
description: 本文介绍如何禁用 Azure AD Connect "不配置" 功能的 PTA。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5db99f5e8ed2ea5844acba5500bc94d8fb0db2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85358338"
---
# <a name="disable-pta-when-using-azure-ad-connect-do-not-configure"></a>使用 Azure AD Connect "不配置" 时禁用 PTA

如果使用传递身份验证和 Azure AD Connect，并将其设置为 "不配置"，则可以禁用它。 可以使用以下 cmdlet 来禁用 PTA。 

## <a name="prerequisites"></a>必备条件
需要以下先决条件：
- 安装了 PTA 代理的任何 windows 计算机。 
- 代理必须处于版本1.5.1742.0 或更高版本。 
- 为了运行 PowerShell cmdlet 来禁用 PTA 的 Azure 全局管理员帐户。

>[!NOTE]
> 如果代理较旧，则它可能没有完成此操作所需的 cmdlet。 你可以从 Azure 门户获取新代理，并将其安装在任意 windows 计算机上，并提供管理员凭据。 安装代理 (不会影响云中的 PTA 状态) 

> [!IMPORTANT]
> 如果你使用的是 Azure 政府云，则必须在 ENVIRONMENTNAME 参数中传递以下值。 
>
>| 环境名称 | 云 |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>禁用 PTA
在 PowerShell 会话中，使用以下内容禁用 PTA：
1. PS C:\Program Files\Microsoft Azure AD Connect Authentication 代理> `Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` 或 `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` 或 `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>如果你没有代理的访问权限

如果没有代理计算机，可以使用以下命令安装代理。

1. 从 portal.azure.com 下载最新的身份验证代理。
2. 安装功能： `.\AADConnectAuthAgentSetup.exe` 或 `.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>后续步骤

- [使用 Azure Active Directory 直通身份验证的用户登录](how-to-connect-pta.md)
