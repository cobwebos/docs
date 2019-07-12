---
title: Azure Active Directory 无密码登录 （预览版）
description: 无密码登录到 Azure AD 使用 FIDO2 安全密钥或 Microsoft 验证器应用 （预览版）
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d80b359be0a6249327ba1ba1d51ffbc330bb073
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712080"
---
# <a name="what-is-passwordless"></a>无密码是什么？

多重身份验证 (MFA) 是保护你的组织，一个好办法，但用户获取之上无需记住其密码的其他层感到失望。 无密码身份验证方法是更方便，因为删除并替换为您必须加上你或你知道密码。

|   | 您必须 | 要查找的内容，或知道 |
| --- | --- | --- |
| 无密码 | 电话或安全密钥 | 生物识别或 PIN |

我们识别每个组织具有不同的需求，谈到身份验证。 Microsoft 当前提供了 Windows Hello，适用于 Windows PC 我们首要无密码体验。 我们要将新凭据添加到无密码的系列：Microsoft Authenticator 应用和 FIDO2 安全密钥。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 应用

允许员工的电话变得无密码身份验证方法。 您可能已经在使用 Microsoft Authenticator 应用作为密码除了方便多重身份验证选项。 但现在，便可作为无密码的选项。

这会变成任何 iOS 或 Android 手机强、 无密码的凭据通过允许用户通过获取到他们的手机的通知，匹配到他们的手机上一个屏幕上显示一个行号，然后使用其生物识别 （登录到任何平台或浏览器触摸或人脸） 或 PIN 以确认。

## <a name="fido2-security-keys"></a>FIDO2 安全密钥

FIDO2 安全密钥是可在任何窗体中来自 unphishable 基于标准的无密码身份验证方法。 快速 Identity Online (FIDO) 是一种开放标准进行无密码身份验证。 它允许用户和组织能够利用标准登录到其资源而无需用户名或密码使用外部安全密钥或内置于设备的平台键。

对于公共预览版，员工可以使用外部安全密钥以登录到 Azure Active Directory 已加入 Windows 10 计算机 （运行版本 1809年或更高版本），并获取单一登录到其云资源。 它们还可以登录支持的浏览器。

尽管有多个键都 FIDO2 FIDO Alliance 通过认证，Microsoft 要求 FIDO2 CTAP 规范由供应商联系以确保最高的安全性并获得最佳体验来实现一些可选扩展。

安全密钥**必须**可实现以下功能和从 Microsoft 兼容的 FIDO2 CTAP 协议扩展：

| # | 功能 / 扩展信任 | 这是为什么必需的？ |
| --- | --- | --- |
| 1 | 常驻密钥 | 此功能，可移植性，你的凭据存储在安全密钥的位置的安全密钥。 |
| 2 | 客户端 pin | 此功能使你能够保护你的凭据与第二个因素，适用于不具有用户界面的安全密钥。 |
| 3 | hmac-secret | 此扩展可确保你可以登录到你的设备脱机或飞行模式中时。 |
| 4 | 每个 RP 的多个帐户 | 此功能可确保可以跨多个 Microsoft 帐户和 Azure Active Directory 等服务使用相同的安全密钥。 |

以下提供商提供已知符合 paswordless 体验不同的外形规格 FIDO2 安全的密钥。 Microsoft 鼓励客户可以通过联系供应商以及 FIDO Alliance 评估这些密钥的安全属性。

| 提供程序 | 联系人 |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://ensurity.com/contact-us.html](https://ensurity.com/contact-us.html) |
| eWBM | [https://www.ewbm.com/page/sub1_5](https://www.ewbm.com/page/sub1_5) |

如果供应商并想要在此列表中获取你的设备，请联系[ Fido2Request@Microsoft.com ](mailto:Fido2Request@Microsoft.com)。

FIDO2 安全密钥是理想之选适用于企业非常安全敏感或有方案或不愿意或能够使用他们的手机作为第二个因素的员工。

## <a name="what-scenarios-work-with-the-preview"></a>哪些方案是否适用于预览版？

1. 管理员可以为其租户中启用无密码身份验证方法
1. 管理员可以针对所有用户也可以选择为每个方法的租户中的用户/组
1. 最终用户可以注册和管理其帐户门户中的这些无密码身份验证方法
1. 最终用户可以使用这些无密码身份验证方法登录
   1. Microsoft Authenticator 应用：将在其中使用 Azure AD 身份验证时，每个方案的工作包括在所有浏览器，在 Windows 10 出框 (OOBE) 安装程序和过程集成在任何操作系统上的移动应用。
   1. 安全密钥：将适用于 Windows 10 1809年或更高版本的锁屏界面和支持的浏览器，如 Microsoft Edge 中的 web。

## <a name="next-steps"></a>后续步骤

[启用你的组织中的无密码选项](howto-authentication-passwordless-enable.md)

### <a name="external-links"></a>外部链接

[FIDO Alliance](https://fidoalliance.org/)

[FIDO2 CTAP 规范](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
