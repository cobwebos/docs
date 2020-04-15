---
title: SSPR 和 MFA 的组合注册 - Azure Active Directory
description: Azure AD 多重身份验证和自助服务密码重置注册（预览版）
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26656b6dafd91d47c05c2d1f923e53f4ba790cf8
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309913"
---
# <a name="combined-security-information-registration-preview"></a>组合安全信息注册（预览）

在合并注册之前，用户分别注册了 Azure 多重身份验证和自助服务密码重置 （SSPR） 的身份验证方法。 人们感到困惑的是，类似的方法被用于多重身份验证和SSPR，但他们必须注册这两个功能。 现在，通过合并注册，用户可以注册一次，并获得多重身份验证和 SSPR 的优势。

![显示用户已注册安全信息的我的个人资料](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

在启用新体验之前，请查看此以管理员为中心的文档和以用户为中心的文档，以确保您了解此功能的功能和效果。 基于[用户文档](../user-help/user-help-security-info-overview.md)进行培训，为用户准备新体验，并帮助确保成功推出。

Azure AD 组合安全信息注册目前不适用于 Azure 美国政府、Azure 德国或 Azure 中国 21Vianet 等国家云。

|     |
| --- |
| 多重身份验证和 Azure 活动目录 （Azure AD） 自助服务密码重置的组合安全信息注册是 Azure AD 的公共预览功能。 有关预览的详细信息，请参阅 Microsoft [Azure 预览的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

> [!IMPORTANT]
> 启用原始预览和增强组合注册体验的用户将看到新行为。 启用两种体验的用户将仅看到新的"我的配置文件"体验。 新的"我的个人资料"与合并注册的外观保持一致，为用户提供无缝体验。 用户可以通过访问[https://myprofile.microsoft.com](https://myprofile.microsoft.com)查看我的个人资料。

> [!NOTE] 
> 在尝试访问安全信息选项时，可能会遇到错误消息。 例如，"对不起，我们无法登录"。 在这种情况下，请确认您没有任何阻止 Web 浏览器上第三方 Cookie 的配置或组策略对象。 

我的"配置文件"页面根据访问该页面的计算机的语言设置进行本地化。 Microsoft 存储浏览器缓存中使用的最新语言，因此后续访问页面的尝试将继续以使用的最后一种语言呈现。 如果清除缓存，页面将重新呈现。 如果要强制特定语言，可以添加到`?lng=<language>`URL 的末尾，其中要呈现的语言的代码。 `<language>`

![设置 SSPR 或其他安全验证方法](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>组合注册中可用的方法

组合注册支持以下身份验证方法和操作：

|   | 注册 | 更改 | 删除 |
| --- | --- | --- | --- |
| Microsoft Authenticator | 是（最多 5 个） | 否 | 是 |
| 其他验证器应用 | 是（最多 5 个） | 否 | 是 |
| 硬件令牌 | 否 | 否 | 是 |
| 电话 | 是 | 是 | 是 |
| 备用号码 | 是 | 是 | 是 |
| 办公电话 | 否 | 否 | 否 |
| 电子邮件 | 是 | 是 | 是 |
| 安全提问 | 是 | 否 | 是 |
| 应用密码 | 是 | 否 | 是 |
| FIDO2 安全密钥<br />*仅从[安全信息](https://mysignins.microsoft.com/security-info)页面进行托管模式*| 是 | 是 | 是 |

> [!NOTE]
> 应用密码仅适用于已强制实施多重身份验证的用户。 应用密码对于通过条件访问策略启用多重身份验证的用户不可用。

用户可以将以下选项之一设置为默认的多重身份验证方法：

- 微软身份验证器 – 通知。
- 身份验证器应用或硬件令牌 + 代码。
- 电话。
- 短信。

随着我们继续向 Azure AD 添加更多身份验证方法，这些方法将在合并注册中可用。

## <a name="combined-registration-modes"></a>组合注册模式

合并注册有两种模式：中断和管理。

- **中断模式**是类似于向导的体验，在用户注册或刷新登录时呈现给用户。

- **管理模式**是用户配置文件的一部分，允许用户管理其安全信息。

对于这两种模式，以前注册了可用于多重身份验证的方法的用户在访问其安全信息之前需要执行多重身份验证。

### <a name="interrupt-mode"></a>中断模式

如果为租户启用了两种策略，则合并注册同时尊重多重身份验证和 SSPR 策略。 这些策略控制用户在登录期间是否中断注册，以及哪些方法可用于注册。

以下是可能提示用户注册或刷新其安全信息的几个方案：

- 通过身份保护强制注册的多重身份验证注册：在登录期间要求用户注册。 它们注册多重身份验证方法和 SSPR 方法（如果用户启用了 SSPR）。
- 通过每个用户多重身份验证强制进行多重身份验证注册：在登录期间要求用户注册。 它们注册多重身份验证方法和 SSPR 方法（如果用户启用了 SSPR）。
- 通过条件访问或其他策略强制进行多重身份验证注册：当用户使用需要多重身份验证的资源时，系统将被要求进行注册。 它们注册多重身份验证方法和 SSPR 方法（如果用户启用了 SSPR）。
- 实施 SSPR 注册：要求用户在登录期间注册。 它们仅注册 SSPR 方法。
- 实施 SSPR 刷新：用户需要按管理员设置的时间间隔查看其安全信息。用户会显示其信息，并可以确认当前信息或根据需要进行更改。

强制执行注册时，将显示用户从大多数到最低安全点，以符合多重身份验证和 SSPR 策略所需的最少方法数。

例如：

- 为 SSPR 启用了用户。 SSPR 策略需要两种方法来重置，并启用了移动应用代码、电子邮件和电话。
   - 此用户需要注册两种方法。
      - 默认情况下，用户将显示身份验证器应用和手机。
      - 用户可以选择注册电子邮件，而不是验证器应用或电话。

此流程图描述在登录期间中断注册时向用户显示哪些方法：

![组合安全信息流程图](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

如果同时启用了多重身份验证和 SSPR，我们建议您强制实施多重身份验证注册。

如果 SSPR 策略要求用户定期查看其安全信息，则用户在登录过程中会中断并显示其所有注册方法。 如果当前信息是最新的，他们可以确认当前信息，也可以在需要时进行更改。 用户在访问此页面时必须执行多重身份验证。

### <a name="manage-mode"></a>管理模式

用户可以通过访问[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)或从"我的个人资料"中选择**安全信息**来访问管理模式。 从那里，用户可以添加方法、删除或更改现有方法、更改默认方法等。

## <a name="key-usage-scenarios"></a>关键使用方案

### <a name="set-up-security-info-during-sign-in"></a>在登录过程中设置安全信息

管理员已强制执行注册。

用户尚未设置所有必需的安全信息并转到 Azure 门户。 输入用户名和密码后，系统会提示用户设置安全信息。 然后，用户按照向导中显示的步骤设置所需的安全信息。 如果您的设置允许，用户可以选择设置默认显示的方法以外的方法。 完成向导后，用户将查看他们设置的方法及其用于多重身份验证的默认方法。 要完成安装过程，用户将确认信息并继续访问 Azure 门户。

### <a name="set-up-security-info-from-my-profile"></a>从"我的个人资料"设置安全信息

管理员尚未强制执行注册。

尚未设置所有必需的安全信息的用户将转到[https://myprofile.microsoft.com](https://myprofile.microsoft.com)。 用户选择左侧窗格中**的安全信息**。 从那里，用户选择添加方法，选择任何可用的方法，并按照步骤设置该方法。 完成后，用户将看到刚刚在"安全信息"页上设置的方法。

### <a name="delete-security-info-from-my-profile"></a>从"我的个人资料"中删除安全信息

以前至少设置一个方法的用户将导航到[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)。 用户选择删除以前注册的方法之一。 完成后，用户不再在"安全信息"页上看到该方法。

### <a name="change-the-default-method-from-my-profile"></a>从"我的配置文件"更改默认方法

以前至少设置一种可用于多重身份验证的方法的用户将导航到[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)。 用户将当前默认方法更改为其他默认方法。 完成后，用户在"安全信息"页上看到新的默认方法。

## <a name="next-steps"></a>后续步骤

[强制用户重新注册身份验证方法](howto-mfa-userdevicesettings.md#manage-user-authentication-options)

[在租户中启用合并注册](howto-registration-mfa-sspr-combined.md)

[SSPR 和 MFA 使用情况和见解报告](howto-authentication-methods-usage-insights.md)

[多因素身份验证和 SSPR 的可用方法](concept-authentication-methods.md)

[配置自助服务密码重置](howto-sspr-deployment.md)

[配置 Azure 多重身份验证](howto-mfa-getstarted.md)
