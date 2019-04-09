---
title: 针对 Azure AD SSPR 和多重身份验证 （预览版）-Azure Active Directory 组合注册
description: Azure AD 多重身份验证和自助服务密码重置注册 （预览版）
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f3eec1f846f1b74ab3e19bca022d4e009540d1a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280003"
---
# <a name="combined-security-information-registration-preview"></a>组合的安全信息注册 （预览版）

组合注册之前用户身份验证方法为 Azure 多重身份验证和自助服务密码重置 (SSPR) 分别注册。 人混淆的多重身份验证和 SSPR 使用了类似方法，但它们必须注册这两项功能。 现在，借助组合注册，用户可以一次注册，并利用多重身份验证和 SSPR。

![我的配置文件显示已注册的用户的安全信息](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

启用新的体验之前, 查看此管理员为中心的文档和专注于用户的文档以确保您了解的功能和此功能的效果。 使您的用户文档的新体验准备你的用户和帮助确保成功推出的培训。

|     |
| --- |
| 组合的安全信息注册多重身份验证和 Azure Active Directory (Azure AD) 自助服务密码重置是 Azure AD 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

> [!IMPORTANT]
> 启用了原始的预览和增强的组合的注册体验的用户将看到新行为。 启用了这两种体验的用户将看到仅我的配置文件体验。 新我的个人资料与组合注册的外观保持一致，并为用户提供无缝体验。 用户可以通过转到查看我的个人资料[ https://myprofile.microsoft.com ](https://myprofile.microsoft.com)。

我的配置文件页面将本地化基于访问页面的计算机的语言设置。 Microsoft 将存储在浏览器缓存中，使用，因此后续尝试访问的页将继续在所使用的最后一个语言中呈现的最新语言。 如果清除了缓存，将重新呈现页面。 如果你想要强制特定语言，您可以添加`?lng=<language>`到末尾的 URL，其中`<language>`是你想要呈现的语言的代码。

![将 SSPR 或其他安全验证方法设置](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>组合注册中可用的方法

结合使用注册支持以下身份验证方法和操作：

|   | 注册 | 更改 | 删除 |
| --- | --- | --- | --- |
| Microsoft Authenticator | 是 （最多 5 个） | 否 | 是 |
| 其他身份验证器应用 | 是 （最多 5 个） | 否 | 是 |
| 硬件标志 | 否 | 否 | 是 |
| 电话 | 是 | 是 | 是 |
| 备用号码 | 是 | 是 | 是 |
| 办公电话 | 否 | 否 | 否 |
| 电子邮件 | 是 | 是 | 是 |
| 安全提问 | 是 | 否 | 是 |
| 应用密码 | 是 | 否 | 是 |

> [!NOTE]
> 应用密码是仅供已强制实施多重身份验证的用户。 应用密码不是可用于通过条件性访问策略启用多重身份验证的用户。

用户可以设置以下选项之一作为默认多重身份验证方法：

- Microsoft Authenticator – 通知。
- 验证器应用程序或硬件令牌 – 代码。
- 电话呼叫。
- 文本消息。

在我们继续添加到 Azure AD 的更多的身份验证方法，这些方法将组合注册中提供。

## <a name="combined-registration-modes"></a>组合的注册模式

有两种模式的组合的注册： 中断和管理。

- **中断模式下**是一种类似于向导的体验，在注册或刷新其安全信息，请参阅在登录时向用户显示。

- **管理模式**用户配置文件的一部分，并允许用户管理其安全信息。

这两种模式之前已注册的方法，用于多重身份验证的用户将需要执行多重身份验证才能访问其安全信息。

### <a name="interrupt-mode"></a>中断模式

组合的注册尊重多重身份验证和 SSPR 策略，如果为租户启用了两种。 这些策略控制在登录期间是否注册为中断用户，哪些方法也可注册。

下面是在其中可能会提示用户注册或刷新其安全信息的几个方案：

* 通过 Identity Protection 来强制实施多重身份验证注册：要求用户在登录期间注册。 他们注册多重身份验证方法和 SSPR 方法 （如果用户已启用的 SSPR）。
* 通过每用户多重身份验证来强制实施多重身份验证注册：要求用户在登录期间注册。 他们注册多重身份验证方法和 SSPR 方法 （如果用户已启用的 SSPR）。
* 通过条件性访问策略或其他策略强制执行多重身份验证注册：要求用户注册使用时，它们需要多重身份验证的资源。 他们注册多重身份验证方法和 SSPR 方法 （如果用户已启用的 SSPR）。
* 强制实施的 SSPR 注册：要求用户在登录期间注册。 仅 SSPR 方法注册它们。
* SSPR 刷新强制执行：用户需要管理员设置的时间间隔查看其安全信息用户显示其信息和可以确认当前信息或根据需要进行更改。

当强制注册时，用户会显示符合的多重身份验证和 SSPR 策略，以最不安全的大多数所需的方法的最小数目。

例如：

* 为用户启用 sspr。 SSPR 策略所需重置两个方法，并启用了移动应用程序代码、 电子邮件和电话。
   * 此用户需要注册两个方法。
      * 用户是默认情况下显示的验证器应用和 phone。
      * 用户可以选择注册而不是身份验证器应用或电话的电子邮件。

此流程图描述了哪些方法显示给用户时中断以在登录期间注册：

![组合的安全信息流程图](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

如果您有多重身份验证和启用 SSPR，我们建议您强制执行多重身份验证注册。

SSPR 策略要求用户定期查看其安全信息，如果用户是在登录期间中断，并显示其已注册的所有方法。 如果它是最新状态，或在需要时进行更改，它们可以确认当前信息。

### <a name="manage-mode"></a>管理模式

用户可以访问通过转到管理模式[ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo)或通过选择**的安全信息**从我的配置文件。 在这里，用户可以将方法添加、 删除或更改现有方法、 更改默认方法，和的详细信息。

## <a name="key-usage-scenarios"></a>密钥用法方案

### <a name="set-up-security-info-during-sign-in"></a>在登录过程中设置的安全信息

管理员已强制执行注册。

用户未设置所有所需的安全信息，并转到 Azure 门户。 输入用户名和密码后，提示用户设置的安全信息。 用户然后按照向导中显示的用于设置所需的安全信息的步骤。 如果你的设置允许它，用户可以选择设置方法以外，默认情况下显示的方法。 完成向导后，用户查看他们设置的方法和它们的默认方法为多重身份验证。 若要完成安装过程，用户确认信息，并将继续执行到 Azure 门户。

### <a name="set-up-security-info-from-my-profile"></a>从我的配置文件设置的安全信息

管理员不具有强制执行注册。

尚未设置所有所需的安全信息的用户将转到[ https://myprofile.microsoft.com ](https://myprofile.microsoft.com)。 用户选择**的安全信息**的左窗格中。 从此处，用户选择添加一个方法，选择任何一种方法可用，并遵循的步骤设置该方法。 完成后，用户会看到安全信息页面设置只是该方法。

### <a name="delete-security-info-from-my-profile"></a>从我的配置文件中删除的安全信息

以前设置了至少一种方法的用户导航到[ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo)。 用户选择删除以前已注册的方法之一。 完成后，用户无法再在安全信息页面上看到该方法。

### <a name="change-the-default-method-from-my-profile"></a>更改我的配置文件中的默认方法

以前设置了至少一种方法，用于多重身份验证的用户导航到[ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo)。 用户更改当前的默认方法为不同的默认方法。 完成后，用户可以在安全信息页面中看到新的默认方法。

## <a name="next-steps"></a>后续步骤

[启用你的租户中的组合的注册](howto-registration-mfa-sspr-combined.md)

[多重身份验证和 SSPR 的可用方法](concept-authentication-methods.md)

[配置自助密码重置](howto-sspr-deployment.md)

[配置 Azure 多重身份验证](howto-mfa-getstarted.md)
