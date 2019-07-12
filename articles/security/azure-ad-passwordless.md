---
title: 了解世界而无需使用 Azure Active Directory 的密码 |Microsoft Docs
description: 本指南可帮助 Ceo、 Cio、 Ciso、 首席标识架构师、 企业架构师和安全和 IT 决策制定者负责选择其 Azure Active Directory 实现的无密码身份验证方法。
services: active-directory
keywords: 无密码，azure ad
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 00f66b6010bead3de131095a47ba1e419d2511c0
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723436"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>一个没有与 Azure Active Directory 密码的世界

它是分解密码与你关系的时间。 密码已在过去，我们很好，但在今天的数字工作区它们变得相对简单的攻击媒介的黑客。 黑客喜欢密码并不难看出时考虑的最常被拒绝的密码，Azure Active Directory (Azure AD) 中包括年、 月、 季节等术语或本地 sports 的原因。 此外，[研究表明](https://aka.ms/passwordguidance)与传统建议的密码管理，例如长度要求、 复杂性要求，以及更改频率是多种原因会起反作用相关人类的本性。

有三种类型的攻击通常用于入侵的用户帐户密码喷射网络钓鱼和违反重播。 Azure AD 功能，例如[智能锁定](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)，[阻止的密码](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)，并[密码保护](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)可以帮助保护免受这些类型的攻击。 同样，实现[多重身份验证](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)(MFA) 或双重验证，通过要求第二个形式的身份验证提供额外的安全性。 但在长期来看，无密码的解决方案是确保最安全的身份验证方法的最佳解决方案。

本文是以帮助您了解和实现 Microsoft 的无密码的解决方案和帮助你选择一个或多个以下选项之间之旅的开头：

* **Windows hello 企业版**。 在 Windows 10 中，Windows hello 企业版替换密码在电脑和移动设备上的强双因素身份验证。 这种身份验证包括一种与设备绑定的新型用户凭据，并使用生物识别技术或 PIN。

* **使用 Microsoft Authenticator 无密码登录**。 可以使用 Microsoft Authenticator 应用中登录到 Azure AD 帐户，而无需使用密码。 类似于的 Windows hello 企业版的技术，Microsoft 验证器使用基于密钥的身份验证启用绑定到设备，并使用生物识别或 PIN 的用户凭据。

* **FIDO2 安全密钥**。 FIDO2 提供了在每个网站都是唯一和 Windows Hello 或外部安全密钥等本地设备上存储加密的登录凭据。 这些安全密钥是抵御网页仿冒，密码被盗的风险和重播攻击。 与通过生物识别或 PIN 的用户验证结合使用，解决方法是两个身份验证会议新式安全需求。

## <a name="the-future-of-passwordless-authentication"></a>无密码身份验证的未来

这些天、 银行、 信用卡公司和其他组织和联机服务通常保护你的帐户通过要求您两次身份验证： 通过使用你的密码，然后再次通过电话、 文本或应用一次。 尽管多重身份验证地址正在共享密码的安全问题、 被盗或者猜到，它没有解决尝试记住它们的不便因子。 哪些用户和组织想要在当今的云时代的是高度安全的无密码身份验证方法*和*方便。

![方便 vs 安全](./media/azure-ad/azure-ad-pwdless-image1.png)

Windows hello 企业业务、 无密码登录 Microsoft Authenticator 和向用户提供高度安全且便于使用的身份验证方法的 FIDO2 安全密钥都共享一个简单、 常见体系结构。 所有这三个基于公共/专用密钥技术，需要本地手势等生物识别或 PIN 和私有密钥绑定到单个设备、 安全地存储，并且永远不会共享。

## <a name="windows-hello-for-business"></a>Windows Hello for Business

在 Windows 10 中，Windows hello 企业版替换密码在电脑和设备上的强双因素身份验证。 身份验证包括新类型的绑定到设备，并使用生物识别手势或 PIN，使用户能够进行 Azure AD 身份验证的用户凭据以及在本地 Active Directory。 只需签名到设备使用 Windows Hello for Business 非常简单。 您使用 PIN 或生物识别手势，如指纹或面部识别。

### <a name="windows-hello-for-business-scenarios"></a>Windows hello 企业版方案

Windows hello 企业版是理想的信息工作者使用的是自己指定的 Windows 计算机。 生物识别，并且凭据直接绑定到用户的 PC 上，这样可防止从所有者以外的任何人访问。 与 PKI 集成以及内置的单一登录 (SSO) 支持，Windows hello 企业版提供了简单而方便的方法用于无缝地访问公司资源的本地和云中。

### <a name="windows-hello-for-business-deployment-considerations"></a>Windows hello 企业业务部署注意事项

Windows hello 企业版是一个分布式的系统，使用多个组件来完成设备注册、 预配和身份验证。 因此，部署需要在组织内的多个团队正确地进行规划。 Windows hello 企业版[规划指南](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide)可用于帮助您决策的 Windows Hello 企业部署和需要考虑的选项的类型。

对于在本地或混合部署，应能够：

* 连接良好的工作网络

* Internet 访问权限

* 多重身份验证服务器时要支持 MFA Windows Hello 企业预配

* 正确的名称解析，内部和外部名称

* Active Directory 和足够数量的每个站点的域控制器，以支持身份验证

* Active Directory 证书服务 2012年或更高版本

* 一个或多个工作站计算机运行 Windows 10，版本 1903

自所有类型的 Windows Hello 为业务部署用作企业颁发的证书为域控制器的根的信任，你将想要自动续订过期的证书的[配置自动注册的服务器和用户证书](https://docs.microsoft.com/windows-server/networking/core-network-guide/cncg/server-certs/configure-server-certificate-autoenrollment)。 对于本地部署，标识提供程序是运行 Windows Server Active Directory 联合身份验证服务 (AD FS) 角色的本地服务器。 联合身份验证系统通常需要负载均衡的服务器阵列（称为场）。 此场在内部网络和外围网络拓扑中配置，以便为身份验证请求确保高可用性。

设置组策略，以要求 Windows Hello for Business 中工作区后，您可能想要说明如何使用 Windows hello 企业准备你的组织中的用户。 例如时有人设置新设备时，, 系统会提示之间进行选择**此设备属于我的组织**或**这是我自己的个人设备**。 对于企业设备，他们应选择前者。 用户还需要告知他们应选择哪个连接选项：**加入到 Azure AD**或**设置本地帐户 （更高版本域联接）** 。

很常见的用户习惯于使用生物识别手势进行身份验证每日以最终忘记其 PIN。 若要避免对技术支持的调用，建议你向用户提供可以重置忘记[密码](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment)并[Pin](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset)。

有许多选项，您可以从中选择部署 Windows hello 企业版时。 提供多个选项可确保几乎每个组织可以部署 Windows hello 企业版。 请考虑以下类型的支持的部署：

* [已加入混合 Azure AD 信任部署](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [已加入混合 Azure AD 的证书信任部署](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [Azure AD 加入单一登录部署指南](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [在本地密钥信任部署](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [本地证书信任部署](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

提供许多选项可以显示复杂的部署。 但是，大多数组织可能会确定，它们已实现大部分 Windows Hello for Business 部署所依赖的基础结构。 无论如何，务必了解 Windows hello 企业版是一个分布式的系统，建议在适当的规划。

我们建议您阅读[规划 Windows Hello for Business Deployment](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide)以帮助您确定部署模型最适合您的特定组织。 然后，根据所做的规划，请参阅[Windows Hello for Business 部署指南](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)以帮助确保您的现有环境中成功部署的 Windows hello 企业版。

### <a name="how-windows-hello-for-business-works"></a>Windows Hello 企业版的工作原理

#### <a name="user-sets-up-windows-hello-for-business"></a>用户设置 Windows hello 企业版

完成最初的双重验证的用户在注册过程中之后, Windows Hello 上设置用户的设备和 Windows 要求用户设置一个手势可以是生物识别功能，如指纹或面部识别或 PIN。 完成设置后，用户提供该手势来验证其身份。 Windows 然后使用 Windows Hello 对用户进行身份验证。

根据你的 Windows 10 设备的功能，你将具有内置安全的 enclave，称为硬件受信任的平台模块 (TPM) 或软件 TPM。 TPM 存储私钥，需要在人脸、 指纹或 PIN 来解锁它。 生物识别数据不会漫游并永远不会发送到外部设备或服务器。 没有单个集合点攻击者可以降低窃取生物识别数据，因为 Windows Hello 仅存储生物识别身份验证数据在设备上。

> [!TIP]
> 表面上看，PIN 让人感觉像密码，但实际上更安全。 密码和 PIN 之间的一个重要区别是 PIN 与在其已设置的特定设备。 窃取您的密码的人可以登录到你的帐户从任何位置。 但如果它们会窃取您的 PIN，它们会窃取您的物理设备太 ！ 此外，由于 PIN 是在设备本地，因此它不被传输到任何位置以便其不能在传输中截获或被盗时从服务器。

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>使用 Windows Hello for Business 的单一登录的用户

Windows Hello for Business 生物识别和 Pin 使用非对称 （公共/专用密钥） 进行身份验证的加密。 在身份验证，期间加密被绑定到 TLS 会话密钥保护身份验证过程，以便拦截 (MiTM) 攻击不能生成安全令牌或项目窃取并重播从其他位置。

Windows hello 企业版提供便捷登录体验，到 Azure AD 用户进行身份验证和 Active Directory 资源。 Azure AD 加入设备在登录期间对 Azure 身份验证，并可以根据需要向 Active Directory 进行身份验证。 混合加入 Azure Active Directory 的设备在登录到 Active Directory 身份验证和 Azure Active Directory 在后台进行验证。

![请参阅源映像](./media/azure-ad/azure-ad-pwdless-image2.jpeg)

以下步骤演示了 Azure Active Directory 的单一登录身份验证。

![Windows 10 锁定屏幕](./media/azure-ad/azure-ad-pwdless-image3.png)

1. 用户登录 Windows 使用生物识别或 PIN 笔势。 手势解锁 Windows Hello for Business 私钥，并发送到云身份验证安全支持提供程序，称为云 AP 提供程序。

2. 云 AP 提供程序从 Azure Active Directory 请求 nonce。

3. Azure AD 返回的有效时间为 5 分钟的 nonce。

4. 云 AP 提供程序对 nonce 使用用户的私钥进行签名，并返回到 Azure Active Directory 的有符号的 nonce。

5. Azure Active Directory 验证签名使用 nonce 的签名对用户的安全已注册的公共密钥的 nonce。 后验证签名，然后，Azure AD 验证返回的已签名的 nonce。 在验证之后的 nonce，Azure AD 使用会话密钥，加密为设备的传输密钥，并将其返回给云 AP 提供程序将创建 PRT。

6. 使用会话密钥，接收加密的 PRT 云 AP 提供程序。 使用设备的专用传输密钥，云 AP 提供程序对会话密钥进行解密，并保护使用设备的 TPM 的会话密钥。

7. 云 AP 提供程序将成功进行身份验证响应返回到 Windows 之后用户能够访问 Windows，以及云中和本地应用程序而无需再次进行身份验证 (SSO)。

在其他方案中涉及 Windows hello 企业版身份验证过程的更深入信息，请参阅[Windows Hello for Business 和身份验证](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#Azure-AD-join-authentication-to-Active-Directory-using-a-Key)。

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>用户管理其 Windows Hello for Business 凭据

[Microsoft PIN 重置服务](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset)在 Azure AD，使用户能够根据需要重置其 PIN 中是一项功能。 使用组策略、 Microsoft Intune 或兼容的 MDM，管理员可以配置 Windows 10 设备，以便安全地使用 Microsoft PIN 重置服务，使用户能够通过设置或更高版本在锁定屏幕其忘记了的 PIN 重置而无需重新注册。

有时用户必须回退为使用密码。 [自助服务密码重置](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment)(SSPR) 是另一个 Azure AD 功能，使用户能够重置其密码，而无需与 IT 人员联系。 用户必须先注册，或者进行注册自助服务密码重置使用该服务。 在注册期间，用户可以选择启用其组织的一个或多个身份验证方法。 SSPR，用户可以快速获取未被阻止并继续工作，无论身在何处或一天的时间。 通过允许用户取消阻止自身，你的组织可以减少的非工作时间和最常见的与密码相关问题的较高的支持成本。

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>使用 Microsoft Authenticator 无密码登录

在无密码登录使用 Microsoft Authenticator 是另一种无密码的解决方案，可用于登录到 Azure AD 帐户使用手机登录。 你仍必须通过提供知道的内容和内容，但电话登录可以跳过输入密码并使用指纹、 人脸或 PIN 移动设备上执行所有身份验证来验证你的身份。

### <a name="microsoft-authenticator-passwordless-scenarios"></a>Microsoft Authenticator 无密码方案

Microsoft Authenticator 应用，用户可以验证其身份，并向其工作或个人帐户进行身份验证。 此外使用完善的密码包含一次性密码或推送通知，或完全取代密码的需求。 而不是使用密码，用户确认其身份，然后使用其移动电话通过指纹扫描、 面部或鸢尾花识别或 PIN。 基于类似于什么 Windows hello 企业使用的安全技术，此工具被打包到一个简单的应用程序，使其方便用户在移动设备上。 适用于 Android 和 iOS 的 Microsoft Authenticator 应用。

### <a name="microsoft-authenticator-deployment-considerations"></a>Microsoft Authenticator 部署注意事项

使用 Microsoft Authenticator 应用到 Azure AD 执行无密码登录的先决条件如下所示：

* 最终用户启用 Azure 多重身份验证

* 若要通过使用 Microsoft Intune 或第三方移动设备管理 (MDM) 解决方案中注册其设备的用户的能力

假定满足这些要求，管理员启用无密码的电话登录租户中使用[Windows PowerShell。](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) 在租户中启用手机登录后，最终用户可以选择使用他们的手机上选择其工作或学校帐户登录**帐户**屏幕应用，然后选择**启用手机登录**.

假设管理员已启用无密码登录，最终用户需要满足以下要求：

* 在 Azure 多重身份验证中注册

* 最新版本的 Microsoft Authenticator 已安装在运行 iOS 8.0 或更高版本的设备或 Android 6.0 或更高版本

* 通过向应用添加推送通知的工作或学校帐户

若要避免的潜在的锁定可能超出您的帐户或无需重新创建新的设备上的帐户，建议使用到的 Microsoft Authenticator[备份你的帐户凭据](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-backup-recovery)到云。 备份后，还可以使用该应用在新设备上恢复信息，从而避免潜在的锁定或重新创建帐户。

由于大多数用户习惯只使用密码进行身份验证，务必组织负责讲授用户了解此过程。 感知可以减少用户的任何呼叫技术支持的可能性[问题](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#known-issues)与使用 Microsoft Authenticator 应用登录。

> [!NOTE]
> 潜在的此解决方案的故障点是漫游时用户所在的位置在没有 Internet 连接。 FIDO2 安全密钥和 Windows hello 企业版不收取相同的限制。

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>与 Microsoft Authenticator works 如何无密码登录

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>用户设置了使用 Microsoft Authenticator 无密码登录

Microsoft Authenticator 应用可用作无密码的解决方案中登录到 Azure AD 帐户之前，必须由管理员和最终用户执行的步骤。

首先，管理员将需要[启用的应用程序使用作为凭据](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users)租户使用 Windows PowerShell 中。 管理员还将需要启用 Azure 多重身份验证 (Azure MFA) 的最终用户并配置 Microsoft Authenticator 应用作为之一[验证方法](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings#verification-methods)。

最终用户将需要[下载并安装](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install)Microsoft Authenticator 应用和[设置了其帐户](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app)以使用 Microsoft Authenticator 应用作为验证方法之一。

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>使用 Microsoft 验证器进行无密码登录的用户

使用 Microsoft Authenticator 应用可以登录到任何 Azure AD 帐户，且无需输入密码。 虽然 Windows 10 锁定屏幕不包含 Microsoft Authenticator 应用作为登录选项，用户可以仍然输入其用户名，然后收到其移动设备上的推送通知，以验证存在。 用户通过匹配数在登录屏幕上，然后提供人脸扫描、 指纹或 PIN 来解锁私钥，并完成身份验证来确认其存在。 此多重身份验证方法是比密码更安全且更方便比输入密码和一个代码。

![身份验证器登录](./media/azure-ad/azure-ad-pwdless-image4.png)

使用 Microsoft Authenticator 的无密码身份验证遵循相同的基本模式，如 Windows hello 企业版但是稍微复杂一些因为用户需要先进行标识，因此 Azure AD 可以找到 Microsoft Authenticator 应用版本正在使用。

![身份验证器进程](./media/azure-ad/azure-ad-pwdless-image5.png)

1. 用户输入其用户名。

2. Azure AD 可以检测用户有强凭据并启动强凭据流。

3. 通知将发送到通过 Apple 推送通知服务 (APNS) iOS 设备上或通过 Firebase Cloud Messaging (FCM) 的 Android 设备上的应用。

4. 用户会收到推送通知，打开应用。

5. 应用调用 Azure AD，并接收一个证明存在挑战，而且 nonce。

6. 用户通过输入其生物识别或 PIN 以解锁私钥完成质询。

7. Nonce 是使用私钥进行签名和发送到 Azure AD。

8. Azure AD 执行公共/专用密钥验证，并返回一个令牌。

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>用户管理其无密码登录 Microsoft Authenticator 凭据

与[结合使用注册](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)，用户可以注册并获取 Azure 多重身份验证和自助服务密码重置的优势。 用户注册和管理这些设置，通过导航到其[我的个人资料页](https://aka.ms/mysecurityinfo)。 除了启用 SSPR，结合注册支持使用多个身份验证方法和操作。

## <a name="fido2-security-keys"></a>FIDO2 安全密钥

FIDO2 是 FIDO Alliance 标准的最新版本，具有两个组件-W3C 的 Web 身份验证 (WebAuthN) 标准和相应 FIDO Alliance 身份验证器客户端协议 (CTAP2)。 FIDO2 标准使用户能够利用硬件、 移动和基于生物识别身份验证器来轻松地使用多个应用程序和网站移动和桌面环境中的进行身份验证。

Microsoft 和行业合作伙伴一直在从事一起 FIDO2 Windows hello 企业若要启用共享的设备上轻松且安全的身份验证的安全设备。 FIDO2 安全密钥可用于执行你与你的凭据和安全地进行身份验证[Azure AD](https://aka.ms/azuread418)-已加入的 Windows 10 设备，是你的组织的一部分。

WebAuthN 定义一个 API，使开发和实现强、 web 应用和服务进行无密码身份验证。 CTAP 协议使符合 FIDO 安全密钥以使用 WebAuthN 和作为身份验证器等外部设备。 通过 Web 身份验证，用户可以登录到联机服务使用其人脸、 指纹、 PIN 或可移植 FIDO2 安全密钥，利用强的公共密钥凭据，而不是密码。 当前 WebAuthN 支持 Microsoft Edge 和 Chrome 支持，Firefox 处于开发阶段。

将强身份验证的跨平台解决方案而无需使用密码的设备和遵守 FIDO2、 WebAuthN 和 CTAP 协议的令牌。 Microsoft 合作伙伴正致力于各种安全关键的窗体因素，例如 USB 安全密钥和 nfc 的智能卡。

### <a name="fido2-security-keys-scenarios"></a>FIDO2 安全密钥方案

FIDO2 安全密钥可以用于选择作为在 Windows 10 锁定屏幕上的凭据提供程序的安全密钥登录到 Azure AD。 用户名或密码不需要这样一个理想的解决方案的第一个行工作人员在多个用户共享 Pc。 当公司策略规定，用户的凭据必须是物理上独立于其设备时，它们也是一个很好的身份验证选项。 用户还可以选择在 Windows 10 1809年或更高版本上使用 Microsoft Edge 浏览器内的其 FIDO2 安全密钥登录到网站。

### <a name="fido2-security-keys-deployment-considerations"></a>FIDO2 安全密钥部署注意事项

管理员可以启用 Azure AD 中的 FIDO2 支持，并将功能分配给用户或组。 策略，也可以创建的密钥如何预配和配置诸如允许或阻止一组特定的硬件安全密钥的限制。 密钥必须以物理方式分发给最终用户。

**启用无密码的要求登录到 Azure AD 和使用 FIDO2 安全密钥的 web 站点如下：**

* Azure AD

* Azure 多重身份验证

* 结合使用注册预览版

* FIDO2 安全密钥预览版需要兼容的 FIDO2 安全密钥

* Web 身份验证 (WebAuthN) 需要 Windows 10 1809年或更高版本上的 Microsoft Edge

* 在基于的 Windows 符号则需要 Azure AD 的 FIDO2 已加入 Windows 10 1809年或更高版本 （Windows 10 1903年或更高版本为获得最佳体验）

FIDO2 符合窗体因素包括 USB、 NFC 和和蓝牙设备。 我们建议选择某些平台以来满足特定需求，外形因素和浏览器尚不可 FIDO2 兼容。

我们还建议每个组织创建一种协议的用户和管理员可以按照应安全密钥丢失或被盗。 用户应报告的丢失或被盗的密钥，使管理员或用户可以从用户的配置文件中删除其安全密钥和预配一个新。

### <a name="how-fido2-security-keys-works"></a>FIDO2 安全密钥的工作原理

#### <a name="user-sets-up-fido2-security-key"></a>用户设置 FIDO2 安全密钥

虽然管理员可以[手动预配密钥](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable)并将它们分发给最终用户，将通过支持预配和启用 Windows 10 锁定屏幕上的 FIDO2 凭据提供程序[Intune](https://docs.microsoft.com/intune/windows-enrollment-methods). 管理员还将需要使用[Azure 门户](https://portal.azure.com/)启用硬件令牌设备作为无密码身份验证方法。

部署 FIDO2 安全密钥还要求用户注册使用其密钥[结合使用注册](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)。 使用组合的注册，用户一次注册并获取 Azure 多重身份验证和单一登录密码重置 (SSPR) 的优势。

除了选择硬件令牌用作默认多重身份验证方法，建议你还选择其他验证选项。

* Microsoft Authenticator-通知

* 验证器应用程序或硬件令牌-代码

* 电话呼叫

* 短信

#### <a name="user-using-fido2-security-key-for-sign-in"></a>使用 FIDO2 安全密钥以进行登录的用户

FIDO2 提供了作为验证器和公共/专用密钥加密用于启用内置平台验证器等 Windows Hello 和 security 注册表项，以解析为专用密钥，并提供公钥的窗体因素之间的抽象层可用作标识符访问外部资源。 FIDO2 安全密钥配备了自己内置的安全 enclave 以便存储私钥，并需要生物识别或 PIN 来解锁它。 凭据不能重复使用、 重播，或在服务之间共享，不受约束网络钓鱼网站和 MiTM 攻击或服务器漏洞。

![FIDO2 登录](./media/azure-ad/azure-ad-pwdless-image6.png)

FIDO2 安全密钥提供安全的身份验证，独立于外形规格。 安全密钥保存凭据，并且应使用的另一第二个因素 （集成到安全密钥） 的指纹或 Windows 登录时输入 PIN 等。 Microsoft 合作伙伴正致力于各种安全关键的外观造型。 一些示例包括 USB 安全密钥和 NFC 已启用智能卡。

> [!NOTE]
> 安全密钥必须实现某些功能和扩展 FIDO2 CTAP 协议是从[兼容 Microsoft](https://aka.ms/fido2securitykeys)。 Microsoft 已测试这些解决方案与 Windows 10 和 Azure Active Directory 的兼容性。

![FIDO2 登录过程](./media/azure-ad/azure-ad-pwdless-image9.png)

1. 用户将 FIDO2 设备插入计算机。

2. Windows 检测到 FIDO2 安全密钥。

3. Windows 将发送的身份验证请求。

4. Azure AD 发送回 nonce。

5. 在用户完成其手势来解锁 FIDO2 安全密钥的安全 enclave 中存储的专用密钥。

6. FIDO2 安全密钥对 nonce 使用私钥进行签名。

7. 具有已签名的 nonce PRT 令牌请求发送到 Azure AD。

8. Azure AD 验证签名使用 FIDO2 公共密钥的 nonce。

9. Azure AD 返回 PRT 若要启用的本地资源的访问权限。

#### <a name="user-manages-their-fido2-security-key-credentials"></a>用户管理其 FIDO2 安全密钥凭据

类似于 Microsoft Authenticator 应用，FIDO2 安全密钥的凭据管理依赖于最终用户的组合的注册体验。

## <a name="deciding-a-passwordless-method"></a>决定无密码的方法

无密码这三个选项之间进行选择取决于你公司的安全、 平台和应用程序要求。

下面是为您选择 Microsoft 无密码技术时要考虑一些因素：

||**Windows Hello for Business**|**无密码登录 Microsoft Authenticator 应用**|**FIDO2 安全密钥**|
|:-|:-|:-|:-|
|**系统必备组件**| Windows 10，版本 1809 或更高版本<br>Azure Active Directory| Microsoft Authenticator 应用<br>电话 (iOS 和 Android 设备运行 Android 6.0 或更高版本。)|Windows 10，版本 1809 或更高版本<br>Azure Active Directory|
|**模式**|平台|软件|硬件|
|**系统和设备**|PC 与内置的受信任的平台模块 (TPM)<br>PIN 和生物识别 |在手机上的 PIN 和生物识别|Microsoft 兼容的 FIDO2 安全设备|
|**用户体验**|使用与 Windows 设备的 PIN 或生物特征识别 （面部、 鸢尾花或指纹） 登录。<br>Windows hello 企业身份验证被绑定到设备;用户需要在设备和登录组件如 PIN 或生物识别身份访问公司资源。|使用移动电话具有指纹扫描、 面部或鸢尾花识别登录或将其固定。<br>用户登录到工作或个人帐户从其 PC 或移动电话。|使用 FIDO2 安全设备 （生物识别、 PIN 和 NFC） 登录<br>用户可以访问基于组织控制的设备，并根据的 PIN、 生物识别使用 USB 安全密钥和 nfc 的智能卡、 密钥，也可穿戴设备等设备进行身份验证。|
|**已启用的方案**| 使用 Windows 设备无密码的体验。<br>适用于专用工作 PC 上单一登录功能与设备和应用程序。|无密码的任意位置使用移动电话的解决方案。<br>适用于从任何设备访问工作或个人应用程序在 web 上找到。|对于辅助角色使用生物识别、 PIN 和 NFC 无密码的体验。<br>适用于共享的 Pc 和移动电话不可行 （例如技术支持人员、 公用网亭或医院团队）|

使用下表选择哪种方法将支持你的要求和用户。

|角色|应用场景|环境|无密码的技术|
|:-|:-|:-|:-|
|**管理员**|安全访问管理任务的设备|分配的 Windows 10 设备|Windows hello 企业业务和/或 FIDO2 安全密钥|
|**管理员**|在非 Windows 设备上的管理任务| 移动版或非 windows 设备|无密码登录 Microsoft Authenticator 应用|
|**信息工作者**|工作效率工作|分配的 Windows 10 设备|Windows hello 企业业务和/或 FIDO2 安全密钥|
|**信息工作者**|工作效率工作| 移动版或非 windows 设备|无密码登录 Microsoft Authenticator 应用|
|**前端防线来辅助角色**|工厂、 工厂、 零售版或数据条目中的网亭|共享的 Windows 10 设备|FIDO2 安全密钥|

## <a name="getting-started"></a>入门

无密码身份验证是未来和更安全的环境的路径的批。 建议组织开始规划此更改并减少对密码及其依赖项。 若要开始，请考虑以下目标：

* 用户启用 MFA + Microsoft Authenticator 应用条件性访问。

* 推出 Azure AD 密码保护 + 更新策略。

* 用户启用 SSPR 进行组合的注册。

* 将 Microsoft Authenticator 应用部署的移动性。

* 部署 Windows hello 企业版 (1903年： 了解最新功能)。

* 部署 FIDO2 设备不能使用手机的用户。

* 如果可能，请禁用基于密码的身份验证。

若要实现这些目标，我们建议以下方法：

1. 启用 Azure Active Directory 来充分利用的功能，例如 Azure MFA 和条件性访问。

2. 启用多重身份验证提供额外的保护。

3. 启用自助密码重置用户需要回退到使用密码的事件中。

4. Microsoft Authenticator 手机登录添加了移动性的部署。

5. 部署 Windows hello 企业版到所有 Windows 10 设备。

6. 准备 FIDO2 安全密钥。

> [!NOTE]
> 请参阅 Azure Active Directory[授权页](https://azure.microsoft.com/pricing/details/active-directory/)有关无密码的方法的许可要求的详细信息。

## <a name="conclusion"></a>结束语

过去几年，Microsoft 一直向无密码的世界，使其做出承诺。 Windows 10 中，Microsoft 引入了 Windows hello 企业版，强，硬件受到保护，使单一登录到 Azure Active Directory 和 Active Directory 的双重凭据。 类似于的 Windows hello 企业版的技术，Microsoft Authenticator 应用使用基于密钥的身份验证启用绑定到移动设备，并使用生物识别或 PIN 的用户凭据。 现在 FIDO2 安全密钥，可携带你与你的凭据，并选择安全密钥作为凭据提供程序在 Windows 10 锁定屏幕上登录到 Azure AD。 所有这三个这些无密码解决方案降低网页仿冒，密码喷射的风险和重播攻击，并向用户提供高度安全和方便地登录并从任何位置访问数据。

采用现代的多重身份验证技术，如生物尺度和公钥加密中广泛访问的设备是有意义的方式可以降低公司的标识风险的影响最大步骤之一。 将无密码是安全的身份验证，长期方法，它仍在发展。 考虑新兴的要求，组织可以准备本身进行计划，以开始将移动到无密码的技术。

## <a name="next-steps"></a>后续步骤

* 概述[什么是无密码？](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)
* [如何启用 Azure AD 中无密码](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable)
