---
title: 开始使用组合注册适用于 Azure AD SSPR 和 MFA （预览版）-Azure Active Directory
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
ms.openlocfilehash: 25450d49fd32adf12ac6c8a71671a9cb796b06c4
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317436"
---
# <a name="enable-combined-security-information-registration-preview"></a>启用结合安全信息注册 （预览版）

启用新的体验之前，请查看文章[结合使用安全信息注册 （预览版）](concept-registration-mfa-sspr-combined.md)以确保您了解的功能和影响此功能。

![组合的安全信息注册增强的体验请求登录时的详细信息。 注册 Microsoft Authenticator 应用作为第一种方法的示例所示。](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| 组合的安全信息注册 Azure 多重身份验证和 Azure AD 自助服务密码重置是 Azure Active Directory 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

## <a name="enable-combined-registration"></a>启用合并注册

完成以下步骤以启用组合的注册：

1. 登录到 Azure 门户中，如下用户管理员或全局管理员。
2. 依次浏览到“Azure Active Directory” > “用户设置” > “管理访问面板预览功能的设置”。
3. 下**用户可以使用预览功能来注册和管理的安全信息-刷新**，选择为启用**选定**的用户或组**所有**用户。

![启用 Azure AD 门户中的所有用户的组合的安全信息预览体验](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> 开始在 2019 年 3 月中的电话呼叫选项将不能向免费/试用 Azure AD 租户中用户的 MFA 和 SSPR 的用户。 此更改不会影响短信。 电话呼叫将继续可供用户在付费 Azure AD 租户。 此更改只会影响免费/试用 Azure AD 租户。

> [!NOTE]
> 启用组合的注册，注册或确认其电话号码或通过新体验的移动应用程序可以将其用于 MFA 和 SSPR，如果 MFA 和 SSPR 策略中启用了这些方法的用户的一次。 这时，如果禁用此体验，请转到以前的 SSPR 注册的用户页上，在`https:/aka.ms/ssprsetup`需要执行多重身份验证才能访问页面。

如果已在以下站点必须在同一区域中的 Internet Explorer 中配置站点到区域分配列表：

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="next-steps"></a>后续步骤

[针对 MFA 和 SSPR 的可用方法](concept-authentication-methods.md)

[配置自助服务密码重置](howto-sspr-deployment.md)

[配置 Azure 多重身份验证](howto-mfa-getstarted.md)

[故障排除结合安全信息注册](howto-registration-mfa-sspr-combined-troubleshoot.md)