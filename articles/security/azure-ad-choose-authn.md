---
title: 为 Azure AD 混合标识解决方案选择正确的身份验证方法 | Microsoft Docs
description: 本指南旨在帮助负责在中型到大型组织中为 Azure AD 混合标识解决方案选择身份验证方法的 CEO、CIO、CISO、首席标识架构师、企业架构师以及安全和 IT 决策制定者。
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 102fa06be3734fa6993616f752922433ee0dee7f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="choosing-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>为 Azure Active Directory 混合标识解决方案选择正确的身份验证方法 

本文是本系列文章中的第一篇，旨在帮助组织实现完整的 Azure AD 混合标识解决方案。 完整的 Azure AD 混合标识解决方案概括为混合标识数字化转型框架，并涵盖组织应关注的业务成果和目标，以确保实现可靠且安全的混合标识解决方案。 框架的第一个业务成果阐述在用户访问云应用时对组织确保身份验证过程安全的要求。 身份验证保护的业务成果的第一个业务目标是，使用户可以使用本地用户名和密码登录云应用。 有了此登录过程以及用户进行身份验证的方式，才有可能在云中实现一切。

对于希望将应用移动到云的组织来说，应首先考虑选择正确的身份验证方式。 不应轻易做出此决策，原因如下：

1. 这是希望移动到云的组织做的第一个决定。 

2. 身份验证方法是组织在云中的关键组成部分，并控制着对所有云数据和资源的访问。

3. 它是 Azure AD 所有其他高级安全和用户体验功能的基础。

4. 身份验证方法一旦实现之后就难以更改。

因为标识是 IT 安全性的新控制层面，所以身份验证是组织对新云世界的访问防护。 组织应确保标识控制层面提高其安全性，并保护其云应用免受入侵。

### <a name="out-of-scope"></a>超出范围

没有现有本地目录占用的组织不是本文的重点。 通常，那些仅在云中创建标识的企业不需要混合标识解决方案。 仅限云标识单独存在在云中，不与相应本地标识相关联。  

## <a name="choosing-the-right-authentication-method"></a>选择正确的身份验证方法

因为 Azure AD 混合标识解决方案作为新的控制平面，身份验证是云访问的基础。 选择正确的身份验证方法是设置 Azure AD 混合标识解决方案至关重要的第一个决定。 使用 Azure AD Connect（还可在云中预配用户）配置身份验证方法的实现。 

若要选择身份验证方法，需要考虑时间、现有基础结构、复杂性和实现所选内容的成本。 这些因素对每个组织都不同，并可能随时间变化。 

Azure AD 支持以下适用于混合标识解决方案的身份验证方法：

### <a name="cloud-authentication"></a>云身份验证
选择此身份验证方法时，Azure AD 处理用户的登录过程。 结合使用无缝单一登录 (SSO)，用户可以登录到云应用，无需重新输入其凭据。 使用云身份验证时具有两个选择： 

**密码哈希同步 (PHS)** - 这是在 Azure AD 中为本地目录对象启用身份验证最简单的方法。 通过密码哈希同步，用户可以使用与其在本地使用的相同用户名和密码，不必部署任意其他基础结构。 某些 Azure AD 高级功能（例如标识保护）需要密码哈希同步，无论选择哪种身份验证方法均如此。

> [!NOTE] 
> 请勿以明文形式存储密码，或者使用 Azure AD 不可逆算法加密。 有关密码哈希同步实际过程的详细信息，请参阅[使用 Azure AD Connect 同步实现密码哈希同步](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization)。 

**传递身份验证 (PTA)** - 使用在一个或多个本地服务上运行的软件代理为 Azure AD 身份验证服务提供简单的密码验证，以借助本地 Active Directory 直接验证用户，确保不在云中进行密码验证。 如果公司的法规要求限制其在云中有哈希密码，因此无法使用密码哈希同步，则可以使用此身份验证方法。 有关实际传递身份验证过程的详细信息，请参阅 [User sign-in with Azure Active Directory Pass-through authentication](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication)（使用 Azure Active Directory 传递身份验证的用户登录）。

