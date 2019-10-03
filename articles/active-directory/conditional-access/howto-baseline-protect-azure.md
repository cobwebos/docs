---
title: 基线策略：要求对服务管理进行 MFA（预览版）- Azure Active Directory
description: 要求对 Azure 资源管理器进行 MFA 的条件访问策略
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
ms.openlocfilehash: 06b3a3f12b2955ae97c43b0caf6a4ac13c9b770a
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086728"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>基线策略:要求对服务管理进行 MFA（预览版）

你可能在组织中使用多种 Azure 服务。 以下服务可以通过 Azure 资源管理器 API 进行管理：

* Azure 门户
* Azure PowerShell
* Azure CLI

使用 Azure 资源管理器来管理服务是需要很高特权的操作。 Azure 资源管理器可以更改租户范围的配置，例如服务设置和订阅计费。 单因素身份验证容易受到各种攻击，如网络钓鱼和密码破解。 因此，必须验证想要访问 Azure 资源管理器的用户的标识并更新配置，方法是：要求用户进行多重身份验证，在通过后才能进行访问。

**要求对服务管理进行 MFA** 是一项[基线策略](concept-baseline-protection.md)，它要求对访问 Azure 门户、Azure PowerShell 或 Azure CLI 的任何用户进行 MFA。 此策略应用于所有访问 Azure 资源管理器的用户，不管这些用户是否为管理员。

在租户中启用此策略以后，所有登录 Azure 管理资源的用户都会被系统要求进行多重身份验证。 如果用户未注册 MFA，则系统会要求用户使用 Microsoft Authenticator 应用进行注册，然后才能继续操作。

若要使用 [Azure Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps) 执行交互式登录，请使用 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet。

```PowerShell
Connect-AzAccount
```

在运行时，此 cmdlet 将显示一个令牌字符串。 若要登录，请复制此字符串并在浏览器中将其粘贴到 [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)。 PowerShell 会话在进行身份验证后就会连接到 Azure。

若要使用 [Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) 执行交互式登录，请运行 [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) 命令。

```azurecli
az login
```

如果 CLI 可以打开默认的浏览器，则它会打开该浏览器并加载登录页。 否则，你需要打开一个浏览器页面，在浏览器中导航到 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 后，按照有关命令行的说明输入授权代码。 然后，在浏览器中使用帐户凭据登录。

## <a name="deployment-considerations"></a>部署注意事项

"**需要服务的 MFA** " 策略适用于所有 Azure 资源管理器用户。

## <a name="enable-the-baseline-policy"></a>启用基线策略

策略“基线策略:要求对服务管理进行 MFA (预览)”已预配置，当你在 Azure 门户中导航到“条件访问”边栏选项卡时，它会显示在顶部。

若要启用此策略并保护管理员，请执行以下操作：

1. 以全局管理员、安全管理员或条件访问管理员的身份登录 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “条件访问”。
1. 在策略列表中选择“基线策略:要求对服务管理进行 MFA (预览版)”。
1. 将“启用策略”设置为“立即使用策略”。
1. 单击“保存”。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

* [条件访问基线保护策略](concept-baseline-protection.md)
* [保护标识基础结构的五个步骤](../../security/fundamentals/steps-secure-identity.md)
* [什么是 Azure Active Directory 中的条件访问？](overview.md)
