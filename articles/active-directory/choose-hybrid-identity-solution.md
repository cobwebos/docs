---
title: "选择 Azure 混合标识解决方案 | Microsoft Docs"
description: "大致了解可用的混合标识解决方案和建议，以便为组织做出最明智的标识监管决策。"
keywords: 
author: jeffgilb
manager: femila
ms.reviewer: jsnow
ms.author: jeffgilb
ms.date: 7/5/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.custom: it-pro
ms.openlocfilehash: 5838e3276765f4f074bca2e3cae81b17edfa7c69
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="microsoft-hybrid-identity-solutions"></a>Microsoft 混合标识解决方案
[Microsoft Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) 混合标识解决方案使你能够将本地目录与 Azure AD 同步，同时仍可在本地管理用户。 如果计划将本地 Windows Server Active Directory 与 Azure AD 进行同步，首先需要决定是使用已同步的标识还是使用联合标识。 通过已同步标识和可选密码哈希，你的用户能够使用相同密码访问本地和基于云的组织资源。 对于更高级的方案要求，如单一登录 (SSO) 或本地 MFA，需要将 Active Directory 联合身份验证服务 (AD FS) 部署到联合标识。 

有多个选项可用于配置混合标识。 本文提供的信息基于易于部署的原则、特定标识和访问管理需求，有助于你选择最适合组织的标识。 当考虑哪个标识模型最适合组织需求时，还需考虑时间、现有基础结构、复杂性和成本。 这些因素对每个组织都不同，并可能随时间变化。 但是，如果需求确实发生更改，你还可灵活切换到不同的标识模型。

> [!TIP]
> 这些解决方案均通过 [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) 提供。

## <a name="synchronized-identity"></a>同步标识 
同步标识是将本地目录对象（用户和组）与 Azure AD 同步的最简单方法。 

![同步混合标识](./media/choose-hybrid-identity-solution/synchronized-identity.png)

