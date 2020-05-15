---
title: 使用 Azure AD 应用程序代理发布本地应用
description: 了解为何要在外部使用应用程序代理向远程用户发布本地 Web 应用程序。 了解应用程序代理体系结构、连接器、身份验证方法和安全优势。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 05/31/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: d38cf25bb3b7622a0d444e4a71a4d62aafc053b6
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196452"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>使用 Azure AD 应用程序代理为远程用户发布本地应用

Azure Active Directory (Azure AD) 提供许多用于保护云中和本地用户、应用与数据的功能。 具体而言，想要在外部发布本地 Web 应用程序的 IT 专业人员可以实施 Azure AD 应用程序代理功能。 然后，需要访问内部应用的远程用户可以安全地访问这些应用。

在新式工作区中，从网络外部安全访问内部应用的能力已变得越发重要。 在处理 BYOD（自带设备）和移动设备等方案时，IT 专业人员很难满足以下两项目标：

* 使最终用户随时随地保持较高的工作效率
* 随时保护企业资产

许多组织认为，只要资源位于企业网络的边界范围内，它们就能受到控制和保护。 但在当今的数字工作区中，受管理的移动设备以及云中的资源与服务已经扩展了该边界。 现在，需要处理好保护用户标识及其设备和应用中存储的数据的复杂性。

或许你已在使用 Azure AD 来管理云中需要访问 Office 365 和其他 SaaS 应用程序以及本地托管式 Web 应用的用户。 如果你已有 Azure AD，可以利用它作为控制平面，来实现对本地应用程序的无缝安全访问。 或者，你可能仍在规划迁移到云中。 如果是这样，在迁移到云的旅程中，一开始可以实施应用程序代理，并采取构建强大标识基础的第一步。

以下列表并不详尽，其中只是列出了在混合共存方案中实施应用代理可以实现的部分目的：

* 在不使用外围网络的情况下以简化的方式在外部发布本地 Web 应用
* 支持在云中和本地跨设备、资源与应用进行单一登录 (SSO)
* 支持对云中和本地的应用执行多重身份验证
* 快速利用 Microsoft 云安全性的云功能
* 集中管理用户帐户
* 集中控制标识和安全性
* 基于组成员身份自动添加或删除用户对应用程序的访问权限

本文将会说明 Azure AD 和应用程序代理如何为远程用户提供单一登录 (SSO) 体验。 用户在不使用 VPN 或双重宿主服务器和防火墙规则的情况下安全连接到本地应用。 本文可帮助你了解应用程序代理如何将云的功能和安全优势引入本地 Web 应用程序。 此外，还会介绍可行的体系结构和拓扑。

## <a name="remote-access-in-the-past"></a>过去的远程访问方式

以前，用于防范攻击者访问内部资源，同时方便远程用户进行访问的控制平面完全位于 DMZ（外围网络）中。 但是，部署在外围网络中的、由外部客户端用来访问企业资源的 VPN 和反向代理解决方案并不适合云世界。 它们往往存在以下弱点：

* 硬件成本
* 维护安全性（修补、监视端口，等等）
* 在边缘对用户进行身份验证
* 对访问外围网络中的 Web 服务器的用户进行身份验证
* 使用 VPN 客户端软件的分发和配置维护远程用户的 VPN 访问权限。 此外，需要在外围网络中维护已加入域的服务器，这很容易受到外部攻击。

在当今的云优先世界中，Azure AD 最适合用于控制哪些人员和内容可以进入你的网络。 Azure AD 应用程序代理与新式身份验证和基于云的技术（例如 SaaS 应用程序和标识提供者）相集成。 用户可以借助这些集成从任意位置访问应用。 应用代理不仅更适合当今的数字工作区，而且比 VPN 和反向代理解决方案更安全，且更易于实施。 远程用户可以像访问 O365 以及其他与 Azure AD 集成的 SaaS 应用一样访问本地应用程序。 无需更改或更新应用程序即可使用应用程序代理。 此外，应用代理不要求通过防火墙打开入站连接。 只需设置好应用代理，然后可以忘记它。

## <a name="the-future-of-remote-access"></a>远程访问的未来

