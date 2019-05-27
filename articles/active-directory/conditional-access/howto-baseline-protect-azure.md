---
title: 基准策略的服务管理 （预览版）-Azure Active Directory 要求 MFA
description: 条件性访问策略需要 MFA 的 Azure 资源管理器
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
ms.openlocfilehash: 00ed40bef3f3cbe59825f546ffa39c3ebfb2e41f
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003171"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>基准策略：服务管理 （预览版） 要求进行 MFA

您可能会在你的组织中使用各种 Azure 服务。 这些服务可以通过 Azure 资源管理器 API 管理：

* Azure 门户
* Azure PowerShell
* Azure CLI

使用 Azure 资源管理器来管理你的服务是一个高特权的操作。 Azure 资源管理器可以更改租户范围的配置，例如服务设置和订阅计费。 单因素身份验证容易受到各种攻击威胁，例如网络钓鱼和密码喷射。 因此，务必要验证的用户想要访问 Azure 资源管理器和更新配置，通过要求才允许访问的多重身份验证的身份。

**服务管理需要 MFA**是[基准策略](concept-baseline-protection.md)，将要求对访问 Azure 门户、 Azure PowerShell 或 Azure CLI 的任何用户进行 MFA。 此策略适用于所有访问 Azure 资源管理器，而不考虑，如果他们作为管理员的用户。

在租户中启用此策略后，登录到 Azure 管理资源的所有用户将都面临多重身份验证。 如果用户未注册 MFA，用户将需要注册才能继续使用 Microsoft Authenticator 应用。

![需要 MFA 的 Azure 资源管理器](./media/howto-baseline-protect-azure/baseline-policy-require-mfa-for-service-management.png)

若要执行交互式登录中使用[Azure Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)，使用[Connect AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet。

```PowerShell
Connect-AzAccount
```

在运行时，此 cmdlet 将显示一个令牌字符串。 若要登录，请复制此字符串并将其粘贴到 [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) 在浏览器中。 PowerShell 会话在进行身份验证后就会连接到 Azure。

若要执行交互式登录中使用[Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)，请运行 [az 登录](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) 命令。

```azurecli
az login
```

如果 CLI 可以打开默认的浏览器，则它会打开该浏览器并加载登录页。 否则，您需要打开一个浏览器页面并按照用于导航到后输入授权代码的命令行上的说明 [https://aka.ms/devicelogin ](https://aka.ms/devicelogin)在浏览器中。 然后，使用你在浏览器中的帐户凭据登录。

## <a name="deployment-considerations"></a>部署注意事项

因为**服务管理的要求使用 MFA**策略适用于所有 Azure 资源管理器用户，需要进行，以确保顺利完成部署几个注意事项。 这些注意事项包括识别用户并不能或不应执行 MFA，以及应用程序和由你的组织不支持新式身份验证的客户端的 Azure AD 中的服务主体。

### <a name="user-exclusions"></a>用户排除项

此基线策略提供选项来排除用户。 在启用之前该策略为你的租户，我们建议排除以下帐户：

* **紧急访问**或**break glass**以防止租户范围内帐户锁定的帐户。 在所有管理员被都锁定在租户外部不太可能方案中，可以使用紧急访问管理帐户能够登录到租户采取步骤来恢复访问权限。
   * 可在本文中，找到更多信息[在 Azure AD 中管理紧急访问帐户](../users-groups-roles/directory-emergency-access.md)。
* **服务帐户**并**服务原则**，例如 Azure AD Connect 同步帐户。 服务帐户是不受限于任何特定用户的非交互式帐户。 它们通常由后端服务，并且允许以编程方式访问应用程序。 应排除服务帐户，因为不能以编程方式完成 MFA。
   * 如果你的组织中的脚本或代码中使用这些帐户，请考虑将它们替换为 [托管标识](../managed-identities-azure-resources/overview.md)。 作为临时的解决方法，可以从基准策略中排除这些特定的帐户。
* 不具有或不能使用智能手机的用户。
   * 此策略要求用户使用 Microsoft Authenticator 应用的 mfa 注册。

## <a name="enable-the-baseline-policy"></a>启用基线策略

策略**基准策略：服务管理 （预览） 需要 MFA**预配置，此时将显示在顶部导航到在 Azure 门户中的条件性访问边栏选项卡时。

若要启用此策略并保护你的管理员：

1. 登录到 **Azure 门户** 作为全局管理员、 安全管理员或条件性访问管理员。
1. 浏览到**Azure Active Directory** > **条件性访问**。
1. 在策略列表中，选择**基准策略：服务管理 （预览） 需要 MFA**。
1. 设置**启用策略**到**立即使用策略**。
1. 通过单击添加任何用户排除项**用户** > **选择排除的用户**，然后选择需要排除的用户。 单击**选择**然后**完成**。
1. 单击 **保存**。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

* [条件性访问基线保护策略](concept-baseline-protection.md)
* [保护标识基础结构的五个步骤](../../security/azure-ad-secure-steps.md)
* [什么是 Azure Active Directory 中的条件访问？](overview.md)