尽管同步标识是最简单且最快速的方法，你的用户将仍需针对基于云的资源维护一个单独的密码。 若要避免此问题，你还可以（可选）将[用户密码哈希同步](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization#what-is-password-synchronization)到 Azure AD 目录中。 同步密码哈希使用户能够使用与本地使用的相同用户名和密码登录基于云的组织资源。 Azure AD Connect 将定期检查本地目录是否发生更改，并保持 Azure AD 目录同步。 如果本地 Active Directory 的用户属性或密码已更改，则将在 Azure AD 中自动更新此信息。 

由于大多数组织只想让用户登录 Office 365、SaaS 应用程序和其他基于 Azure AD 的资源，因此建议使用默认的密码同步选项。 如果这对你不适应，请在传递身份验证和 AD FS 之间进行选择。

> [!TIP]
> 用户密码以表示实际用户密码的哈希值形式存储在本地 Windows Server Active Directory 中。 哈希值是单向数学函数（哈希算法）的计算结果。 没有任何方法可将单向函数的结果还原为纯文本版本的密码。 无法使用密码哈希来登录本地网络。 如果选择同步密码，Azure AD Connect 将从本地 Active Directory 提取密码哈希，并在同步到 Azure AD 之前将额外安全处理应用于密码哈希。 还可将密码同步与密码写回一起使用，以便在 Azure AD 中启用自助密码重置。 此外，还可以对连接到公司网络中的已加入域的计算机上的用户启用单一登录 (SSO)。 通过单一登录，受支持的用户只需输入用户名即可安全访问云资源。 

## <a name="pass-through-authentication"></a>直通身份验证
[Azure AD 传递身份验证](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication)使用本地 Acitve Directory 为基于 Azure AD 的服务提供简单的密码验证解决方案。 如果组织的安全和符合性策略不允许发送用户密码（即使以哈希格式发送也不允许），则只需对加入域的设备提供桌面 SSO 支持，建议使用传递身份验证进行评估。 传递身份验证不需要在 DMZ 中进行任何部署，因此在与 AD FS 进行比较时可以简化部署基础结构。 如果用户使用 Azure AD 进行登录，此身份验证方法可直接针对本地 Active Directory 验证用户的密码。

![直通身份验证](./media/choose-hybrid-identity-solution/pass-through-authentication.png)

使用传递身份验证，无需复杂的网络基础结构，也不需要将本地密码存储在云中。 结合单一登录，传递身份验证可在登录 Azure AD 或其他云服务时提供真正集成的体验。

传递身份验证可以通过 Azure AD Connect 进行配置，利用一个简单的本地代理来侦听密码验证请求。 可轻松地将此代理部署到多台计算机，以提供高可用性和负载均衡。 由于所有通信均为出站，因此无需在 DMZ 中安装连接器。 服务器计算机的连接器要求如下所示：

- Windows Server 2012 R2 或更高版本
- 加入通过其验证用户的林中的域

> [!NOTE]
> Azure AD 传递身份验证当前处于预览阶段，支持新式身份验证的基于 Web 浏览器的客户端和 Office 客户端均支持这种身份验证。 对于不支持的客户端，例如旧的 Office 客户端和 Exchange ActiveSync（包括移动设备上的本机电子邮件客户端），建议使用等效的新式身份验证。 新式身份验证不仅允许使用传递身份验证，还允许应用条件访问策略，如多重身份验证。 

使用加入到 Azure AD 的 Windows 10 设备时，当前不支持传递身份验证。 但可以将密码哈希同步用作自动回退，支持前面提到的 Windows 10 和旧客户端。 在预览阶段，如果选择传递身份验证作为 Azure AD Connect 中的登录选项，则默认启用密码哈希同步。


## <a name="federated-identity-ad-fs"></a>联合标识 (AD FS)
若要更好地控制用户访问 Office 365 和其他云服务的方式，可使用 [Active Directory 联合身份验证服务 (AD FS)](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server-2016) 设置与单一登录 (SSO) 的目录同步。 使用 AD FS 联合验证用户的登录时，可将身份验证委托给验证用户凭据的本地服务器。 在此模型中，本地 Active Directory 凭据永远不会传递到 Azure AD 中。

![联合标识](./media/choose-hybrid-identity-solution/federated-identity.png)

也称为“联合身份验证”，这种登录方法可确保所有用户身份验证均在本地得以控制，并且允许管理员实施更严格的访问控制。 使用 AD FS 的联合身份验证是最复杂的选项，需要在本地环境中部署其他服务器。 联合身份验证还承诺为 Active Directory 和 AD FS 基础结构提供全天候支持。 如果本地 Internet 访问、域控制器或 AD FS 服务器不可用，用户无法登录云服务，此时就需要这种高级支持。

> [!TIP]
> 如果决定使用 Active Directory 联合身份验证服务 (AD FS) 进行联合身份验证，则可以选择性地设置密码同步，作为在 AD FS 基础结构发生故障时的备用身份验证方式。


## <a name="common-scenarios-and-recommendations"></a>常见方案和建议
下面是一些常见的混合标识和访问管理方案，其中每个方案都包含有关适合的混合标识选项的建议。

|我需要：|PWS 和 SSO<sup>1</sup>| PTA 和 SSO<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|将本地 Active Directory 中创建的新用户、联系人和组帐户自动同步到云。|![建议](./media/choose-hybrid-identity-solution/ic195031.png)| ![建议](./media/choose-hybrid-identity-solution/ic195031.png) |![建议](./media/choose-hybrid-identity-solution/ic195031.png)|
|为 Office 365 混合方案设置我的租户|![建议](./media/choose-hybrid-identity-solution/ic195031.png)| ![建议](./media/choose-hybrid-identity-solution/ic195031.png) |![建议](./media/choose-hybrid-identity-solution/ic195031.png)|
|使用户能够使用其本地密码登录并访问云服务|![建议](./media/choose-hybrid-identity-solution/ic195031.png)| ![建议](./media/choose-hybrid-identity-solution/ic195031.png) |![建议](./media/choose-hybrid-identity-solution/ic195031.png)|
|使用公司凭据实现单一登录|![建议](./media/choose-hybrid-identity-solution/ic195031.png)| ![建议](./media/choose-hybrid-identity-solution/ic195031.png) |![建议](./media/choose-hybrid-identity-solution/ic195031.png)|
|确保未在云中存储密码哈希| |![建议](./media/choose-hybrid-identity-solution/ic195031.png)|![建议](./media/choose-hybrid-identity-solution/ic195031.png)|
|启用本地多重身份验证解决方案| | |![建议](./media/choose-hybrid-identity-solution/ic195031.png)|
|支持用户使用智能卡身份验证<sup>4</sup>| | |![建议](./media/choose-hybrid-identity-solution/ic195031.png)|
|在 Office 门户和 Windows 10 桌面上显示密码到期通知| | |![建议](./media/choose-hybrid-identity-solution/ic195031.png)|

> <sup>1</sup> 使用单一登录的密码同步。 

> <sup>2</sup> 传递身份验证和单一登录。 

> <sup>3</sup> 使用 AD FS 的联合单一登录。

> <sup>4</sup> AD FS 可与企业 PKI 集成，允许使用证书登录。 这些证书可以是通过受信任预配通道（如 MDM、GPO 或智能卡证书（包括 PIV/CAC 卡）或 Hello 企业版（信任证书））部署的软证书。 有关智能卡身份验证支持的详细信息，请参阅[此博客](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)。


## <a name="next-steps"></a>后续步骤
[详细了解 Azure 概念证明环境](https://aka.ms/aad-poc)

[安装 Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)

[监视混合标识同步](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)

