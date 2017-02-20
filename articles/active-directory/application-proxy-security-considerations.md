---
title: "使用 Azure AD 应用程序代理远程访问应用时的安全注意事项 | Microsoft 文档"
description: "介绍使用 Azure AD 应用程序代理时的安全注意事项。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: b01d0ddef2271c440cbff1684c85d23dcda9a1de
ms.openlocfilehash: 75d657412a61de38ab53708a8108d40309de742d


---

# <a name="security-considerations-when-accessing-apps-remotely-using-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理远程访问应用时的安全注意事项

>[!NOTE]
>应用程序代理是一项仅当升级到高级版或基本版的 Azure Active Directory 才可用的功能。 有关详细信息，请参阅 [Azure Active Directory 版本](active-directory-editions.md)。
>  

本文介绍 Azure AD 应用程序代理如何提供安全服务用于远程发布和访问应用程序。 

Azure AD 应用程序代理提供以下安全优势：

**基于身份验证的访问：**只有经过身份验证的连接才能访问你的网络

* Azure AD 应用代理依赖于使用 Azure AD STS 执行所有身份验证。 对于使用预身份验证的已发布应用程序，如果无法提供有效的 STS 令牌，将不允许流量通过应用代理服务流向你的环境。
* 预身份验证的独特性质会阻止大量的匿名攻击，只允许经过身份验证的标识访问后端应用程序。

**条件访问：**在与网络建立连接之前应用更丰富的策略控制

