---
title: 有关 Azure AD SSPR MFA （预览） 的组合的注册
description: Azure AD 多重身份验证和自助服务密码重置注册 （预览版）
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa9872ef76264956430bb69856a197042c196dfd
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57550184"
---
# <a name="combined-security-information-registration-preview"></a>组合的安全信息注册 （预览版）

组合注册之前用户注册 Azure 多重身份验证 (MFA) 和两个不同的体验通过自助服务密码重置 (SSPR) 身份验证方法。 用户已针对 Azure MFA 和 SSPR 使用了类似方法，但它们必须单独注册每个功能相混淆。 现在，进行组合的注册，用户可以一次注册并获取 Azure MFA 和 SSPR 的优势。

![结合使用安全信息-我的个人资料显示已注册用户，包括： Microsoft Authenticator 和电话目录中的示例用户的安全信息。](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

启用新的体验之前, 查看此管理员为中心的文档和专注于用户的文档以确保您了解的功能和影响此功能。 使您的用户文档的新体验准备你的用户和帮助确保成功推出的培训。

|     |
| --- |
| 组合的安全信息注册 Azure 多重身份验证和 Azure AD 自助服务密码重置是 Azure Active Directory 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

> [!IMPORTANT]
> 如果原始的预览和增强的组合的注册体验启用了用户，他们将看到新体验。 为这两种体验启用的用户将只看到新的我的配置文件体验。 新我的个人资料与组合注册的外观保持一致，并为用户提供无缝体验。 用户可以通过转到查看我的个人资料[ https://myprofile.microsoft.com ](https://myprofile.microsoft.com)。

MyProfile 页面将本地化基于访问页面的计算机上的当前语言设置。 Microsoft 将存储利用浏览器缓存中，以便访问的后续尝试仍将呈现在最后一个语言中使用的最新语言。 清除缓存将会重新呈现页面。 如果你想要强制特定语言添加`?lng=de-DE`到 URL 的末尾位置`de-DE`设置到的相应语言代码将强制在该语言中要呈现的页。

![显示安全信息和用户设置 SSPR 或其他安全验证的其他方法允许我配置文件的接口。](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-converged-registration"></a>聚合注册中可用的方法

在此期间，组合的注册为这些方法支持的以下方法和操作：

|   | 注册 | 更改 | 删除 |
| --- | --- | --- | --- |
| Microsoft Authenticator | 是 (最大 5) | 否 | 是 |
| 其他身份验证器应用 | 是 (最大 5) | 否 | 是 |
| 硬件标志 | 否 | 否 | 是 |
| 电话 | 是 | 是 | 是 |
| 备用号码 | 是 | 是 | 是 |
| 办公电话 | 否 | 否 | 否 |
| 电子邮件 | 是 | 是 | 是 |
| 安全提问 | 是 | 否 | 是 |
| 应用密码 | 是 | 否 | 是 |

> [!NOTE]
> 应用密码才可供已强制实施 MFA 的用户使用。 应用密码不是可用于通过条件性访问策略启用了 MFA 的用户。

Mfa，用户可以为其默认方法设置以下选项：

- Microsoft Authenticator-通知
- 验证器应用程序或硬件令牌-代码
- 电话呼叫
- 短信

在我们继续添加到 Azure AD 的多个身份验证方法此类，这些方法将在组合注册中提供。

## <a name="combined-registration-modes"></a>组合的注册模式

有两种"模式"的组合的注册： 中断和管理。 

中断模式下，是一种类似于向导的体验，在注册或刷新其安全信息，请参阅在登录时向用户显示。 

管理模式是用户的配置文件的一部分，使他们能够管理其安全信息。 

这两种模式中，如果用户以前已注册的方法，用于 MFA，它们将需要执行 MFA，才能访问其安全信息。

### <a name="interrupt-mode"></a>中断模式

组合的注册尊重 MFA 和 SSPR 策略，如果为租户启用了两种。 这些策略可以控制、 是否中断用户在登录过程中，注册，并且是可用于注册的方法。

以下列表可能会提示用户注册或刷新其安全信息的几个方案：

* 通过 Identity Protection 来强制实施 MFA 注册：将要求用户在登录过程中注册。 他们注册 MFA 的方法和 SSPR 方法 （如果用户已启用的 SSPR）。
* 通过每用户 MFA 强制实施 MFA 注册：将要求用户在登录过程中注册。 他们注册 MFA 的方法和 SSPR 方法 （如果用户已启用的 SSPR）。
* 通过条件性访问策略或其他策略强制执行 MFA 注册：用户需要时访问的资源的需要进行 MFA 注册。 用户将注册 MFA 的方法和 SSPR 方法 （如果用户已启用的 SSPR）。
* 强制实施的 SSPR 注册：要求用户在登录过程中注册。 它们仅注册 SSPR 方法
* SSPR 刷新强制执行：用户需要管理员设置的时间间隔查看其安全信息用户显示其信息和可以选择"看上去很好"或根据需要进行更改。

当强制注册时，用户会显示符合从大多数的 MFA 和 SSPR 策略所需的方法的最小数量以最不安全。

示例：

* 为用户启用 sspr。 SSPR 策略所需重置两个方法，并启用了移动应用程序代码、 电子邮件和电话。
   * 此用户需要注册两个方法。
      * 默认情况下，它们是显示身份验证器应用和 phone。
      * 用户可以选择注册而不是身份验证器应用或电话的电子邮件。

以下流程图描述了哪些方法显示给用户时中断在登录过程中注册：

![结合使用解释的方法时的详细信息是必需的在登录时所需数量的安全信息流程图。 如果只需要 MFA 或仅 SSPR，这可能会更改](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

如果您具有 MFA 和启用 SSPR，我们建议您强制执行 MFA 注册。

SSPR 策略要求用户定期查看其安全信息，如果用户是在登录期间中断，并显示其已注册的所有方法。 如果信息是最新的或他们可以选择"编辑信息"，他们可以选择"看起来不错"进行更改。

### <a name="manage-mode"></a>管理模式

用户可以访问通过转到管理模式[ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo)或通过从我的配置文件选择"安全信息"。 在这里，用户可以将方法添加、 删除或更改现有方法、 更改其默认方法，和的详细信息。

## <a name="key-usage-scenarios"></a>密钥用法方案

### <a name="set-up-security-info-during-sign-in"></a>在登录过程中设置的安全信息

管理员已强制执行注册。

用户未设置所有所需的安全信息，并导航到 Azure 门户。 在输入其用户名和密码之后, 被提示用户设置的安全信息。 用户然后按照向导中显示的用于设置所需的安全信息的步骤。 用户可以选择设置之外什么显示默认情况下，如果您的设置允许的方法。 在向导结束时，用户查看他们设置的方法和它们的默认方法为 MFA。 若要完成安装过程，用户确认信息，并将继续执行到 Azure 门户。

### <a name="set-up-security-info-from-my-profile"></a>从我的配置文件设置的安全信息

管理员不具有强制执行注册。

具有尚未设置所有所需的安全信息的用户导航到[ https://myprofile.microsoft.com ](https://myprofile.microsoft.com)。 用户然后选择**的安全信息**从左侧导航栏中。 从此处，用户选择添加方法、 选择任何一种方法可供用户，以下步骤设置该方法。 完成后，用户会看到他们只需设置安全信息页面的方法。

### <a name="delete-security-info-from-my-profile"></a>从我的配置文件中删除的安全信息

以前设置了至少一种方法的用户导航到[ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo)。 用户选择删除以前已注册的方法之一。 完成后，用户无法再在安全信息页面上看到该方法。

### <a name="change-default-method-from-my-profile"></a>从我的个人资料更改默认方法

以前设置了至少一种方法，用于 MFA 的用户导航到[ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo)。 用户更改其当前的默认方法为不同的默认方法。 完成后，用户可以在安全信息页面中看到其新的默认方法。

## <a name="next-steps"></a>后续步骤

[启用你的租户中的组合的注册](howto-registration-mfa-sspr-combined.md)

[针对 MFA 和 SSPR 的可用方法](concept-authentication-methods.md)

[配置自助服务密码重置](howto-sspr-deployment.md)

[配置 Azure 多重身份验证](howto-mfa-getstarted.md)