在当今的数字工作区中，用户可在任意位置使用多种设备和应用完成工作。 唯一的定数就是用户标识。 正因如此，当今实现安全网络的第一步就是使用 [Azure AD 的标识管理](https://docs.microsoft.com/azure/security/security-identity-management-overview)功能作为安全控制平面。 使用标识作为控制平面的模型通常包括以下组件：

* 一个用于跟踪用户和用户相关信息的标识提供者。
* 设备目录，用于维护有权访问企业资源的设备列表。 此目录包含相应的设备信息（例如设备类型、完整性等）。
* 策略评估服务，用于确定某个用户和设备是否符合安全管理员制定的策略。
* 授予或拒绝组织资源访问权限的功能。

Azure AD 使用应用程序代理来跟踪需要访问本地发布的和云中的 Web 应用的用户。 它针对这些应用提供一个中心管理点。 尽管不一定要这样做，但我们建议同时启用 Azure AD 条件访问。 在用户如何进行身份验证和获取访问权限方面定义条件可以进一步确保只有适当的人员才能访问应用程序。

**注意：** 必须知道，Azure AD 应用程序代理旨在替代需要访问内部资源的漫游（或远程）用户所用的 VPN 或反向代理。 它并不适合企业网络中的内部用户。 内部用户不必要地使用应用程序代理可能会意外地造成不利的性能问题。

![Azure Active Directory 和所有应用](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>应用代理工作原理的概述

应用程序代理是在 Azure 门户中配置的 Azure AD 服务。 使用应用程序代理可以在 Azure 云中发布与组织中内部应用程序服务器 URL 连接的外部公共 HTTP/HTTPS URL 终结点。 可将这些本地 Web 应用与 Azure AD 集成，以支持单一登录。 然后，最终用户可以像访问 Office 365 和其他 SaaS 应用一样访问本地 Web 应用。

此功能的组件包括应用程序代理服务（在云中运行）、应用程序代理连接器（在本地服务器上运行的轻型代理）和 Azure AD（标识提供者）。 所有三个组件协同工作，为用户提供单一登录体验来访问本地 Web 应用程序。

登录后，外部用户可在其桌面或 iOS/MAC 设备上使用熟悉的 URL 或[“我的应用”访问面板](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)访问本地 Web 应用程序。 例如，应用代理可为远程桌面、SharePoint 站点、Tableau、Qlik、Outlook Web 版和业务线 (LOB) 应用程序提供远程访问和单一登录功能。

![Azure AD 应用程序代理体系结构](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>身份验证

可通过多种方法配置应用程序的单一登录，所选的方法取决于应用程序使用的身份验证。 应用程序代理支持以下类型的应用程序：

* Web 应用程序
* 想要公开给不同设备上丰富应用程序的 Web API
* 托管在远程桌面网关之后的应用程序
* 与 Active Directory 身份验证库 (ADAL) 集成的富客户端应用

应用代理适用于使用以下本机身份验证协议的应用：

* **[Windows 集成身份验证 (IWA)](application-proxy-configure-single-sign-on-with-kcd.md)。** 对于 IWA，应用程序代理连接器使用 Kerberos 约束委派 (KCD) 对 Kerberos 应用程序的用户进行身份验证。

应用代理还支持在第三方集成或特定的配置方案中使用以下身份验证协议：

* [**基于标头的身份验证**](application-proxy-configure-single-sign-on-with-ping-access.md)。 此登录方法使用名为 PingAccess 的第三方身份验证服务。当应用程序使用标头进行身份验证时会使用此方法。 在此方案中，身份验证由 PingAccess 处理。
* [**基于表单或密码的身份验证**](application-proxy-configure-single-sign-on-password-vaulting.md)。 使用此身份验证方法时，用户在首次访问应用程序时使用用户名和密码登录。 首次登录后，Azure AD 会为应用程序提供用户名和密码。 在此方案中，身份验证由 Azure AD 处理。
* [**SAML 身份验证**](application-proxy-configure-single-sign-on-on-premises-apps.md)。 使用 SAML 2.0 或 WS 联合身份验证协议的应用程序支持基于 SAML 的单一登录。 Azure AD 使用 SAML 单一登录通过用户的 Azure AD 帐户对应用程序进行身份验证。

有关支持的方法的详细信息，请参阅[选择单一登录方法](what-is-single-sign-on.md#choosing-a-single-sign-on-method)。

### <a name="security-benefits"></a>安全优势

应用程序代理和 Azure AD 提供的远程访问解决方案支持可供客户利用的多种安全优势，包括：

* **经过身份验证的访问**。 应用程序代理最适合用于发布支持[预身份验证](application-proxy-security.md#authenticated-access)的应用程序，以确保只能通过身份验证的连接访问网络。 对于使用预身份验证的已发布应用程序，如果无法提供有效的令牌，将不允许流量通过应用代理服务流向本地环境。 预身份验证的独特性质会阻止大量的针对性攻击，只有经过身份验证的标识才能访问后端应用程序。
* **条件访问**。 在与网络建立连接之前应用更丰富的策略控制。 使用条件访问可以针对允许哪些流量访问后端应用程序来定义限制。 基于位置、身份验证强度和用户风险配置文件，创建限制登录的策略。 随着条件访问的不断演进，会添加更多的控制措施来提高安全性，例如与 Microsoft Cloud App Security (MCAS) 的集成。 借助 MCAS 集成可为本地应用程序配置[实时监视](application-proxy-integrate-with-microsoft-cloud-application-security.md)，这样就可以利用条件访问基于条件访问策略来实时监视和控制会话。
* **流量终止**。 与后端服务器重建会话时，发往后端应用程序的所有流量将在云中的应用程序代理服务上终止。 此连接策略意味着不会公开后端服务器来定向 HTTP 流量。 由于防火墙未受到攻击，可以更好地防范这些流量受到有针对性的 DoS（拒绝服务）攻击。
* **所有访问都是出站的**。 应用程序代理连接器仅使用通过端口 80 和 443 与云中应用程序代理服务建立的出站连接。 由于没有入站连接，因此不需要为传入的连接或外围网络中的组件打开防火墙端口。 所有连接都是通过安全通道建立的出站连接。
* **基于安全分析和机器学习 (ML) 的智能**。 应用程序代理是 Azure Active Directory 的一部分，因此它可以利用 [Azure AD 标识保护](https://docs.microsoft.com/azure/active-directory/identity-protection/overview)（需要 [Premium P2 许可](https://azure.microsoft.com/pricing/details/active-directory/)）。 Azure AD 标识保护将机器学习安全智能与来自 Microsoft [反数字犯罪部门](https://news.microsoft.com/stories/cybercrime/index.html)和 [Microsoft 安全响应中心](https://www.microsoft.com/msrc)的数据馈送结合起来，以主动识别遭到入侵的帐户。 “标识保护”针对高风险登录提供实时防护。它考虑到多种因素，包括：从受感染的设备进行访问、通过匿名化网络进行访问，或者从异常位置和不太可能的位置进行访问。这样可以增强会话的风险配置文件。 此风险配置文件用于实时保护。 其中的许多报告与事件已通过某个 API 提供，便于与 SIEM 系统集成。

* **以服务的形式进行远程访问**。 无需担心要维护和修补本地服务器即可启用远程访问。 应用程序代理是 Microsoft 拥有的一项 Internet 级服务，因此，用户始终可以获得最新的安全修补程序和升级。 未修补的软件仍会遭受大量攻击。 根据美国国土安全局的信息，[高达 85% 的有针对性攻击都是可预防的](https://www.us-cert.gov/ncas/alerts/TA15-119A)。 使用此服务模型就不再需要承受管理边缘服务器的繁重负担，也不需要费力对其进行修补。

* **Intune 集成**。 使用 Intune 可将企业流量与个人流量分开路由。 应用程序代理确保企业流量经过身份验证。 还可将[应用程序代理和 Intune 托管的浏览器](https://docs.microsoft.com/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers)功能结合使用，使远程用户能够在 iOS 和 Android 设备上安全访问内部网站。

### <a name="roadmap-to-the-cloud"></a>云路线图

实施应用程序代理的另一项主要优势是可将 Azure AD 扩展到本地环境。 事实上，实施应用代理是将组织和应用转移到云的关键一步。 转移到云并摆脱本地身份验证可以减少本地的占用空间，并使用 Azure AD 标识管理功能作为控制平面。 只需对现有应用程序做出极少量的更新甚至无需更新，就能访问单一登录、多重身份验证和集中管理等云功能。 将必要的组件轻松安装到应用代理即可建立远程访问框架。 转移到云后，可以访问最新的 Azure AD 功能和更新，并可以访问高可用性和灾难恢复等功能。

若要详细了解如何将应用迁移到 Azure AD，请参阅[将应用程序迁移到 Azure Active Directory](https://aka.ms/migrateapps/whitepaper) 白皮书。

## <a name="architecture"></a>体系结构

下图概括性地演示了 Azure AD 身份验证服务和应用程序代理如何共同为最终用户提供本地应用程序的单一登录。

![Azure AD 应用程序代理身份验证流](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. 在用户通过终结点访问应用程序后，将其重定向到 Azure AD 登录页面。 如果已配置条件访问策略，则此时会检查特定的条件，以确保符合组织的安全要求。
2. 成功登录后，Azure AD 向用户的客户端设备发送令牌。
3. 客户端将令牌发送到应用程序代理服务，该服务检索令牌中的用户主体名称 (UPN) 和安全主体名称 (SPN)。
4. 应用程序代理转发请求，应用程序代理[连接器](application-proxy-connectors.md)拾取请求。
5. 连接器代表用户执行所需的任何其他身份验证（可选操作，是否执行将取决于身份验证方法），请求应用程序服务器的内部终结点，并将请求发送到本地应用程序。 
6. 通过连接器将应用程序服务器返回的响应发送到应用程序代理服务。
7. 响应将从应用程序代理服务发送到用户。

|组件 |**说明**|
|:-|:-|
|端点|终结点是 URL 或 [最终用户门户](end-user-experiences.md)。 用户可通过访问外部 URL 访问位于你网络外部的应用程序。 网络内的用户可以通过 URL 或最终用户门户访问应用程序。 当用户转到其中一个终结点时，将在 Azure AD 中进行身份验证，并通过连接器路由到本地应用程序。|
|Azure AD|Azure AD 使用存储在云端的租户目录执行身份验证。|
|应用程序代理服务|应用程序代理服务作为 Azure AD 的一部分在云中运行。 它将登录令牌从用户传递到应用程序代理连接器。 应用程序代理在收到请求时转发任何可访问的标头，并根据其协议将标头设置为客户端 IP 地址。 如果传入代理的请求已有该标头，则将客户端 IP 地址添加到逗号分隔列表的末尾，该地址为标头的值。|
|应用程序代理连接器|连接器是可在网络内的 Windows Server 上运行的轻型代理。 连接器管理云端应用程序代理服务与本地应用程序之间的通信。 它只使用出站连接，因此不需要开放任何入站端口或在 DMZ 中放置任何对象。 连接器是无状态的，可根据需要从云中提取信息。 有关连接器的详细信息（例如，它们如何均衡负载和执行身份验证），请参阅 [了解 Azure AD 应用程序代理连接器](application-proxy-connectors.md)。|
|Active Directory (AD)|Active Directory 在本地运行，对域帐户执行身份验证。 配置单一登录后，连接器会与 AD 通信以执行所需的任何其他身份验证。|
|本地应用程序|最后，用户便可以访问本地应用程序。|

Azure AD 应用程序代理包括基于云的应用程序代理服务和本地连接器。 该连接器侦听来自应用程序代理服务的请求并处理与内部应用程序的连接。 必须注意，所有通信均通过 TLS 进行，始终从连接器发起，目标为应用程序代理服务。 也就是说，通信仅限出站。 连接器在执行所有调用时，都会使用客户端证书向应用程序代理服务进行身份验证。 只有在执行初始设置步骤时，连接安全性才有所不同，因为客户端证书是在此步骤中建立的。 有关更多详细信息，请参阅应用程序代理[揭秘](application-proxy-security.md#under-the-hood)。

### <a name="application-proxy-connectors"></a>应用程序代理连接器

[应用程序代理连接器](application-proxy-connectors.md)是本地部署的轻型代理，可帮助与云中的应用程序代理服务建立出站连接。 必须将连接器安装在能够访问后端应用程序的 Windows Server 上。 用户通过连接器连接到应用代理云服务，以将其流量路由到应用，如下图所示。

![Azure AD 应用程序代理网络连接](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

按如下所述在连接器与应用代理服务之间完成设置和注册：

1. IT 管理员为出站流量打开端口 80 和 443，并允许访问连接器、应用代理服务和 Azure AD 所需的多个 URL。
2. 管理员登录到 Azure 门户并运行一个可执行文件，以在本地 Windows 服务器上安装连接器。
3. 该连接器开始“侦听”应用代理服务。
4. 管理员将本地应用程序添加到 Azure AD 并配置相应的设置，例如用户在连接到其应用时所需的 URL。

有关详细信息，请参阅[规划 Azure AD 应用程序代理部署](application-proxy-deployment-plan.md)。

建议始终部署多个连接器，以实现冗余和缩放。 连接器与服务相结合可以处理所有高可用性任务，并可动态添加或删除。 每当有新请求抵达时，该请求会路由到可用的连接器之一。 当连接器运行时，它会在连接到服务时保持活动状态。 如果某个连接器暂时不可用，它不会对此流量做出响应。 未使用的连接器标记为非活动状态，保持这种状态 10 天后将被删除。

连接器还会轮询服务器，确定是否有更高版本的连接器。 尽管你可以执行手动更新，但只要应用程序代理连接器更新程序服务保持运行，连接器就会自动更新。 对于有多个连接器的租户，每次自动更新每个组中的一个连接器，以防环境中出现停机现象。

**注意**：可以通过订阅应用程序代理的 RSS 源来监视其[版本历史记录页](application-proxy-release-version-history.md)，以便在发布更新后收到通知。

将每个应用程序代理连接器分配到[连接器组](application-proxy-connector-groups.md)。 同一连接器组中的连接器充当高可用性和负载均衡的一个单位。 可以创建新组，在 Azure 门户中将连接器分配到这些组，然后分配特定的连接器来为特定的应用程序提供服务。 建议在每个连接器组中至少分配两个连接器，以实现高可用性。

需要支持以下方案时，连接器组非常有用：

* 地理应用发布
* 应用程序分段/隔离
* 发布云中或本地运行的 Web 应用

有关选择在何处安装连接器和优化网络的详细信息，请参阅[使用 Azure Active Directory 应用程序代理时的网络拓扑注意事项](application-proxy-network-topology.md)。

## <a name="other-use-cases"></a>其他用例

到目前为止，本文只是重点介绍了如何使用应用程序代理在外部发布本地应用，同时允许对所有云应用和本地应用进行单一登录。 但是，应用代理还有其他值得探讨的用例。 其中包括：

* **安全发布 REST API**。 在本地运行或者在云中的虚拟机上托管业务逻辑或 API 时，应用程序代理可提供一个公共终结点用于进行 API 访问。 使用 API 终结点访问可以控制身份验证和授权，而无需使用传入端口。 对于使用 Intune 的桌面、iOS、MAC 和 Android 设备，应用程序代理可通过多重身份验证和基于设备的条件访问等 Azure AD Premium 功能提供更高的安全性。 有关详细信息，请参阅[如何让本机客户端应用程序与代理应用程序交互](application-proxy-configure-native-client-application.md)和[结合 Azure Active Directory 和 API 管理使用 OAuth 2.0 保护 API](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)。
* **远程桌面服务** **(RDS)** 。 标准的 RDS 部署需要开放入站连接。 但是，[使用应用程序代理的 RDS 部署](application-proxy-integrate-with-remote-desktop-services.md)会从运行连接器服务的服务器提供永久性的出站连接。 这样，你便可以通过远程桌面服务发布本地应用程序，为最终用户提供更多的应用程序。 此外，可以使用有限的一组双重验证方法和 RDS 的条件访问控制减小部署的受攻击面。
* **发布使用 WebSocket 进行连接的应用程序**。 [Qlik Sense](application-proxy-qlik.md) 支持目前以公共预览版提供，今后将会扩展到其他应用。
* **让本机客户端应用程序与代理应用程序交互**。 使用 Azure AD 应用程序代理不仅可以发布 Web 应用，而且还能发布已使用 Azure AD 身份验证库 (ADAL) 配置的[本机客户端应用程序](application-proxy-configure-native-client-application.md)。 本机客户端应用程序不同于 Web 应用，因为前者安装在设备上，而后者需通过浏览器进行访问。

## <a name="conclusion"></a>结束语

我们的工作方式和所用的工具日新月异。 随着越来越多的员工自带设备办公并普遍使用软件即服务 (SaaS) 应用程序，组织管理和保护其数据的方式也必须有所革新。 公司不再只是在边界保护的自有围墙内部运营。 数据传输到的位置比以往任何时候更多 -- 它们会跨越本地和云环境。 这种演变已帮助提高用户的工作效率和协作能力，但同时也为保护敏感数据带来了更多的挑战。

不管你当前是在使用 Azure AD 管理混合共存方案中的用户，还是对迁移到云的旅程感兴趣，实施 Azure AD 应用程序代理服务都可以通过远程访问来帮助减小本地空间占用量。

当今的组织应开始利用应用代理的以下优势：

* 在外部发布本地应用，消除维护传统 VPN 或其他本地 Web 发布解决方案和外围网络方法的相关开销
* 单一登录到所有应用程序，包括 Office 365 或其他 SaaS 应用，以及本地应用程序
* 云规模的安全性：Azure AD 可利用 Office 365 遥测来防止未经授权的访问
* Intune 集成可确保企业流量经过身份验证
* 集中管理用户帐户
* 自动更新可确保获得最新的安全修补程序
* 发布的新功能；SAML 单一登录支持的最新功能，以及应用程序 Cookie 的更精细管理

## <a name="next-steps"></a>后续步骤

* 有关规划、操作和管理 Azure AD 应用程序代理的信息，请参阅[规划 Azure AD 应用程序代理部署](application-proxy-deployment-plan.md)。
* 若要安排现场演示或获取 90 天免费试用版进行评估，请参阅[企业移动性 + 安全性入门](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)。
