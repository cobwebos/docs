---
title: "通过 Microsoft Passport 在不使用密码的情况下进行身份验证 | Microsoft 文档"
description: "概述 Microsoft Passport 并提供有关部署 Microsoft Passport 的其他信息。"
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
tags: azure-classic-portal
ms.assetid: f907bb90-8776-46ca-9e12-279949af66ff
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: befad7c8b40792d93ddef44a8ce0c8deb4dfe8e4


---
# <a name="authenticating-identities-without-passwords-through-microsoft-passport"></a>通过 Microsoft Passport 在不使用密码的情况下进行身份验证
单纯使用密码进行身份验证的传统方法不足以保障用户的安全。 使用这种方法时，用户需重复使用密码，有时还会忘记密码。 密码可能会泄露、被骗取、易于破解且可猜出。 密码也很难记住，并且易于遭受攻击，例如“[传递哈希](https://technet.microsoft.com/dn785092.aspx)”。

## <a name="about-microsoft-passport"></a>关于 Microsoft Passport
对于组织和消费者而言，Microsoft Passport 是基于私钥/公钥或证书的身份验证方法，它比密码身份验证更有优势。 这种形式的身份验证依赖于密钥对凭据，其可取代密码且能抵御漏洞、窃取及钓鱼。

 Microsoft Passport 让用户能够向 Microsoft 帐户、Windows Server Active Directory 帐户、Microsoft Azure Active Directory (Azure AD) 帐户或支持 Fast IDentity Online (FIDO) 身份验证的非 Microsoft 服务进行身份验证。 在 Microsoft Passport 注册期间完成最初的双重验证之后，将在用户的设备上设置 Microsoft Passport，并且该用户需设置一个手势（可能是 Windows Hello 或 PIN）。 用户会提供该手势来验证其身份。 Windows 接着使用 Microsoft Passport 来对用户进行身份验证，并帮助他们访问受保护的资源和服务。

私钥可以通过“用户手势”单独使用，例如，PIN、生物识别技术或远程设备（例如用户用来登录设备的智能卡）。 此信息会链接到证书或非对称密钥对。 如果设备上装配了受信任平台模块 (TPM) 芯片，此私钥将会通过硬件认证。 私钥永远不会离开设备。

公钥已注册到 Azure Active Directory 和 Windows Server Active Directory（以便在本地使用）。 标识提供者 (IDP) 通过将用户的公钥映射到私钥来验证用户的身份，并通过一次性密码 (OTP)、PhoneFactor 或不同的通知机制来提供登录信息。

## <a name="why-enterprises-should-adopt-microsoft-passport"></a>企业为何应采用 Microsoft Passport
启用 Microsoft Passport 后，企业可通过以下措施使其资源变得更安全：

* 使用硬件首选选项来设置 Microsoft Passport。 这表示将在可用时于 TPM 1.2 或 TPM 2.0 上生成密钥。 无法使用 TPM 时，软件将生成密钥。
* 定义 PIN 的复杂性和长度，以及是否要在组织中启用 Hello 使用情况。
* 配置 Microsoft Passport 可以使用基于证书的信任来支持类似于智能卡的方案。

## <a name="how-microsoft-passport-works"></a>Microsoft Passport 的工作原理
1. 密钥是在硬件上由 TPM 或软件生成的。 许多设备都有内置 TPM 芯片，通过将加密密钥集成到设备来保护硬件。 TPM 1.2 或 TPM 2.0 会生成密钥或者根据生成的密钥创建的证书。
2. TPM 会认证这些与硬件绑定的密钥。
3. 单个解锁手势可对设备解锁。 如果设备已加入域或已加入 Azure AD，则此手势允许访问多个资源。

## <a name="how-the-microsoft-passport-lifecycle-works"></a>Microsoft Passport 生命周期的工作原理
![Microsoft Passport 生命周期](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

上图阐释了私钥/公钥对，以及标识提供者所提供的验证。 下面将详细解释其中的每个步骤：

1. 用户通过多个内置验证方法（手势、物理智能卡、Multi-Factor Authentication）证明其身份，并将此信息发送到 Azure Active Directory 或本地 Active Directory 等标识提供者 (IDP)。
2. 然后，设备将创建密钥、认证密钥、接收此密钥的公开部分、为其附加站点声明、登录并将它发送到 IDP 以注册此密钥。
3. 在注册密钥的公开部分之后，IDP 会质询设备，以使用密钥的私密部分进行签名。
4. 然后，IDP 将执行验证并颁发可让用户和设备访问受保护资源的身份验证令牌。 IDP 可以编写跨平台应用，或者通过 JavaScript/Webcrypto API 使用浏览器支持为其用户创建和使用 Microsoft Passport 凭据。

## <a name="the-deployment-requirements-for-microsoft-passport"></a>Microsoft Passport 的部署要求
### <a name="at-the-enterprise-level"></a>在企业级别
* 企业有一个 Azure 订阅。

### <a name="at-the-user-level"></a>在用户级别
* 用户的计算机运行 Windows 10 专业版或企业版。

有关详细的部署说明，请参阅[在组织中启用 Microsoft Passport for Work](active-directory-azureadjoin-passport-deployment.md)。

## <a name="additional-information"></a>其他信息
* [面向企业的 Windows 10：在工作中使用设备的方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备](active-directory-azureadjoin-user-upgrade.md)
* [了解 Azure AD Join 的使用方案](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connect domain-joined devices to Azure AD for Windows 10 experiences（体验 Windows 10 时将已加入域的设备连接到 Azure AD）](active-directory-azureadjoin-devices-group-policy.md)
* [设置 Azure AD Join](active-directory-azureadjoin-setup.md)




<!--HONumber=Dec16_HO5-->


