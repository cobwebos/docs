---
title: Azure 活动目录无密码登录（预览）
description: 使用 FIDO2 安全密钥或 Microsoft 身份验证器应用了解无密码登录 Azure 活动目录的选项
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07ba38a5d7e8e8a89ba122efb1734c1f13a94d48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332190"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Azure 活动目录的无密码身份验证选项

多重身份验证 （MFA） 是保护组织安全的好方法，但用户通常对必须记住其密码的附加安全层感到沮丧。 无密码身份验证方法更方便，因为密码被删除，并替换为您拥有的东西，以及您知道的东西。

|   | 你拥有的东西 | 你是什么人或知道的 |
| --- | --- | --- |
| 无密码 | Windows 10 设备、电话或安全密钥 | 生物识别或 PIN |

在身份验证方面，每个组织都有不同的需求。 Microsoft 提供了以下三个与 Azure 活动目录 （Azure AD） 集成的无密码身份验证选项：

- Windows Hello for Business
- Microsoft Authenticator 应用
- FIDO2 安全密钥

![身份验证：安全性与便利性](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello for Business

适用于具有自己指定的 Windows PC 的信息工作者，Windows Hello 适合企业。 生物识别和 PIN 直接与用户的 PC 相关联，这可以防止所有者以外的任何人访问。 借助公钥基础结构 （PKI） 集成和对单一登录 （SSO） 的内置支持，Windows Hello For Business 提供了一种方便的方法，用于在本地和云中无缝访问公司资源。

![使用适用于企业的 Windows Hello 用户登录的示例](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

以下步骤显示了登录过程如何与 Azure 活动目录配合使用。

![图表，概述了使用 Windows Hello 企业登录的用户参与的步骤](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. 用户使用生物识别或 PIN 手势登录到 Windows。 该手势解锁了适用于企业的 Windows Hello 私钥，并发送到云身份验证安全支持提供商（称为*云 AP 提供商*）。
1. 云 AP 提供程序从 Azure AD 请求一次 nonce。
1. Azure AD 返回有效期为 5 分钟的 nonce。
1. 云 AP 提供程序使用用户的私钥对 nonce 进行签名，并将签名的 nonce 返回到 Azure AD。
1. Azure AD 使用用户的安全注册公钥对 nonce 签名验证已签名的 nonce。 验证签名后，Azure AD 将验证返回的已签名 nonce。 验证 nonce 后，Azure AD 将创建一个主刷新令牌 （PRT），该令牌的会话密钥已加密到设备的传输密钥，并将其返回到云 AP 提供程序。
1. 云 AP 提供商使用会话密钥接收加密 PRT。 使用设备的专用传输密钥，Cloud AP 提供商会解密会话密钥，并使用设备的可信平台模块 （TPM） 保护会话密钥。
1. 云 AP 提供程序向 Windows 返回成功的身份验证响应。 然后，用户能够访问 Windows 以及云和本地应用程序，而无需再次进行身份验证 （SSO）。

"适合企业 Windows [Hello"计划指南](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide)可用于帮助您就适用于企业部署的 Windows Hello 类型和需要考虑的选项做出决策。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 应用

允许员工的电话成为无密码身份验证方法。 除了密码之外，您可能已经将 Microsoft 身份验证器应用用作方便的多重身份验证选项。 您还可以使用身份验证器应用作为无密码选项。

![使用 Microsoft 身份验证器应用登录到 Microsoft 边缘](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

身份验证器应用程序将任何 iOS 或 Android 手机转换为强大的无密码凭据。 用户可以登录到任何平台或浏览器，通过收到通知到他们的手机，匹配屏幕上显示的号码到他们的手机，然后使用他们的生物识别（触摸或面部）或PIN确认。 有关安装详细信息[，请参阅下载并安装 Microsoft 身份验证器应用](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install)。

使用身份验证器应用的无密码身份验证遵循与适用于企业的 Windows Hello 相同的基本模式。 由于需要识别用户，以便 Azure AD 可以找到正在使用的 Microsoft 身份验证器应用版本，因此它稍微复杂一些：

![概述使用 Microsoft 身份验证器应用的用户登录所涉及的步骤的图表](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. 用户输入其用户名。
1. Azure AD 检测用户具有强凭据并启动强凭据流。
1. 通知通过 iOS 设备上的 Apple 推送通知服务 （APNS） 或通过 Android 设备上的 Firebase 云消息 （FCM） 发送到应用。
1. 用户收到推送通知并打开应用。
1. 该应用程序调用 Azure AD 并接收存在验证质询和 nonce。
1. 用户通过输入其生物识别或 PIN 来解锁私钥来完成挑战。
1. nonce 使用私钥签名并发送回 Azure AD。
1. Azure AD 执行公钥/私钥验证并返回令牌。

## <a name="fido2-security-keys"></a>FIDO2 安全密钥

FIDO2 安全密钥是一种不可仿冒标准的无密码身份验证方法，可以采用任何形式因素。 快速在线身份 （FIDO） 是无密码身份验证的开放标准。 FIDO 允许用户和组织使用内置于设备中的外部安全密钥或平台密钥，利用标准登录其资源，而无需使用用户名或密码。

对于公共预览，员工可以使用安全密钥登录到其 Azure AD 或混合 Azure AD 加入的 Windows 10 设备，并单签名到其云和本地资源。 用户还可以登录到受支持的浏览器。 对于对安全性非常敏感或方案或员工不愿意或无法使用手机作为第二个因素的企业来说，FIDO2 安全密钥是一个不错的选择。

![使用安全密钥登录到 Microsoft 边缘](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

当用户使用 FIDO2 安全密钥登录时，将使用以下过程：

![使用 FIDO2 安全密钥概述用户登录所涉及的步骤的图表](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. 用户将 FIDO2 安全密钥插入其计算机。
2. Windows 检测到 FIDO2 安全密钥。
3. Windows 发送身份验证请求。
4. Azure AD 会发送回一个 nonce。
5. 用户完成其手势以解锁存储在 FIDO2 安全密钥安全安全区中的私钥。
6. FIDO2 安全密钥使用私钥标记 nonce。
7. 主刷新令牌 （PRT） 令牌请求与已签名的 nonce 将发送到 Azure AD。
8. Azure AD 使用 FIDO2 公钥验证已签名的 nonce。
9. Azure AD 返回 PRT 以启用对本地资源的访问。

虽然有许多密钥由 FIDO 联盟认证 FIDO2，但 Microsoft 需要供应商实施 FIDO2 客户端到身份验证器协议 （CTAP） 规范的一些可选扩展，以确保最大的安全性和最佳经验。

安全密钥**必须**实现 FIDO2 CTAP 协议中的以下功能和扩展，才能与 Microsoft 兼容：

| # | 功能/扩展信任 | 为什么需要此功能或扩展？ |
| --- | --- | --- |
| 1 | 驻留密钥 | 此功能使安全密钥可移植，其中凭据存储在安全密钥上。 |
| 2 | 客户端引脚 | 此功能使您能够使用第二个因素保护凭据，并应用于没有用户界面的安全密钥。 |
| 3 | 赫马克-秘密 | 此扩展可确保您可以在设备处于线路关闭或飞行模式时登录。 |
| 4 | 每个 RP 的多个帐户 | 此功能可确保可以在多个服务（如 Microsoft 帐户和 Azure 活动目录）中使用相同的安全密钥。 |

以下提供程序提供已知与无密码体验兼容的不同外形规格的 FIDO2 安全密钥。 我们鼓励您通过联系供应商和 FIDO 联盟来评估这些密钥的安全属性。

| 提供程序 | 联系人 |
| --- | --- |
| 尤比科 | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| 飞天 | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| 恩贞 | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| 奥森趋势 | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| 金雅拓（泰莱斯集团） | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| OneSpan公司 | [https://www.onespan.com/sites/default/files/2019-08/Digipass-SecureClick_datasheet.pdf](https://www.onespan.com/sites/default/files/2019-08/Digipass-SecureClick_datasheet.pdf) |
| 伊德梅隆科技公司 | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) | 

> [!NOTE]
> 如果您购买并计划使用基于 NFC 的安全密钥，则需要支持的安全密钥 NFC 读取器。 NFC 读取器不是 Azure 要求或限制。 有关支持的 NFC 读取器列表，请与供应商联系以获取基于 NFC 的安全密钥。

如果您是供应商，并希望将设备放在受支持设备列表中，请联系[Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com)。

## <a name="what-scenarios-work-with-the-preview"></a>哪些方案与预览一起使用？

- 管理员可以为租户启用无密码身份验证方法
- 管理员可以针对所有用户或选择其租户中的每个方法的用户/组
- 最终用户可以在其帐户门户中注册和管理这些无密码身份验证方法
- 最终用户可以使用这些无密码身份验证方法登录
   - Microsoft 身份验证器应用：适用于使用 Azure AD 身份验证的方案，包括所有浏览器、Windows 10 开箱即用 （OOBE） 设置期间以及任何操作系统上的集成移动应用。
   - 安全密钥：在 Microsoft 边缘（旧版和全新边缘）等受支持的浏览器中，在 Windows 10 和 Web 的锁屏界面上工作。

## <a name="choose-a-passwordless-method"></a>选择无密码方法

这三个无密码选项之间的选择取决于公司的安全性、平台和应用要求。

在选择 Microsoft 无密码技术时，需要考虑以下一些因素：

||**适合商务的窗口**|**使用 Microsoft 身份验证器应用进行无密码登录**|**FIDO2 安全密钥**|
|:-|:-|:-|:-|
|**先决条件**| Windows 10 版本 1809 或更高版本<br>Azure Active Directory| Microsoft Authenticator 应用<br>手机（iOS 和运行 Android 6.0 或以上 Android 的设备。|Windows 10 版本 1809 或更高版本<br>Azure Active Directory|
|**模式**|Platform|软件|硬件|
|**系统和设备**|具有内置可信平台模块 （TPM） 的 PC<br>PIN 和生物识别识别 |手机上的 PIN 和生物识别识别|兼容微软的 FIDO2 安全设备|
|**用户体验**|使用 PIN 或生物识别（面部、虹膜或指纹）与 Windows 设备登录。<br>Windows Hello 身份验证绑定到设备;因此，Windows Hello 身份验证与设备相关联。用户需要设备和登录组件（如 PIN 或生物识别因子）来访问公司资源。|使用带有指纹扫描、面部或虹膜识别或 PIN 的手机登录。<br>用户通过电脑或手机登录工作或个人帐户。|使用 FIDO2 安全设备（生物识别、PIN 和 NFC）登录<br>用户可以基于组织控制访问设备，并根据 PIN、生物识别技术（如 USB 安全密钥和支持 NFC 的智能卡、密钥或可穿戴设备）进行身份验证。|
|**已启用的方案**| Windows 设备的无密码体验。<br>适用于专用工作 PC，能够单次登录设备和应用程序。|使用手机的任何地方无密码解决方案。<br>适用于从任何设备访问 Web 上的工作或个人应用程序。|使用生物识别、PIN 和 NFC 的员工无密码体验。<br>适用于共享 PC 和移动电话不可行的选项（例如，对于服务台人员、公共信息亭或医院团队）|

使用下表选择哪种方法将支持您的要求和用户。

|角色|方案|环境|无密码技术|
|:-|:-|:-|:-|
|**管理**|安全访问设备以执行管理任务|分配的 Windows 10 设备|适用于企业和/或 FIDO2 安全密钥的 Windows 您好|
|**管理**|非 Windows 设备上的管理任务| 移动或非窗口设备|使用 Microsoft 身份验证器应用进行无密码登录|
|**信息工作者**|生产力工作|分配的 Windows 10 设备|适用于企业和/或 FIDO2 安全密钥的 Windows 您好|
|**信息工作者**|生产力工作| 移动或非窗口设备|使用 Microsoft 身份验证器应用进行无密码登录|
|**前线工人**|工厂、工厂、零售或数据输入中的展台|共享 Windows 10 设备|FIDO2 安全密钥|

## <a name="next-steps"></a>后续步骤

[在组织中启用 FIDO2 安全密钥无密码选项](howto-authentication-passwordless-security-key.md)

[在组织中启用基于电话的无密码选项](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>外部链接

[FIDO 联盟](https://fidoalliance.org/)

[FIDO2 CTAP 规范](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
