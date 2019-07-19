---
title: 了解没有密码的世界 Azure Active Directory |Microsoft Docs
description: 本指南可帮助首席身份验证人员、Cio、Ciso、首席标识架构师、企业架构师以及负责为其 Azure Active Directory 实现选择无密码 authentication 方法的安全和 IT 决策制定者。
services: active-directory
keywords: 无密码、azuread
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: c1f1fc4a09cba469edfea060afea47053cb87ac4
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302182"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>没有密码的世界 Azure Active Directory

这就是用密码分解关系的时候了。 过去, 密码是很好的, 但在今天的数字工作区中, 他们就成为了黑客的一个相对简单的攻击媒介。 黑客喜欢密码, 如果您认为 Azure Active Directory (Azure AD) 中经常拒绝的密码包括年份、月份、季节或本地体育团队等术语, 则很难看出为什么会出现这种情况。 而且,[研究](https://aka.ms/passwordguidance)表明, 针对密码管理的传统建议 (例如, 长度要求、复杂性要求和更改频率) 出于各种与人力性质相关的原因而适得其反。

通常用于泄露用户帐户的三种类型的攻击包括密码喷涂、网络钓鱼和破坏性重播。 Azure AD 功能 (如[智能锁定](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)、[禁止密码](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)和[密码保护](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)) 可帮助防范这类攻击。 同样, 实现[多重身份验证](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)(MFA) 或双重验证, 通过要求第二种形式的身份验证提供额外的安全性。 但在长时间运行中, 无密码解决方案是确保最安全的身份验证方法的最佳解决方案。

本文旨在帮助你了解和实现 Microsoft 的无密码解决方案, 并帮助你在以下一个或多个选项之间进行选择:

* **Windows Hello 企业版**。 在 Windows 10 中, Windows Hello 企业版将密码替换为 Pc 和移动设备上的强双重身份验证。 这种身份验证包括一种与设备绑定的新型用户凭据，并使用生物识别技术或 PIN。

* **无密码登录 Microsoft Authenticator**。 Microsoft Authenticator 应用可用于登录到 Azure AD 帐户, 而无需使用密码。 类似于 Windows Hello 企业版, Microsoft Authenticator 使用基于密钥的身份验证来启用绑定到设备并使用生物识别或 PIN 的用户凭据。

* **FIDO2 安全密钥**。 FIDO2 提供在每个网站之间唯一的加密登录凭据, 并将其存储在本地设备上, 如 Windows Hello 或外部安全密钥。 这些安全密钥可以抵御网络钓鱼、密码盗用和重播攻击的风险。 解决方案与通过生物识别或 PIN 的用户验证相结合, 解决方案是满足新式安全需求的双重验证。

## <a name="the-future-of-passwordless-authentication"></a>无密码 authentication 的未来

这些日子、银行、信用卡公司以及其他组织和联机服务通常要求您验证您的身份是否两次: 一次通过使用您的密码来保护您的帐户, 然后通过电话、文本或应用程序再次使用。 尽管多重身份验证解决了共享、被盗或被猜到的密码的安全问题, 但并不能解决尝试记住这些密码的不便因素。 目前的云端时代, 用户和组织所需的无密码身份验证方法是高度安全*和*方便的。

![方便性 vs security](./media/azure-ad/azure-ad-pwdless-image1.png)

Windows Hello 企业版, 使用 Microsoft Authenticator 和 FIDO2 安全密钥的无密码登录都共享一个简单的通用体系结构, 该体系结构为用户提供了一种高度安全且易于使用的身份验证方法。 这三种技术都基于公共/私有-密钥技术, 需要本地手势 (如生物识别或 PIN) 以及绑定到单一设备并安全存储和永不共享的私钥。

## <a name="windows-hello-for-business"></a>Windows Hello for Business

在 Windows 10 中, Windows Hello 企业版将密码替换为 Pc 和设备上的强双重身份验证。 身份验证包含一种新类型的用户凭据, 该凭据绑定到设备并使用生物识别手势或 PIN, 使用户能够对 Azure AD 以及本地 Active Directory 进行身份验证。 只需使用 Windows Hello 企业版登录到设备很简单。 你可以使用 PIN 或生物识别手势, 如指纹或面部识别。

### <a name="windows-hello-for-business-scenarios"></a>Windows Hello 企业版方案

Windows Hello 企业版非常适合拥有自己的指定 Windows PC 的信息工作者。 生物识别和凭据直接绑定到用户的 PC, 这会阻止除所有者之外的任何人访问。 借助 PKI 集成和对单一登录 (SSO) 的内置支持, Windows Hello 企业版提供了一种简单而方便的方法, 可无缝访问本地和云中的公司资源。

### <a name="windows-hello-for-business-deployment-considerations"></a>Windows Hello 企业版部署注意事项

Windows Hello 企业版是一种分布式系统, 使用多个组件完成设备注册、预配和身份验证。 因此, 部署需要在组织内的多个团队中进行适当的计划。 Windows Hello 企业版[规划指南](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide)可用于帮助您决定 Windows Hello 企业版部署的类型以及需要考虑的选项。

部署 Windows Hello 企业版时, 有许多选项可供选择。 提供多个选项可确保几乎每个组织都能部署 Windows Hello 企业版。 请考虑以下受支持的部署类型:

* [混合 Azure AD 联接的密钥信任部署](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [混合 Azure AD 联接的证书信任部署](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [Azure AD 联接单一登录部署指南](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [本地密钥信任部署](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [本地证书信任部署](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

提供很多选项会使部署显得非常复杂。 但是, 大多数组织可能会认为他们已经实现了 Windows Hello 企业版部署所依赖的大多数基础结构。 无论如何, 了解 Windows Hello 企业版是一种分布式系统并推荐适当的规划, 这一点很重要。

建议你阅读[规划 Windows Hello 企业版部署](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide), 以帮助你确定最适合你的特定组织的部署模型。 然后, 请参阅[Windows Hello 企业版部署指南](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide), 以帮助确保在现有环境中成功部署 Windows Hello 企业版。

### <a name="how-windows-hello-for-business-works"></a>Windows Hello 企业版的工作原理

#### <a name="user-sets-up-windows-hello-for-business"></a>用户设置 Windows Hello 企业版

在注册期间对用户进行初始双重验证后, 将在用户的设备上设置 Windows Hello, Windows 会要求用户设置手势, 这可以是一个生物识别, 如指纹、面部识别或 PIN。 设置后, 用户将提供笔势来验证其身份。 然后, windows 使用 Windows Hello 对用户进行身份验证。

根据你的 Windows 10 设备的功能, 你可以使用内置的安全 enclave (称为硬件受信任的平台模块 (TPM) 或软件 TPM)。 TPM 存储私钥, 此密钥需要你的人脸、指纹或 PIN 才能解锁。 生物识别数据不会漫游, 并且永远不会发送到外部设备或服务器。 由于 Windows Hello 仅在设备上存储生物识别数据, 因此攻击者可能不会泄露单一收集点来窃取生物识别数据。

> [!TIP]
> 在表面上, PIN 感觉像是密码, 但实际上更安全。 密码和 PIN 之间的一个重要区别在于, PIN 绑定到设置它的特定设备。 盗取你的密码的人可以从任何位置登录你的帐户。 但如果他们盗取你的 PIN, 他们也必须窃取你的物理设备! 此外, 由于 PIN 在设备的本地, 因此不会在任何位置传输, 因此无法在传输或从服务器中截取。

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>用户使用 Windows Hello 企业版登录

Windows Hello 企业版生物识别和 Pin 使用非对称 (公钥/私钥) 加密进行身份验证。 在身份验证过程中, 加密与 TLS 会话密钥相关联, 这将保护身份验证过程, 使中间人 (MiTM) 攻击无法盗取生成的安全令牌或项目并从其他位置重播。

Windows Hello 企业版提供了一种方便的登录体验, 用于对用户进行身份验证, 以便 Azure AD 和 Active Directory 资源。 在登录过程中, Azure AD 联接的设备将在 Azure 中进行身份验证, 并可以选择对 Active Directory 进行身份验证。 混合 Azure Active Directory 联接设备在登录期间进行 Active Directory 身份验证, 并在后台 Azure Active Directory 进行身份验证。

![查看源图像](./media/azure-ad/azure-ad-pwdless-image2.jpeg)

以下步骤说明 Azure Active Directory 的登录身份验证。

![Windows 10 锁定屏幕](./media/azure-ad/azure-ad-pwdless-image3.png)

1. 用户使用生物识别或 PIN 手势登录 Windows。 手势会解除对 Windows Hello 企业版私钥的锁定, 并发送到云身份验证安全支持提供程序 (称为云 AP 提供程序)。

2. 云 AP 提供商请求 Azure Active Directory 的 nonce。

3. Azure AD 返回有效时间为5分钟的 nonce。

4. 云 AP 提供程序使用用户的私钥对 nonce 进行签名, 并将签名的 nonce 返回到 Azure Active Directory。

5. Azure Active Directory 使用用户安全注册的公钥对 nonce 签名进行签名。 验证签名后, Azure AD 验证返回的签名 nonce。 验证 nonce 后, Azure AD 创建具有已加密到设备传输密钥的会话密钥的 PRT, 并将其返回给云 AP 提供商。

6. 云 AP 提供程序接收带会话密钥的加密 PRT。 使用设备的专用传输密钥, 云 AP 提供程序会解密会话密钥, 并使用设备的 TPM 来保护会话密钥。

7. 云 AP 提供程序将成功的身份验证响应返回给 Windows, 之后用户便能够访问 Windows 以及云和本地应用程序, 而无需再次进行身份验证 (SSO)。

若要深入了解 Windows Hello 企业版的其他方案, 请参阅[Windows Hello 企业版和身份验证](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#Azure-AD-join-authentication-to-Active-Directory-using-a-Key)。

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>用户管理其 Windows Hello 企业版凭据

[MICROSOFT PIN 重置服务](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset)是 Azure AD 中的一项功能, 使用户能够根据需要重置其 PIN。 使用组策略、Microsoft Intune 或兼容的 MDM, 管理员可以配置 Windows 10 设备以安全地使用 Microsoft PIN 重置服务, 该服务使用户可以通过设置或在锁定屏幕上重置其忘记的 PIN, 而无需重新注册。

有时用户必须回退到使用密码。 [自助服务密码重置](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment)(SSPR) 是另一项 Azure AD 功能, 使用户能够重置其密码, 而无需联系 IT 员工。 在使用服务之前, 用户必须注册以进行自助密码重置。 注册过程中, 用户选择一个或多个由其组织启用的身份验证方法。 SSPR 使用户无论在何处或是当天的时间, 都可以快速地取消阻止并继续工作。 通过允许用户取消阻止其自身, 你的组织可以降低大多数常见密码相关问题的非生产时间和高支持成本。

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>无密码登录 Microsoft Authenticator

使用 Microsoft Authenticator 的无密码登录是另一个无密码解决方案, 可用于使用电话登录登录到 Azure AD 帐户。 你仍必须通过提供你知道的信息和某些东西来验证你的身份, 但手机登录使你可以跳过输入密码, 并使用指纹、面部或 PIN 在移动设备上执行所有身份验证。

### <a name="microsoft-authenticator-passwordless-scenarios"></a>Microsoft Authenticator 无密码方案

通过 Microsoft Authenticator 应用, 用户可以验证其身份并向其工作帐户或个人帐户进行身份验证。 它还可以用于通过一次性密码或推送通知来补充密码, 或者完全替换密码。 用户不使用密码, 而是通过指纹扫描、面部或 iris 识别或 PIN, 使用移动电话确认其身份。 此工具是基于 Windows Hello 使用的安全技术构建的, 它在移动设备上打包为一个简单的应用程序, 使其成为用户的便捷选项。 Microsoft Authenticator 应用可用于 Android 和 iOS。

### <a name="microsoft-authenticator-deployment-considerations"></a>Microsoft Authenticator 部署注意事项

使用 Microsoft Authenticator 应用执行无密码登录到 Azure AD 的先决条件包括:

* 已为最终用户启用 Azure 多重身份验证

* 用户通过使用 Microsoft Intune 或第三方移动设备管理 (MDM) 解决方案来注册其设备的功能

假如满足了这些要求, 管理员就可以使用[Windows PowerShell](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users)在租户中启用无密码手机登录。 在租户中启用手机登录后, 最终用户可以选择使用手机登录, 方法是在应用的**帐户**屏幕上选择其工作或学校帐户, 然后选择 "**启用手机登录**"。

假设管理员已启用无密码登录, 则最终用户需要满足以下要求:

* 已在 Azure 多重身份验证中注册

* 安装在运行 iOS 8.0 或6.0 更高版本或更高版本的设备上的最新版本的 Microsoft Authenticator

* 将推送通知添加到应用的工作或学校帐户

若要避免锁定帐户, 或者必须在新设备上重新创建帐户, 建议使用 Microsoft Authenticator 将[帐户凭据备份](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-backup-recovery)到云中。 备份后，还可以使用该应用在新设备上恢复信息，从而避免潜在的锁定或重新创建帐户。

由于大多数用户习惯于只使用密码进行身份验证, 因此你的组织必须培训有关此过程的用户。 对于使用 Microsoft Authenticator 应用登录相关的任何[问题](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#known-issues), 认知度可能会降低用户呼叫技术支持的可能性。

> [!NOTE]
> 如果漫游用户所在的位置没有 Internet 连接, 则可能是此解决方案的潜在故障点。 FIDO2 安全密钥和 Windows Hello 企业版不受相同限制。

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>无密码登录 Microsoft Authenticator 的工作原理

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>用户通过 Microsoft Authenticator 设置无密码登录

必须由管理员和最终用户执行步骤, 然后才能将 Microsoft Authenticator 应用用作无密码解决方案, 以便登录到 Azure AD 帐户。

首先, 管理员需要在使用 Windows PowerShell 的租户中将[应用作为凭据启用](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users)。 管理员还需要为最终用户启用 Azure 多重身份验证 (Azure MFA), 并将 Microsoft Authenticator 应用程序配置为[验证方法](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings#verification-methods)之一。

最终用户将需要[下载并安装](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install)Microsoft Authenticator 应用, 并[设置其帐户](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app)以使用 Microsoft Authenticator 应用作为验证方法之一。

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>使用 Microsoft Authenticator 无密码登录的用户

使用 Microsoft Authenticator 应用可以登录到任何 Azure AD 帐户，且无需输入密码。 虽然 Windows 10 锁屏版不包含 Microsoft Authenticator 应用作为登录选项, 但用户仍可以输入其用户名, 然后在其移动设备上收到推送通知以验证状态。 用户通过在登录屏幕上匹配某个数字来确认其状态, 然后提供人脸扫描、指纹或 PIN 来解锁私钥并完成身份验证。 此多重验证方法比密码更安全, 而不是输入密码和代码。

![身份验证器登录](./media/azure-ad/azure-ad-pwdless-image4.png)

使用 Microsoft Authenticator 的无密码 authentication 遵循与 Windows Hello for Business 相同的基本模式, 但这有点复杂, 因为用户需要进行标识, 以便 Azure AD 可以查找 Microsoft Authenticator 应用程序版本广泛.

![验证器进程](./media/azure-ad/azure-ad-pwdless-image5.png)

1. 用户输入用户名。

2. Azure AD 检测到用户具有强凭据并启动强凭据流。

3. 通过 iOS 设备上的 Apple Push Notification 服务 (APNS) 或 Android 设备上的 Firebase Cloud 消息 (FCM) 将通知发送到应用。

4. 用户收到推送通知并打开应用。

5. 该应用程序调用 Azure AD, 并收到一项存在证据和 nonce。

6. 用户通过输入生物识别或 PIN 锁定私钥来完成质询。

7. Nonce 用私钥签名并发送回 Azure AD。

8. Azure AD 执行公钥/私钥验证并返回令牌。

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>用户通过 Microsoft Authenticator 凭据管理他们的无密码登录

通过[组合注册](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined), 用户可以注册并获得 Azure 多重身份验证和自助密码重置的好处。 用户通过导航到["我的配置文件" 页](https://aka.ms/mysecurityinfo)来注册和管理这些设置。 除了启用 SSPR, 组合注册还支持多种身份验证方法和操作。

## <a name="fido2-security-keys"></a>FIDO2 安全密钥

FIDO2 是最新版本的 FIDO 联盟标准版, 具有两个组件: W3c Web Authentication (WebAuthN) 标准和相应的 FIDO 联合客户端到身份验证协议 (CTAP2)。 FIDO2 标准使用户能够利用基于硬件、移动和生物识别的验证器在移动和桌面环境中轻松地对许多应用和网站进行身份验证。

Microsoft 和行业合作伙伴已经在 FIDO2 security 设备上协同工作, 以便在共享设备上启用简单安全的身份验证。 通过 FIDO2 安全密钥, 你可以向你携带凭据, 并安全地向组织中已加入[Azure AD](https://aka.ms/azuread418)的 Windows 10 设备进行身份验证。

WebAuthN 定义了一个 API, 该 API 支持通过 web 应用和服务进行强、无密码的身份验证。 CTAP 协议允许与 FIDO 兼容的外部设备使用 WebAuthN, 并将其用作验证器。 利用 Web 身份验证, 用户可以通过其面部、指纹、PIN 或 FIDO2 安全密钥 (而不是密码) 登录到联机服务。 Microsoft Edge 支持当前的 WebAuthN, 并且支持 Chrome 和 Firefox 正在开发中。

符合 FIDO2、WebAuthN 和 CTAP 协议的设备和令牌会引入无需使用密码即可实现强身份验证的跨平台解决方案。 Microsoft 合作伙伴正在开发各种安全关键形式的因素, 例如 USB 安全密钥和启用 NFC 的智能卡。

### <a name="fido2-security-keys-scenarios"></a>FIDO2 安全密钥方案

通过在 Windows 10 锁屏界面上选择安全密钥作为凭据提供程序, 可以使用 FIDO2 安全密钥登录到 Azure AD。 不需要用户名或密码, 这使得它成为在多个用户之间共享电脑的第一行工作人员的理想解决方案。 当公司策略规定用户的凭据必须与设备进行物理隔离时, 它们也是一种出色的身份验证选项。 用户还可以选择使用 Windows 10 1809 版或更高版本上的 Microsoft Edge 浏览器中的 FIDO2 安全密钥登录到网站。

### <a name="fido2-security-keys-deployment-considerations"></a>FIDO2 安全密钥部署注意事项

管理员可以在 Azure AD 中启用 FIDO2 支持, 并将功能分配给用户或组。 还可为密钥的预配方式创建策略, 并配置限制, 例如允许或阻止一组特定的硬件安全密钥。 密钥必须以物理方式分发给最终用户。

**使用 FIDO2 安全密钥启用无密码登录 Azure AD 和网站的要求包括以下各项:**

* Azure AD

* Azure 多重身份验证

* 组合注册预览

* FIDO2 安全密钥预览需要兼容的 FIDO2 安全密钥

* Web 身份验证 (WebAuthN) 需要 Windows 10 版本1809或更高版本上的 Microsoft Edge

* 基于 FIDO2 的 Windows 登录需要 Azure AD 联接 Windows 10 版本1809或更高版本 (Windows 10 版本1903或更高版本上的最佳体验)

与 FIDO2 兼容的外形规格包括 USB、NFC 和蓝牙设备。 建议选择满足特定需求的外形规格, 因为某些平台和浏览器尚不符合 FIDO2。

我们还建议每个组织为用户和管理员创建一个协议, 以确保安全密钥丢失或被盗。 用户应报告丢失或被盗的密钥, 以便管理员或用户可以从用户的配置文件中删除其安全密钥并设置一个新密钥。

### <a name="how-fido2-security-keys-works"></a>FIDO2 安全密钥的工作原理

#### <a name="user-sets-up-fido2-security-key"></a>用户设置 FIDO2 安全密钥

虽然管理员可以[手动预配密钥](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable)并将其分发给最终用户, 但在 Windows 10 锁屏上设置和启用 FIDO2 凭据提供程序将通过[Intune](https://docs.microsoft.com/intune/windows-enrollment-methods)提供支持。 管理员还需要使用[Azure 门户](https://portal.azure.com/)将硬件令牌设备启用为无密码身份验证方法。

部署 FIDO2 安全密钥还要求用户使用[组合注册](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)注册其密钥。 通过组合注册, 用户可以注册一次, 并获得 Azure 多重身份验证和单一登录密码重置 (SSPR) 的好处。

除了选择硬件令牌作为默认多重身份验证方法外, 还建议选择其他验证选项。

* Microsoft Authenticator--通知

* 验证器应用或硬件令牌--代码

* 电话呼叫

* 短信

#### <a name="user-using-fido2-security-key-for-sign-in"></a>用于登录的用户使用 FIDO2 安全密钥

FIDO2 在用作身份验证器和公钥/私钥加密的窗体因数之间提供了一个抽象层, 用于启用内置平台验证器 (如 Windows Hello 和安全密钥) 以解析为私钥并提供公钥可用作访问外部资源的标识符。 FIDO2 安全密钥配备有自己的内置安全 enclave, 用于存储私钥, 并要求生物识别或 PIN 解锁。 凭据不能在服务中重复使用、重播或共享, 并且不受网络钓鱼和 MiTM 攻击或服务器泄露的限制。

![FIDO2 登录](./media/azure-ad/azure-ad-pwdless-image6.png)

FIDO2 安全密钥提供安全的身份验证, 而不考虑其外观。 安全密钥保存凭据, 并应使用额外的第二个因素 (如指纹 (集成到安全密钥) 或要在 Windows 登录时输入的 PIN) 进行保护。 Microsoft 合作伙伴正在开发各种安全关键形式因素。 一些示例包括 USB 安全密钥和启用 NFC 的智能卡。

> [!NOTE]
> 安全密钥必须实现从 FIDO2 CTAP 协议到[Microsoft 兼容](https://aka.ms/fido2securitykeys)的某些功能和扩展。 Microsoft 已测试这些解决方案与 Windows 10 和 Azure Active Directory 的兼容性。

![FIDO2 登录过程](./media/azure-ad/azure-ad-pwdless-image9.png)

1. 用户将 FIDO2 设备插入计算机。

2. Windows 检测 FIDO2 安全密钥。

3. Windows 发送身份验证请求。

4. Azure AD 发回 nonce。

5. 用户完成其笔势以解锁存储在 FIDO2 安全密钥的 secure enclave 中的私钥。

6. FIDO2 安全密钥用私钥对 nonce 进行签名。

7. 带有签名 nonce 的 PRT 令牌请求将发送到 Azure AD。

8. Azure AD 使用 FIDO2 公钥验证签名 nonce。

9. Azure AD 返回 PRT 以启用对本地资源的访问。

#### <a name="user-manages-their-fido2-security-key-credentials"></a>用户管理其 FIDO2 安全密钥凭据

与 Microsoft Authenticator 应用类似, FIDO2 安全密钥的凭据管理依赖于最终用户的组合注册体验。

## <a name="deciding-a-passwordless-method"></a>确定无密码方法

这三个无密码选项之间的选择取决于公司的安全、平台和应用要求。

下面是在选择 Microsoft 密码不足的技术时要考虑的一些因素:

||**Windows Hello for Business**|**无密码 Microsoft Authenticator 应用登录**|**FIDO2 安全密钥**|
|:-|:-|:-|:-|
|**必备组件**| Windows 10 版本1809或更高版本<br>Azure Active Directory| Microsoft Authenticator 应用<br>手机 (运行 Android 6.0 或更高版本的 iOS 和 Android 设备。)|Windows 10 版本1809或更高版本<br>Azure Active Directory|
|**模式**|平台|软件|硬件|
|**系统和设备**|带有内置受信任的平台模块 (TPM) 的 PC<br>PIN 和生物识别识别 |电话上的 PIN 和生物识别识别|FIDO2 兼容 Microsoft 的安全设备|
|**用户体验**|使用 PIN 或生物识别识别 (面部、iris 或指纹) 通过 Windows 设备登录。<br>Windows Hello 身份验证已绑定到设备;用户需要设备和登录组件 (如 PIN 或生物识别因素) 来访问公司资源。|使用带有指纹扫描、面部或 iris 识别或 PIN 的移动电话登录。<br>用户从他们的 PC 或手机登录到工作帐户或个人帐户。|使用 FIDO2 security 设备登录 (生物识别、PIN 和 NFC)<br>用户可以基于组织控制和使用设备 (如 USB 安全密钥和启用了 NFC 的智能卡、密钥或可穿戴设备) 的设备, 基于 PIN、生物识别来访问设备。|
|**启用的方案**| Windows 设备的无密码体验。<br>适用于专用于设备和应用程序的单一登录功能的工作 PC。|使用移动电话的无密码的任意位置解决方案。<br>适用于从任何设备访问 web 上的工作或个人应用程序。|使用生物识别、PIN 和 NFC 的辅助角色的无密码体验。<br>适用于共享 Pc, 并且移动电话不是可行的选项 (如技术支持人员、公共展台或医院团队)|

使用下表选择支持和用户的方法。

|增添|应用场景|环境|无密码技术|
|:-|:-|:-|:-|
|**Admin**|安全访问设备以执行管理任务|分配的 Windows 10 设备|Windows Hello 企业版和/或 FIDO2 安全密钥|
|**Admin**|非 Windows 设备上的管理任务| 移动或非 windows 设备|无密码 Microsoft Authenticator 应用登录|
|**信息工作者**|工作效率|分配的 Windows 10 设备|Windows Hello 企业版和/或 FIDO2 安全密钥|
|**信息工作者**|工作效率| 移动或非 windows 设备|无密码 Microsoft Authenticator 应用登录|
|**前端工作线程**|工厂、植物、零售或数据输入中的网亭|共享 Windows 10 设备|FIDO2 安全密钥|

## <a name="getting-started"></a>入门

无密码 authentication 是未来的浪潮和更安全的环境的路径。 建议组织开始规划此更改并减少其对密码的依赖关系。 若要开始操作, 请考虑以下目标:

* 允许用户使用 MFA + Microsoft Authenticator 应用 + 条件性访问。

* 推出 Azure AD 密码保护 + 更新策略。

* 使用组合注册为 SSPR 启用用户。

* 部署 Microsoft Authenticator 应用以实现移动性。

* 部署 Windows Hello 企业版 (1903: 保持最新)。

* 为不能使用手机的用户部署 FIDO2 设备。

* 如果可能, 禁用基于密码的身份验证。

若要实现这些目标, 建议采用以下方法:

1. 启用 Azure Active Directory 以充分利用 Azure MFA 和条件访问等功能。

2. 启用多重身份验证以提供额外的保护。

3. 在用户需要回退到使用密码的事件中启用自助密码重置。

4. 部署 Microsoft Authenticator 手机登录以便添加移动性。

5. 将 Windows Hello 企业版部署到所有 Windows 10 设备。

6. 准备 FIDO2 安全密钥。

> [!NOTE]
> 有关无密码方法的许可要求的详细信息, 请参阅 Azure Active Directory[许可页](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="conclusion"></a>结束语

在过去几年中, Microsoft 一直致力于启用没有密码的世界。 在 Windows 10 中, Microsoft 引入了 Windows Hello 企业版, 这是一个强大的、受硬件保护的双因素凭据, 支持单一登录 Azure Active Directory 和 Active Directory。 类似于 Windows Hello 企业版, Microsoft Authenticator 应用使用基于密钥的身份验证来启用关联到移动设备并使用生物识别或 PIN 的用户凭据。 现在, 使用 FIDO2 安全密钥, 你可以通过在 Windows 10 锁屏界面上选择安全密钥作为凭据提供程序来携带凭据并登录到 Azure AD。 所有这三种无密码解决方案都可以降低网络钓鱼、密码喷涂和重播攻击的风险, 并为用户提供一种在任何位置登录和访问数据的安全性和便利的方法。

在广泛可访问的设备中采用生物识别和公钥加密等新式多重身份验证技术是一项最有影响力的步骤, 可有意义地降低公司的身份风险。 无密码是一种用于安全身份验证的长期方法, 仍在不断发展。 考虑到新兴要求, 组织可通过制定一套计划开始迁移到无密码技术来做好准备。

## <a name="next-steps"></a>后续步骤

* [无密码](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)概述
* [如何在 Azure AD 中启用无密码](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable)
