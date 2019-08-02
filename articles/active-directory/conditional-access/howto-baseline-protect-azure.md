---
title: 基线策略需要 MFA 进行服务管理 (预览版)-Azure Active Directory
description: 要求对 Azure 进行 MFA 的条件性访问策略资源管理器
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: aab2aa4415345747a0e87b90ef0a7ee770ef3465
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608123"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>基准策略:需要 MFA 进行服务管理 (预览版)

你可能会在你的组织中使用多种 Azure 服务。 这些服务可以通过 Azure 资源管理器 API 进行管理:

* Azure 门户
* Azure PowerShell
* Azure CLI

使用 Azure 资源管理器来管理服务是一种高度特权的操作。 Azure 资源管理器可以改变租户范围的配置, 例如服务设置和订阅计费。 单因素身份验证容易受到各种攻击, 如仿冒和密码喷涂。 因此, 在允许访问之前要求进行多重身份验证之前, 请务必验证要访问 Azure 资源管理器和更新配置的用户的身份。

**要求 mfa for service management**是一种[基准策略](concept-baseline-protection.md), 需要对任何访问 Azure 门户、Azure PowerShell 或 AZURE CLI 的用户进行 mfa。 此策略适用于访问 Azure 资源管理器的所有用户, 无论他们是否是管理员。

在租户中启用此策略后, 登录到 Azure 管理资源的所有用户将面临多重身份验证的挑战。 如果用户未注册 MFA, 用户将需要使用 Microsoft Authenticator 的应用进行注册, 然后才能继续操作。

若要使用[Azure Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)执行交互式登录, 请使用[AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet。

```PowerShell
Connect-AzAccount
```

在运行时，此 cmdlet 将显示一个令牌字符串。 若要登录, 请复制此字符串, 并将 [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)其粘贴到  浏览器中。 PowerShell 会话在进行身份验证后就会连接到 Azure。

若要使用[Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)执行交互式登录, 请运行 [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) 命令。

```azurecli
az login
```

如果 CLI 可以打开默认的浏览器，则它会打开该浏览器并加载登录页。 否则, 需要打开浏览器页面并按照命令行上的说明在浏览器中导航到 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)后输入授权代码。 之后, 请在浏览器中用帐户凭据登录。

## <a name="deployment-considerations"></a>部署注意事项

由于**要求服务管理**策略的 MFA 适用于所有 Azure 资源管理器用户, 因此需要考虑几个注意事项来确保顺利部署。 这些注意事项包括: 在 Azure AD 中标识用户和服务原则, 这些用户和服务原则不能或不应执行 MFA, 以及不支持新式身份验证的组织使用的应用程序和客户端。

## <a name="enable-the-baseline-policy"></a>启用基线策略

策略**基线策略:需要对服务管理 (预览版)** 进行预配置, 并且当你导航到 Azure 门户中的 "条件性访问" 边栏选项卡时, 它将显示在顶部。

若要启用此策略并保护管理员:

1. 以全局管理员、安全管理员或条件访问管理员身份登录到 **Azure 门户** 。
1. 浏览到**Azure Active Directory** > **条件性访问**。
1. 在策略列表中, 选择 **"基准策略":需要对服务管理 (预览版)** 进行 MFA。
1. 将 "**启用策略**" 设置为 "**立即使用策略**"。
1. 单击 " **保存**"。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

* [条件性访问基线保护策略](concept-baseline-protection.md)
* [保护标识基础结构的五个步骤](../../security/fundamentals/steps-secure-identity.md)
* [什么是 Azure Active Directory 中的条件访问？](overview.md)