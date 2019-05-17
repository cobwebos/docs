---
title: 开始使用组合注册适用于 Azure AD SSPR 和多重身份验证 （预览版）-Azure Active Directory
description: 启用结合使用 Azure AD 多重身份验证和自助服务密码重置注册 （预览版）
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/1/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: abe9cba604100a42a4cd29ccd5af47e8898ea409
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65812932"
---
# <a name="enable-combined-security-information-registration-preview"></a>启用结合安全信息注册 （预览版）

启用新的体验之前，请查看文章[结合使用安全信息注册 （预览版）](concept-registration-mfa-sspr-combined.md)以确保您了解功能以及此功能的效果。

![组合的安全信息注册增强的体验](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| 组合的安全信息注册 Azure 多重身份验证和 Azure Active Directory (Azure AD) 自助服务密码重置是 Azure AD 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

## <a name="enable-combined-registration"></a>启用合并注册

完成这些步骤以启用组合的注册：

1. 登录到 Azure 门户中，如下用户管理员或全局管理员。
2. 转到**Azure Active Directory** > **用户设置** > **管理的访问面板中预览功能设置**。
3. 下**用户可以使用预览功能来注册和管理的安全信息-刷新**，选择为启用**选定**的用户或组**所有**用户。

   ![启用所有用户的组合的安全信息预览体验](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> 从 2019 年 3 月开始，电话呼叫选项将不可供多重身份验证和 SSPR 免费/试用 Azure AD 租户中的用户。 短信不受此更改。 电话呼叫选项仍将为用户提供付费 Azure AD 租户。

> [!NOTE]
> 启用后结合注册，注册或确认其电话号码或通过新体验的移动应用程序可以将其用于多重身份验证和 SSPR，如果这些方法启用多重身份验证和 SSPR 中的用户策略。 这时，如果禁用此体验，请转到以前的 SSPR 注册的用户页上，在`https:/aka.ms/ssprsetup`需要执行多重身份验证才能访问页面。

如果在 Internet Explorer 中配置了站点到区域分配列表，以下站点必须位于同一区域：

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>组合注册的条件性访问策略

保护何时以及如何为 Azure 多重身份验证和自助服务密码重置的用户注册现已可用于条件性访问策略中的用户操作。 此预览功能可供组织对已启用[结合使用注册预览](../authentication/concept-registration-mfa-sspr-combined.md)。 在组织中所需用户注册 Azure 多重身份验证和从中心位置，如受信任的网络位置的 SSPR HR 载入期间的位置，可能会启用此功能。 有关创建条件访问中的受信任的位置的详细信息，请参阅文章[什么是 Azure Active Directory 条件访问中的位置条件？](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>创建一个策略需要从受信任位置的注册

以下策略适用于所有所选用户，尝试注册使用的组合的注册体验和阻止的访问，除非它们从标记为受信任的网络位置进行连接。

![创建用于控制安全信息注册的 CA 策略](media/howto-registration-mfa-sspr-combined/conditional-access-register-security-info.png)

1. 在中**Azure 门户**，浏览到**Azure Active Directory** > **条件性访问**
1. 选择“新建策略”
1. 在名称，输入此策略的名称。 例如，**结合安全信息注册受信任的网络上**
1. 下**分配**，单击**用户和组**，并选择用户和你想要应用此策略的组

   > [!WARNING]
   > 必须为用户启用[结合使用注册预览](../authentication/howto-registration-mfa-sspr-combined.md)。

1. 下**云应用程序或操作**，选择**用户操作**，检查**注册安全信息 （预览版）**
1. 下**条件** > **位置**
   1. 配置**是**
   1. 包括**的任何位置**
   1. 排除**所有受信任的位置**
   1. 单击**完成**位置边栏选项卡上
   1. 单击**完成**上的条件边栏选项卡
1. 下**的访问控制** > **授予**
   1. 单击**阻止访问**
   1. 然后单击**选择**
1. 设置**启用策略**到**上**
1. 然后单击**创建**

## <a name="next-steps"></a>后续步骤

[多重身份验证和 SSPR 的可用方法](concept-authentication-methods.md)

[配置自助服务密码重置](howto-sspr-deployment.md)

[配置 Azure 多重身份验证](howto-mfa-getstarted.md)

[故障排除结合安全信息注册](howto-registration-mfa-sspr-combined-troubleshoot.md)

[什么是 Azure Active Directory 条件性访问的位置条件？](../conditional-access/location-condition.md)