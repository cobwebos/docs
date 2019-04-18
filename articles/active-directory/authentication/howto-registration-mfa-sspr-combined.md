---
title: 开始使用组合注册适用于 Azure AD SSPR 和多重身份验证 （预览版）-Azure Active Directory
description: 启用结合使用 Azure AD 多重身份验证和自助服务密码重置注册 （预览版）
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
ms.openlocfilehash: 3baf2690ae07b87bb4d5dba30fcd20f62a1a4506
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59280564"
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

## <a name="next-steps"></a>后续步骤

[多重身份验证和 SSPR 的可用方法](concept-authentication-methods.md)

[配置自助服务密码重置](howto-sspr-deployment.md)

[配置 Azure 多重身份验证](howto-mfa-getstarted.md)

[故障排除结合安全信息注册](howto-registration-mfa-sspr-combined-troubleshoot.md)