### <a name="federated-authentication"></a>联合身份验证
选择此身份验证方法时，Azure AD 将身份验证过程传递到单独的受信任身份验证系统，例如本地 Active Directory 联合身份验证服务 (AD FS)，验证用户的密码。 身份验证系统可以提供其他身份验证要求，例如基于智能卡的身份验证或第三方多重身份验证。 有关详细信息，请参阅[部署 Active Directory 联合身份验证服务](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide)。

以下部分使用决策树帮助你决定自己适合哪种身份验证方法。 该决策树有助于你确定为 Azure AD 混合标识解决方案部署云还是联合身份验证。

## <a name="azure-ad-authentication-decision-tree"></a>Azure AD 身份验证决策树

![image1](media/azure-ad/azure-ad-authn-image1.png)

## <a name="detailed-considerations-on-authentication-methods"></a>详细的身份验证方法注意事项

### <a name="cloud-authentication-password-hash-sync"></a>云身份验证：密码哈希同步 

* **工作量：**对于只需要使其用户登录到 Office 365、SaaS 应用和其他基于 Azure AD 资源的组织，在部署、维护和基础结构方面，密码哈希同步所需的工作量最少。 启用后，密码哈希同步即是 Azure AD Connect 同步过程的一部分，并且每两分钟运行一次。 

* **用户体验：**建议组织借助密码哈希同步部署无缝单一登录 (SSO)，通过避免登陆后的不必要提示提升用户登录体验。