* 使用条件访问可以针对允许哪些流量访问后端应用程序来进一步具体化限制。 可以基于位置、身份验证强度和用户风险配置文件来定义限制。
* 此功能可给攻击者带来更大的障碍。 可以阅读 [Azure Active Directory 条件访问入门](https://azure.microsoft.com/en-us/documentation/articles/active-directory-conditional-access-azuread-connected-apps)，了解有关条件访问的详细信息。

**流量终止：**所有流量在云中终止

* Azure AD 应用代理是一个反向代理，因此，发往后端应用程序的所有流量将在服务上终止。 只能与后端服务器重建会话。 这意味着，不会公开后端服务器来定向 HTTP 流量。 例如，可以更轻松地缓解有针对性的攻击。

**所有访问都是出站的：**无需向企业网络打开入站连接

* Azure AD 连接器与 Azure AD 应用代理服务保持出站连接。 这意味着，无需为传入连接打开防火墙端口。 
* 传统的方法要求部署外围网络，并在网络边缘向未经身份验证的连接开放访问权限。 这就需要为 WAF 产品投入大笔额外资金，以便分析流量，为环境提供进一步的保护。 使用应用代理可以避免这种情况。 甚至可以考虑不部署外围网络，因为所有连接都是出站的，并且是通过安全通道建立的。

**安全分析和基于机器语言的智能：**先进的安全保护

* Azure AD Identity Protection 包含机器学习驱动的智能，其数据源来自我们的反数字犯罪部门和 Microsoft 安全响应中心。 此外，我们会主动识别遭到入侵的帐户并提供实时保护，防范高风险的登录。 我们会考虑诸多因素，例如，通过受感染的设备和匿名网络的访问，以及通过异常位置和不太可能的位置的访问。
* 其中的许多报告与事件已通过某个 API 提供，便于与 SIEM 系统集成。
* 可以阅读 [Azure Active Directory Identity Protection](https://azure.microsoft.com/en-us/documentation/articles/active-directory-identityprotection)，了解有关 Azure AD Identity Protection 的详细信息。

**以服务的形式进行远程访问：**无需担心要维护和修补本地服务器

* Azure AD 应用代理是 Microsoft 拥有的一个 Internet 级服务，保证为用户提供最新的安全修补程序和升级。 因此，我们会处理好用户的网络安全。
* 未修补的软件仍会遭受大量攻击。 使用我们的服务模型，用户不再需要承受管理边缘服务器的负担。 

Azure AD 随附的远程访问服务 [Azure 信任中心](https://azure.microsoft.com/en-us/support/trust-center)所述的指导原则和标准运行。 

下图显示了 Azure AD 如何实现对本地应用程序的安全远程访问。

 ![AzureAD 应用程序代理连接器](./media/application-proxy-security-considerations/secure-remote-access.png)

## <a name="components-of-the-azure-ad-app-proxy-solution"></a>Azure AD 应用代理解决方案的组件

Azure AD 应用程序代理由两个部分组成：

* 基于云的服务 (AAD AP)。 这是建立外部客户端/用户连接的位置。
* 称为“AAD 应用代理连接器”的本地组件。  该连接器侦听来自 AAD AP 服务的请求并处理与内部应用程序的连接，包括处理用于 SSO 的 KCD（Kerberos 约束委派）等各种项。

### <a name="traffic-flowsand-how-they-are-secured"></a>流量及其保护方式
本部分包含有关如何保护流量的详细信息。 发生以下情况时，将在连接器与应用代理服务之间建立流量： 

* 首次设置连接器时。
* 连接器从应用代理服务提取配置信息，包括每个连接器所属的连接器组。
* 用户访问发布的应用程序时。

>[!NOTE]
>所有通信通过 SSL 发生，始终从连接器发起，目标为应用代理服务。 此服务只会建立出站连接。
>

连接器在执行所有调用时，使用客户端证书向应用代理服务进行身份验证。 只有在执行初始设置步骤时才不是这样，因为客户端证书是在此步骤中建立的。

#### <a name="install-the-connector"></a>安装连接器

首次设置连接器时会发生以下流量。

1. 在安装连接器的过程中，将连接器注册到服务。 此时，系统会提示用户输入其 Azure AD 管理员凭据。 获取令牌，然后将其提供给 Azure AD 应用代理服务。
2. 应用代理评估该令牌，确保该用户是颁发的令牌所针对的租户中公司管理员角色的成员。 如果不是，则终止过程。
3. 连接器生成客户端证书请求并将此请求连同令牌一起传递给应用代理服务，后者将验证令牌并为客户端证书请求签名。 
4. 以后，连接器将使用此客户端证书来与应用代理服务通信。
4. 连接器使用其客户端证书从服务执行初始的系统配置数据提取，并准备好接收请求。

#### <a name="periodic-configuration-updates"></a>定期配置更新

每当应用代理服务更新配置设置时，都会定期发生以下流量。

1. 连接器使用其客户端证书连接到应用代理服务中的配置终结点。
2. 验证客户端证书后，应用代理服务向连接器返回配置数据；例如，连接器应该所属的连接器组。
3. 如果当前证书超过 30 天，连接器将生成新的证书请求，即，每隔 30 天有效地滚动更新客户端证书。

#### <a name="accesss-published-applications"></a>访问发布的应用程序

当用户访问发布的应用程序时，将发生以下流量。

1. 当用户访问发布的应用程序时，应用代理服务将检查应用的配置设置。  如果应用配置为使用预身份验证和 Azure AD，则将用户重定向到 Azure AD STS 进行身份验证。  如果应用是使用传递身份验证发布的，则跳过此步骤。
 * 在使用 Azure AD 进行身份验证期间，我们将检查特定应用程序的任何条件访问策略要求。 这是为了确保将用户分配到该应用程序；（例如，如果需要 MFA，身份验证序列将提示用户执行第二道身份验证）。
 * 所有检查都通过后，Azure AD STS 将为应用程序颁发已签名的令牌，并将用户重定向回到应用代理服务。
 * 然后，应用代理将验证该令牌，确保它是向用户请求访问的应用程序颁发的。 这项验证是连同其他检查一起进行的，例如，这些检查包括确保令牌已由 Azure AD 签名并且仍在有效期内，等等。
 * 应用代理设置加密的身份验证 Cookie（例如非持久性 Cookie），指出已在应用程序中发生身份验证。  此 Cookie 包含基于 Azure AD 颁发的令牌和其他数据（例如，用于身份验证的用户名）的过期时间戳。  此 Cookie 是使用只有应用代理服务知道的私钥加密的。
 * 应用代理将用户重定向回到最初请求的 URL。
 >[!NOTE] 
 >如果预身份验证步骤的任何一个环节失败，用户的请求将被拒绝，并向用户显示一条消息指出问题的起源。
 >

2. 应用代理从客户端收到请求后，将验证预身份验证条件是否得到满足，并且 Cookie 是否仍然有效（如果需要）。 然后，在相应的队列中放置一个请求供本地连接器处理。 

 >[!NOTE]
 >来自连接器的所有请求通过出站连接发送到应用代理服务。 连接器向应用代理保持打开出站连接。 传入某个请求时，应用代理会在一个打开的连接上将该请求排入队列，等待连接器提取。
 >

 * 该请求包含应用程序中的项，例如，已加密 Cookie 中的请求标头和数据、发出请求的用户，以及请求 ID头  但是，加密的身份验证 Cookie 不会发送到连接器。
3. 连接器基于持久的出站连接从队列中接收请求。 应用代理将基于请求执行以下操作之一：
 * 连接器确认它是否可以识别该应用程序。  如果不可以，连接器将与应用代理服务建立连接，以收集有关该应用程序的详细信息并将其缓存在本地。
 * 如果请求是一个简单的操作，例如，像 RESTful“GET”请求一样在正文中不包含任何数据，则连接器将与目标内部资源建立连接并等待响应。
 * 如果请求的正文中包含关联的数据（例如 RESTful“POST”操作），连接器将使用客户端证书来与应用代理实例建立出站连接。 这样做是为了请求数据，并打开与内部资源的连接。  收到来自连接器的请求后，应用代理服务开始接受用户的内容并将其转发到连接器。  连接器随之将数据转发到内部资源。
4. 完成向后端发送请求/传输所有内容后，连接器将等待响应。
5. 收到响应后，连接器将与应用代理服务建立出站连接，以返回标头详细信息并开始流式传输返回的数据。
6. 应用代理将数据“流式传输”给用户。  根据应用程序的需要和定义，此时可能会发生某些标头处理活动。

在使用客户端浏览器通过 Azure Web 应用程序来与本地 Windows 身份验证简单对象访问协议 (SOAP) 终结点通信时，如果需要帮助，请参阅这篇 [Azure 现场说明博客](http://www.azurefieldnotes.com/2016/12/02/claims-to-windows-identity-translation-solutions-and-its-flaws-when-using-azure-ad-application-proxy)。 

##<a name="next-steps"></a>后续步骤
[了解 Azure AD 应用程序代理连接器](application-proxy-understand-connectors.md)




<!--HONumber=Feb17_HO1-->


