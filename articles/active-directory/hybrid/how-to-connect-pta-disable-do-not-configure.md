---
title: 使用 Azure AD 连接时禁用 PTA"不配置" |微软文档
description: 本文介绍如何使用 Azure AD 连接"不配置"功能禁用 PTA。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa1046dc64fed3edb6c9d04f76a96f488769ff42
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726795"
---
# <a name="disable-pta-when-using-azure-ad-connect-do-not-configure"></a>使用 Azure AD 连接时禁用 PTA"不配置"

如果使用 Azure AD Connect 的直通身份验证并将其设置为"不配置"，则可以禁用它。 禁用 PTA 可以使用以下 cmdlet 完成。 

## <a name="prerequisites"></a>先决条件
需要以下先决条件：
- 安装了 PTA 代理的任何窗口计算机。 
- 代理必须位于版本 1.5.1742.0 或更高版本。 
- Azure 全局管理员帐户，以便运行 PowerShell cmdlet 以禁用 PTA。

>[!NOTE]
> 如果您的代理较旧，则可能没有完成此操作所需的 cmdlet。 可以从 Azure 门户获取新代理，将其安装在任何窗口计算机上并提供管理员凭据。 （安装代理不会影响云中的 PTA 状态）

> [!IMPORTANT]
> 如果使用 Azure 政府云，则必须使用以下值传递环境名称参数。 
>
>| 环境名称 | 云 |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>禁用 PTA
在 PowerShell 会话中，使用以下内容禁用 PTA：
1. PS C：\程序文件\微软 Azure AD 连接身份验证代理>`Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` 或 `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` 或 `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>如果您无法访问代理

如果没有代理计算机，则可以使用以下命令安装代理。

1. 从portal.azure.com下载最新的 Auth 代理。
2. 安装功能：`.\AADConnectAuthAgentSetup.exe`或`.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>后续步骤

- [使用 Azure Active Directory 传递身份验证的用户登录](how-to-connect-pta.md)