* **高级方案：**组织可以选择借助 Azure AD Identity Protection 报表（例如已泄漏凭据报表）使用标识的见解。 Windows Hello for Business 是[使用密码哈希同步时有特定要求](https://docs.microsoft.com/en-us/windows/access-protection/hello-for-business/hello-identity-verification)的另一个选项。需要借助密码哈希同步的多重身份验证的组织必须使用 Azure AD 多重身份验证，并且不能使用第三方或本地多重身份验证方法。

* **业务连续性：**密码哈希同步完全可以作为云服务使用，适合所有 Microsoft 数据中心。 出于灾难恢复目的，建议将第二个 Azure AD Connect 服务器以暂存模式部署在备用配置中。

* **注意事项：**当前密码哈希同步不会立即强制更改本地帐户状态。 在此情况下，除非用户帐户状态已同步到 Azure AD，否则用户有权访问云应用。 如果组织希望克服此限制，在管理员对本地用户帐户状态做出大量更新后（例如禁用帐户），建议激活新的同步周期。 锁定在外的帐户是下一次周期中同步的另一个用户帐户状态。 

> [!NOTE] 
> 密码过期状态当前不会借助 Azure AD Connect 同步到 Azure AD。 

请参阅[实现密码哈希同步](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization)了解相关部署步骤。

### <a name="cloud-authentication-pass-through-authentication"></a>云身份验证：传递身份验证  

* **工作量：**对于传递身份验证，需要在具有本地 Active Directory 域服务访问权限（包括对本地 AD 域控制器的访问权限）的现有服务器上安装一个或多个（建议三个）轻型代理。 这些代理需要具有对 Internet 的出站访问权限，并且有权访问域控制器。 因此，它不支持在外围网络中部署代理，因为它需要对域控制器的不受约束网络访问权限。 所有流量都已进行加密并受限于身份验证请求。 有关此过程的详细信息，请参阅传递身份验证的[安全性深入研究](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-security-deep-dive)。

* **用户体验：**建议组织借助传递身份验证部署无缝单一登录，通过避免登陆后的不必要提示提升用户登录体验。

* **高级方案：**传递身份验证可确保在本地用户帐户状态为禁用、锁定、密码过期或不在用户的允许登录时段时，立即拒绝身份验证请求。 需要借助传递身份验证的多重身份验证的组织必须使用 Azure AD 多重身份验证，并且不能使用第三方或本地多重身份验证方法。 高级功能（例如 Identity Protection 已泄漏凭据报表）要求部署密码哈希同步，不管是否选择传递身份验证均如此。

* **业务连续性：**除 Azure AD Connect 服务器上的第一个代理以外，建议部署两个额外的传递代理，确保身份验证请求的高可用性。 如果部署了三个代理，关闭一个代理进行维护时另一个仍然可能失败。 部署除传递身份验证以外的密码哈希同步的另一个优势是：主要身份验证方法不再可用时（例如本地服务器不可用时），它可以用作备用身份验证方法。

* **注意事项：**如果将密码哈希同步用作传递身份验证的备用身份验证方法，并且代理无法验证用户凭据，那么不会自动发生到密码哈希同步的故障转移。 需要使用 Azure AD Connect 手动切换登录方法。 传递身份验证仅支持使用新式身份验证和特定 Exchange Online 协议（例如 ActiveSync、POP3 和 IMAP4）的云应用。 例如，Office 应用支持的详细信息上 [Microsoft Office 2013 和更高版本支持新式身份验证，但是更低版本不支持](https://blogs.office.com/en-us/2015/11/19/updated-office-365-modern-authentication-public-preview/)。 请参阅传递身份验证[常见问题](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq)和其他注意事项（包括备用 ID 支持）。

请参阅[实现传递身份验证](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication)了解部署步骤。

### <a name="federated-authentication"></a>联合身份验证

* **工作量：**使用联合身份验证系统依赖于对用户进行身份验证的外部系统。 某些公司想要借助 Azure AD 混合标识解决方案重复使用现有联合系统投资。 联合系统的维护和管理超出 Azure AD 控制。 使用联合系统确保其安全部署和解决身份验证负载，这完全取决于组织。 

* **用户体验：**联合身份验证的用户体验依赖于联合服务器场功能、拓扑和配置的实现。 某些组织需要这种灵活性来调整和配置对联合服务器场的访问权限，以满足其安全要求。 例如，有可能将内部连接用户和设备配置为用户自动登录，不会提示他们输入凭据，因为他们已登录到设备。 另一方面，如有必要，某些高级安全功能可能使用户的登录过程更加困难。

* **高级方案：**客户有 Azure AD 本身不支持的身份验证要求时，通常需要联合身份验证解决方案，有关详细信息，请参阅[此处](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/)，但常见要求包括：

    * 需要智能卡或证书的身份验证
    * 使用本地 MFA 服务器或第三方多重提供程序。
    * 使用第三方身份验证解决方案的身份验证。 请参阅[Azure AD 联合身份验证兼容性列表](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)。
    * 用户必须使用 sAMAccountName 登录（例如 DOMAIN\username），而不是使用用户主体名称 (UPN)（例如 user@domain.com）
    * Windows Hello for Business 实现依赖于证书信任的内部企业公钥基础结构 (PKI)

* **业务连续性：**联合系统通常需要负载均衡的服务器数组（也称为场）配置在内部网络和外围拓扑中，以确保身份验证的高可用性。 当主要身份验证方法不再可用时（例如本地服务器不可用时），可以部署密码哈希同步，并将联合身份验证部署为备用身份验证方法。 某些大型企业组织需要联合解决方案，以支持使用异地 DNS 配置的针对低延迟身份验证请求的多 Internet 入口点。

* **注意事项：**联合系统通常需要更多的本地基础结构投资。 如果已有本地联合投资，大多数组织会选择此选项，它是使用单个标识提供者的一个强大业务需求。 与云身份验证解决方案相比，联合的操作和故障排除更加复杂。 如果使用具有不可路由的域（无法在 Azure AD 中进行验证）的用户 ID，登录时需要进行额外配置才能实现。 此要求被称为“备用登录 ID 支持”。 相关限制和要求，请参阅[配置备用登录 ID](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)。

有关部署步骤，请参阅[实现联合服务](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/deploying-federation-servers)。

> [!NOTE] 
> 部署 Azure AD 混合标识解决方案时，必须确保实现 Azure AD Connect 受支持拓扑之一。 有关受支持和不受支持的配置详细信息，请参阅 [Azure AD Connect 拓扑](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-topologies)。

## <a name="architecture-diagrams"></a>体系结构关系图

下图概述了每个可以与 Azure AD 混合标识解决方案连同使用的身份验证方法所需的高级体系结构组件。 此外，还概述了解决方案差异比较。

下图概述了简单的密码哈希同步解决方案：

![PHS](media/azure-ad/azure-ad-authn-image2.png)

下图概述了传递身份验证的代理要求：

![PTA](media/azure-ad/azure-ad-authn-image3.png)

下图概述了组织的外围和内部网络中联合身份验证所需的组件：

![ADFS](media/azure-ad/azure-ad-authn-image4.png)

## <a name="recommendations-and-considerations-from-azure-ad"></a>Azure AD 建议和注意事项

标识系统确保用户有权访问云应用和云中迁移和提供的业务线应用。 身份验证控制对应用的访问，有助于授权用户保持效率并防止行为不端攻击者接触组织敏感数据。 因此，为组织选择正确的身份验证方法时，请考虑以下建议。 

无论选择的哪个身份验证方法，都请使用或启用密码哈希同步，原因如下：

1. **高可用性和灾难恢复：**传递身份验证和联合身份验证依赖于本地基础结构。 对于传递身份验证，这包括服务器硬件和网络。 它还依赖于域控制器，响应来自传递身份验证代理的身份验证请求。 对于联合身份验证，本地内存占用更多，因为它需要代理身份验证请求的外围网络中的服务器和内部联合服务器。 为了避免单一故障点，组织应部署冗余服务器，确保任意组件失败时始终为身份验证请求提供服务。 许多组件都需要维护才能保持正常运行，如果没有正确计划和实现维护，很可能出现中断。 可以使用密码哈希同步避免中断，因为 Microsoft 的 Azure AD 云身份验证服务可全局缩放且始终可用。

2. **本地中断生存：**网络攻击或灾难造成的本地中断可能导致严重后果，从品牌信誉损失到组织瘫痪而无法处理攻击。 去年，许多组织都受到了恶意软件的攻击（包括有针对性的勒索软件），导致其本地服务器停机。 在帮助客户处理这些类型的攻击时，Microsoft 注意到了两种类型的组织：

   a. 之前已经启用密码哈希同步的组织在几小时内就可以重新联机，方法是将其身份验证方法更改为使用密码哈希同步。通过 Office 365 使用对电子邮件的访问权限，可以解决问题并获得对其他基于云的工作负荷的访问权限。

   b. 之前未启用密码哈希同步的组织必须依靠不受信任的外部使用者电子邮件系统进行通信和解决问题。 在这些情况下，需要几周甚至更多的时间才能再次启动并运行。

3. **标识保护：**在云中保护用户的最佳方法之一是使用 Azure AD Identity Protection。 Microsoft 不断扫描 Internet 上行为不端攻击者在暗网上销售和提供的用户和密码列表。 Azure AD 可以用此信息验证组织的任意用户名和密码是否泄漏。 所以，无论是使用联合身份验证还是传递身份验证，启用密码哈希同步都很重要。 泄漏的凭据以报告形式提供，可用来在用户尝试使用这些密码登录时阻止登录或强制用户更改密码。

根据 [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html) 提供的最新信息，Microsoft 拥有功能最完备的标识和访问管理功能集。 Microsoft 在向授权用户提供从几乎任意设备到上千 SaaS 应用程序（例如 Office 365）的登录的同时每天规避七万亿网络事件。 

## <a name="conclusion"></a>结束语

本文概述了多种身份验证选项，组织可以配置和部署它们以支持对云应用的访问权限。 为了满足各种业务、安全和技术要求，组织可以在密码哈希同步、传递身份验证和联合身份验证之间进行选择。 借助各种身份验证方法，组织可以选择是否通过部署解决方案的工作量和登录过程的用户体验来满足业务需求。 还需要评估组织是否需要高级方案和每种身份验证防范的业务连续性功能。 最后，必须评估每种身份验证方法的注意事项，查看是否会阻止实现所选内容。

## <a name="next-steps"></a>后续步骤

当今世界中，威胁一天 24 小时内都存在，且可能来自任意位置。 实现正确的身份验证方法有助于减轻安全风险并保护标识。 

[开始使用](https://docs.microsoft.com/en-us/azure/active-directory/get-started-azure-ad) Azure AD 并为组织部署正确的身份验证解决方案。

如果你正在考虑从联合身份验证迁移到云身份验证，请了解[有关更改登录方法](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method)的详细信息。 可使用[这些项目计划](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/tree/master/Authentication)，帮助你规划和实施迁移。
