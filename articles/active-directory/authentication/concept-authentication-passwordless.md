---
title: Azure Active Directory 无密码登录（预览版）
description: 了解使用 FIDO2 安全密钥或 Microsoft Authenticator 应用无密码登录到 Azure Active Directory 的选项
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba579d6da8c759a4653b729f1a471efdedc2baa7
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505764"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Azure Active Directory 的无密码 authentication 选项

多重身份验证（MFA）是保护组织的一种好方法，但用户通常会在必须记住其密码的情况下使用额外的安全层。 无密码身份验证方法更为方便，因为密码会被删除并替换为你拥有的内容，以及你或你知道的内容。

|   | 你拥有的东西 | 你或知道的内容 |
| --- | --- | --- |
| 无密码 | Windows 10 设备、电话号码或安全密钥 | 生物识别或 PIN |

当涉及身份验证时，每个组织都有不同的需求。 Microsoft 提供以下三个无密码身份验证选项：

- Windows Hello for Business
- Microsoft Authenticator 应用
- FIDO2 安全密钥

![身份验证：安全性和便利性](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello for Business

Windows Hello 企业版非常适合拥有自己的指定 Windows PC 的信息工作者。 生物识别和 PIN 直接绑定到用户的 PC，这会阻止除所有者之外的任何人访问。 利用公钥基础结构（PKI）集成和单一登录（SSO）的内置支持，Windows Hello 企业版提供了一种方便的方法，可在本地和云中无缝访问公司资源。

Windows Hello 企业版[规划指南](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide)可用于帮助您决定 Windows Hello 企业版部署的类型以及需要考虑的选项。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 应用

允许员工的电话成为无密码的身份验证方法。 除密码外，你可能已使用 Microsoft Authenticator 应用作为便利的多重身份验证选项。 你还可以使用验证器应用作为无密码选项。

![通过 Microsoft Authenticator 应用登录 Microsoft Edge](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

验证器应用会将任何 iOS 或 Android 手机变成强、无密码凭据。 用户可以通过以下方式登录到任何平台或浏览器：向其手机发送通知，将屏幕上显示的数字与电话上的数字匹配，然后使用生物识别（触摸或人脸）或 PIN 确认。

## <a name="fido2-security-keys"></a>FIDO2 安全密钥

FIDO2 安全密钥是基于 unphishable 标准的无密码身份验证方法，可采用任何形式。 Fast Identity Online （FIDO）是无密码 authentication 的开放标准。 FIDO 允许用户和组织利用标准登录到其资源，而无需使用外部安全密钥或设备内置的平台密钥。

对于公共预览版，员工可以使用安全密钥登录到其 Azure AD 或混合 Azure AD 加入 Windows 10 设备，并对其云和本地资源进行单一登录。 用户还可以登录到受支持的浏览器。 对于安全敏感的企业而言，FIDO2 安全密钥是一个不错的选择，或者不愿意或无法使用其电话作为第二个因素的方案或员工。

![使用安全密钥登录 Microsoft Edge](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

尽管有很多密钥是通过 FIDO 联盟 FIDO2 认证的，但 Microsoft 需要由供应商实现的 FIDO2 客户端到验证器协议（CTAP）规范的一些可选扩展，以确保最高的安全性和最佳接触.

安全密钥**必须**实现 FIDO2 CTAP 协议中的以下功能和扩展，才能与 Microsoft 兼容：

| # | 功能/扩展信任 | 为什么需要此功能或扩展？ |
| --- | --- | --- |
| 1 | 居民密钥 | 此功能使安全密钥可移植，其中的凭据存储在安全密钥上。 |
| 2 | 客户端 pin | 利用此功能，你可以使用另一个因素来保护凭据，并将其应用于没有用户界面的安全密钥。 |
| 3 | hmac-secret | 此扩展可确保你可以在设备处于脱机状态或处于飞行模式时登录到你的设备。 |
| 4 | 每个 RP 多个帐户 | 此功能可确保你可以在多个服务（如 Microsoft 帐户和 Azure Active Directory）上使用相同的安全密钥。 |

以下提供商提供了 FIDO2 安全密钥，它们具有已知兼容无密码体验的不同形式因素。 建议你通过联系供应商以及 FIDO 联盟来评估这些密钥的安全属性。

| 提供程序 | 联系人 |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |

> [!NOTE]
> 如果你购买并计划使用基于 NFC 的安全密钥，则需要为安全密钥提供支持的 NFC 读卡器。 NFC 读卡器不是 Azure 要求或限制。 有关支持的 NFC 读卡器的列表，请与供应商联系以获取基于 NFC 的安全密钥。

如果你是供应商，并且想要在此受支持设备列表上获取设备，请联系[Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com)。

## <a name="what-scenarios-work-with-the-preview"></a>使用预览版的情况如何？

- 管理员可以为其租户启用无密码 authentication 方法
- 对于每个方法，管理员可面向所有用户或选择其租户中的用户/组
- 最终用户可以在其帐户门户中注册和管理这些无密码 authentication 方法
- 最终用户可以用这些无密码身份验证方法登录
   - Microsoft Authenticator 应用：在使用 Azure AD 身份验证的情况下工作，包括在所有浏览器中、在 Windows 10 开箱（OOBE）安装期间以及在任何操作系统上集成的移动应用。
   - 安全密钥：在受支持的浏览器（如 Microsoft Edge）中使用针对 Windows 10 和 web 的锁屏界面。

## <a name="next-steps"></a>后续步骤

[在组织中启用 FIDO2 security key passwordlesss 选项](howto-authentication-passwordless-security-key.md)

[在组织中启用基于手机的无密码选项](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>外部链接

[FIDO 联盟](https://fidoalliance.org/)

[FIDO2 CTAP 规范](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
