---
title: 'Azure Active Directory 无密码登录 (预览版) '
description: 了解使用 FIDO2 安全密钥或 Microsoft Authenticator 应用无密码登录到 Azure Active Directory 的选项
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2055730505b360ef8d5bf50cd83532627fb8e08
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245411"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Azure Active Directory 的无密码 authentication 选项

多重身份验证 (MFA) 等功能是保护组织的一种好方法，但用户通常会在必须记住其密码的情况下使用额外的安全层。 无密码身份验证方法更为方便，因为密码会被删除并替换为你拥有的内容，以及你或你知道的内容。

| 身份验证  | 你拥有的东西 | 你或知道的内容 |
| --- | --- | --- |
| 无密码 | Windows 10 设备、电话号码或安全密钥 | 生物识别或 PIN |

当涉及身份验证时，每个组织都有不同的需求。 Microsoft 提供了以下三个无密码身份验证选项，这些选项与 Azure Active Directory (Azure AD) ：

- Windows Hello for Business
- Microsoft Authenticator 应用
- FIDO2 安全密钥

![身份验证：安全性和便利性](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello for Business

Windows Hello 企业版非常适合拥有自己的指定 Windows PC 的信息工作者。 生物识别和 PIN 凭据直接绑定到用户的 PC，这会阻止除所有者之外的任何人访问。 利用公钥基础结构 (PKI) 集成和内置支持单一登录 (SSO) ，Windows Hello 企业版提供了一种方便的方法，可用于无缝访问本地和云中的公司资源。

![使用 Windows Hello 企业版进行用户登录的示例](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

以下步骤演示了如何使用 Azure AD 的登录过程：

![概述用户登录 Windows Hello 企业版所涉及步骤的示意图](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. 用户使用生物识别或 PIN 手势登录 Windows。 手势会解除对 Windows Hello 企业版私钥的锁定，并发送到云身份验证安全支持提供程序（称为 *云 AP 提供程序*）。
1. 云 AP 提供商请求 Azure AD 的 nonce。
1. Azure AD 返回有效时间为5分钟的 nonce。
1. 云 AP 提供程序使用用户的私钥对 nonce 进行签名，并将签名的 nonce 返回到 Azure AD。
1. Azure AD 使用用户安全注册的公钥对 nonce 签名进行签名。 验证签名后，Azure AD 验证返回的签名 nonce。 验证 nonce 后，Azure AD 将使用加密到设备传输密钥的会话密钥 (PRT) 创建主刷新令牌，并将其返回给云 AP 提供商。
1. 云 AP 提供程序接收带会话密钥的加密 PRT。 使用设备的专用传输密钥，云 AP 提供程序会解密会话密钥，并使用设备的受信任的平台模块 (TPM) 来保护会话密钥。
1. 云 AP 提供程序向 Windows 返回成功的身份验证响应。 然后，用户可以访问 Windows 以及云和本地应用程序，而无需再次 (SSO) 进行身份验证。

Windows Hello 企业版 [规划指南](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) 可用于帮助您决定 Windows Hello 企业版部署的类型以及需要考虑的选项。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 应用

你还可以允许员工的电话成为无密码的身份验证方法。 除密码外，你可能已使用 Microsoft Authenticator 应用作为便利的多重身份验证选项。 你还可以使用验证器应用作为无密码选项。

![通过 Microsoft Authenticator 应用登录 Microsoft Edge](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

验证器应用会将任何 iOS 或 Android 手机变成强、无密码凭据。 用户可以通过以下方式登录到任何平台或浏览器：向其手机发送通知，将屏幕上显示的数字与电话上的一个数字匹配，然后使用其生物识别 (触摸或面部) 或 PIN 以确认。 有关安装的详细信息，请参阅 [下载并安装 Microsoft Authenticator 应用](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) 。

使用验证器应用的无密码 authentication 遵循与 Windows Hello 企业版相同的基本模式。 这会稍微复杂一些，因为需要识别用户，以便 Azure AD 可以找到正在使用的 Microsoft Authenticator 应用程序版本：

![概述用户通过 Microsoft Authenticator 应用登录所涉及的步骤的示意图](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. 用户输入用户名。
1. Azure AD 检测到用户具有强凭据并启动强凭据流。
1. 通过 iOS 设备上的 Apple Push Notification 服务 (APNS) ，或通过 Android 设备上的 Firebase Cloud 消息传递 (FCM) 将通知发送到应用。
1. 用户收到推送通知并打开应用。
1. 该应用程序调用 Azure AD，并收到一项存在证据和 nonce。
1. 用户通过输入生物识别或 PIN 锁定私钥来完成质询。
1. Nonce 用私钥签名并发送回 Azure AD。
1. Azure AD 执行公钥/私钥验证并返回令牌。

若要开始无密码登录，请完成以下操作方法：

> [!div class="nextstepaction"]
> [启用使用验证器应用的无密码签名](howto-authentication-passwordless-phone.md)

## <a name="fido2-security-keys"></a>FIDO2 安全密钥

FIDO2 安全密钥是基于 unphishable 标准的无密码身份验证方法，可采用任何形式。 快速标识在线 (FIDO) 是用于无密码 authentication 的开放标准。 FIDO 允许用户和组织利用标准登录到其资源，而无需使用外部安全密钥或设备内置的平台密钥。

员工可以使用安全密钥登录到其 Azure AD 或混合 Azure AD 加入 Windows 10 设备，并对其云和本地资源进行单一登录。 用户还可以登录到受支持的浏览器。 对于安全敏感的企业而言，FIDO2 安全密钥是一个不错的选择，或者不愿意或无法使用其电话作为第二个因素的方案或员工。

通过 FIDO2 安全密钥登录到 Azure AD 当前为预览版。

![使用安全密钥登录 Microsoft Edge](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

当用户使用 FIDO2 安全密钥登录时，将使用以下过程：

![概述用户使用 FIDO2 安全密钥登录所涉及步骤的示意图](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. 用户将 FIDO2 安全密钥插入到计算机中。
2. Windows 检测 FIDO2 安全密钥。
3. Windows 发送身份验证请求。
4. Azure AD 发回 nonce。
5. 用户完成其笔势以解锁存储在 FIDO2 安全密钥的 secure enclave 中的私钥。
6. FIDO2 安全密钥用私钥对 nonce 进行签名。
7. 向 Azure AD 发送带签名 nonce (PRT) 令牌请求的主刷新令牌。
8. Azure AD 使用 FIDO2 公钥验证签名 nonce。
9. Azure AD 返回 PRT 以启用对本地资源的访问。

尽管有很多密钥 FIDO2 通过 FIDO 联盟进行了认证，Microsoft 还是需要由供应商实现的 FIDO2 客户端到身份验证器协议的一些可选扩展 (CTAP) 规范，以确保最高的安全性和最佳体验。

安全密钥 **必须** 实现 FIDO2 CTAP 协议中的以下功能和扩展，才能与 Microsoft 兼容：

| # | 功能/扩展信任 | 为什么需要此功能或扩展？ |
| --- | --- | --- |
| 1 | 居民密钥 | 此功能使安全密钥可移植，其中的凭据存储在安全密钥上。 |
| 2 | 客户端 pin | 利用此功能，你可以使用另一个因素来保护凭据，并将其应用于没有用户界面的安全密钥。 |
| 3 | hmac 密钥 | 此扩展可确保你可以在设备处于脱机状态或处于飞行模式时登录到你的设备。 |
| 4 | 每个 RP 多个帐户 | 此功能可确保你可以在多个服务（如 Microsoft 帐户和 Azure Active Directory）上使用相同的安全密钥。 |

以下提供商提供了 FIDO2 安全密钥，它们具有已知兼容无密码体验的不同形式因素。 建议你通过联系供应商以及 FIDO 联盟来评估这些密钥的安全属性。

| 提供程序 | 联系人 |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://shop.ftsafe.us/pages/microsoft](https://shop.ftsafe.us/pages/microsoft) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| TrustKey 解决方案 | [https://www.trustkeysolutions.com/security-keys/](https://www.trustkeysolutions.com/security-keys/) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Gemalto 身份 (Thales 组)  | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| OneSpan Inc。 | [https://www.onespan.com/products/fido](https://www.onespan.com/products/fido) |
| IDmelon 技术 Inc。 | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) | 

> [!NOTE]
> 如果你购买并计划使用基于 NFC 的安全密钥，则需要为安全密钥提供支持的 NFC 读卡器。 NFC 读卡器不是 Azure 要求或限制。 有关支持的 NFC 读卡器的列表，请与供应商联系以获取基于 NFC 的安全密钥。

如果你是供应商，并且想要在此支持的设备列表上获取设备，请联系 [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com) 。

若要开始 FIDO2 安全密钥，请完成以下操作方法：

> [!div class="nextstepaction"]
> [启用使用 FIDO2 安全密钥的无密码签名](howto-authentication-passwordless-security-key.md)


## <a name="what-scenarios-work-with-the-preview"></a>使用预览版的情况如何？

Azure AD 无密码登录功能当前以预览版提供。 请注意以下事项：

- 管理员可以为其租户启用无密码 authentication 方法
- 对于每个方法，管理员可面向所有用户或选择其租户中的用户/组
- 最终用户可以在其帐户门户中注册和管理这些无密码 authentication 方法
- 最终用户可以用这些无密码身份验证方法登录
   - Microsoft Authenticator 应用：在使用 Azure AD 身份验证的情况下，包括跨所有浏览器、在 Windows 10 全新版 (OOBE) 安装程序中，以及在任何操作系统上集成的移动应用。
   - 安全密钥：在受支持的浏览器（如 Microsoft Edge）中使用针对 Windows 10 和 web 的锁定屏幕 (旧边缘和新边缘) 。

## <a name="choose-a-passwordless-method"></a>选择无密码方法

这三个无密码选项之间的选择取决于公司的安全、平台和应用要求。

下面是在选择 Microsoft 无密码技术时要考虑的一些因素：

||**Windows Hello 企业版**|**无密码 Microsoft Authenticator 应用登录**|**FIDO2 安全密钥**|
|:-|:-|:-|:-|
|**必备组件**| Windows 10 版本 1809 或更高版本<br>Azure Active Directory| Microsoft Authenticator 应用<br>电话 (运行 Android 6.0 或更高版本的 iOS 和 Android 设备。 ) |Windows 10 版本 1809 或更高版本<br>Azure Active Directory|
|**模式**|平台|软件|硬件|
|**系统和设备**|带有内置受信任的平台模块 (TPM) 的 PC<br>PIN 和生物识别识别 |电话上的 PIN 和生物识别识别|FIDO2 兼容 Microsoft 的安全设备|
|**用户体验**|使用 PIN 或生物识别识别登录 (使用 Windows 设备的面部) 、iris 或指纹。<br>Windows Hello 身份验证已绑定到设备;用户需要设备和登录组件（如 PIN 或生物识别因素）来访问公司资源。|使用带有指纹扫描、面部或 iris 识别或 PIN 的移动电话登录。<br>用户从他们的 PC 或手机登录到工作帐户或个人帐户。|使用 FIDO2 security 设备登录 (生物识别、PIN 和 NFC) <br>用户可以基于组织控制和使用设备（如 USB 安全密钥和启用了 NFC 的智能卡、密钥或可穿戴设备）的设备，基于 PIN、生物识别来访问设备。|
|**启用的方案**| Windows 设备的无密码体验。<br>适用于专用于设备和应用程序的单一登录功能的工作 PC。|使用移动电话的无密码的任意位置解决方案。<br>适用于从任何设备访问 web 上的工作或个人应用程序。|使用生物识别、PIN 和 NFC 的辅助角色的无密码体验。<br>适用于共享 Pc，移动电话不是可行的选项 (例如，用于咨询台人员、公共展台或医院团队) |

使用下表选择支持和用户的方法。

|增添|方案|环境|无密码技术|
|:-|:-|:-|:-|
|**管理员**|安全访问设备以执行管理任务|分配的 Windows 10 设备|Windows Hello 企业版和/或 FIDO2 安全密钥|
|**管理员**|非 Windows 设备上的管理任务| 移动或非 windows 设备|无密码 Microsoft Authenticator 应用登录|
|**信息工作者**|工作效率|分配的 Windows 10 设备|Windows Hello 企业版和/或 FIDO2 安全密钥|
|**信息工作者**|工作效率| 移动或非 windows 设备|无密码 Microsoft Authenticator 应用登录|
|**前端工作线程**|工厂、植物、零售或数据输入中的网亭|共享 Windows 10 设备|FIDO2 安全密钥|

## <a name="next-steps"></a>后续步骤

若要开始 Azure AD 中的无密码，请完成以下操作方法之一：

* [启用 FIDO2 安全密钥无密码登录](howto-authentication-passwordless-security-key.md)
* [使用验证器应用启用基于手机的无密码登录](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>外部链接

* [FIDO 联盟](https://fidoalliance.org/)
* [FIDO2 CTAP 规范](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
