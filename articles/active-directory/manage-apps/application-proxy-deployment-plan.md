---
title: 规划 Azure Active Directory 应用程序代理部署
description: 规划组织内应用程序代理部署的端到端指南
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: baselden
ms.custom: has-adal-ref
ms.openlocfilehash: a293bd33d3a25f26e5374184da42db335041284d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610133"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>规划 Azure AD 应用程序代理部署

Azure Active Directory （Azure AD）应用程序代理是一种安全且经济高效的远程访问解决方案，适用于本地应用程序。 它为 "Cloud First" 组织提供直接转换路径，以管理对旧的本地应用程序的访问，这些应用程序尚不能使用新式协议。 有关更多介绍性信息，请参阅[什么是应用程序代理](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)。

建议使用应用程序代理来使远程用户访问内部资源。 应用程序代理不需要对这些远程访问用例使用 VPN 或反向代理。 它不适用于位于企业网络上的用户。 使用应用程序代理进行 intranet 访问的这些用户可能会遇到意外的性能问题。

本文包含规划、操作和管理 Azure AD 应用程序代理所需的资源。

## <a name="plan-your-implementation"></a>规划实施

以下部分提供了关键规划元素的广泛视图，这些元素将为你提供高效的部署体验。

### <a name="prerequisites"></a>先决条件

在开始实施之前，需要满足以下先决条件。 在本[教程](application-proxy-add-on-premises-application.md)中，可以查看有关设置环境的详细信息，包括这些先决条件。

* **连接器**：连接器是可部署到的轻型代理：
   * 本地物理硬件
   * 在任何虚拟机监控程序解决方案中托管的 VM
   * 在 Azure 中托管的 VM，用于启用到应用程序代理服务的出站连接。

* 有关更详细的概述，请参阅[了解 Azure AD 应用代理连接器](application-proxy-connectors.md)。

     * 必须先[为 TLS 1.2 启用](application-proxy-add-on-premises-application.md)连接器计算机，然后才能安装连接器。

     * 如果可能，请在与后端 web 应用程序服务器[相同的网络](application-proxy-network-topology.md)和网段中部署连接器。 在完成应用程序发现后，最好部署连接器。
     * 建议每个连接器组至少有两个连接器，以提供高可用性和规模。 如果在任何时候都需要为计算机服务，则具有三个连接器是最佳的。 查看[连接器容量表](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning)，以帮助确定要在其上安装连接器的计算机的类型。 计算机越大，连接器的缓冲区和性能就越多。

* **网络访问设置**： Azure AD 应用程序代理连接器[通过 HTTPS （tcp 端口443）和 HTTP （tcp 端口80）连接到 Azure](application-proxy-add-on-premises-application.md)。

   * 不支持终止连接器 TLS 流量，并且会阻止连接器使用各自的 Azure 应用代理终结点建立安全通道。

   * 避免连接器和 Azure 之间的出站 TLS 通信的所有形式的内联检查。 连接器与后端应用程序之间的内部检查是可行的，但可能会降低用户体验，因此不建议这样做。

   * 连接器本身的负载平衡也不受支持，甚至不受必要。

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>配置 Azure AD 应用程序代理之前的重要注意事项

必须满足以下核心要求才能配置和实现 Azure AD 应用程序代理。

*  **Azure 载入**：在部署应用程序代理之前，必须从本地目录同步用户标识，或直接在 Azure AD 租户中创建用户标识。 标识同步允许 Azure AD 在授予用户对应用代理发布的应用程序的访问权限之前对用户进行预身份验证，并获得执行单一登录 (SSO) 所需的用户标识符信息。

* **条件性访问要求**：我们不建议使用应用程序代理进行 intranet 访问，因为这会增加会影响用户的延迟。 建议将应用程序代理与预身份验证和条件访问策略一起使用，以便从 internet 进行远程访问。  提供 intranet 使用条件性访问的方法是实现应用程序的现代化，使其能够直接使用 AAD 进行身份验证。 有关详细信息，请参阅将[应用程序迁移到 AAD 的资源](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources)。

* **服务限制**：若要防止单个租户过度资源，有每个应用程序和租户设置限制限制。 若要查看这些限制，请参阅[Azure AD 服务限制和限制](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)。 这些限制基于比典型的使用量更高的基准，并为大多数部署提供充足的缓冲区。

* **公共证书**：如果你使用的是自定义域名，则必须购买 TLS/SSL 证书。 根据组织的要求，获取证书可能需要一些时间，我们建议尽早开始此过程。 Azure 应用程序 Proxy 支持标准、[通配符](application-proxy-wildcard.md)或基于 SAN 的证书。 有关更多详细信息，请参阅[Azure AD 应用程序代理配置自定义域](application-proxy-configure-custom-domain.md)。

* **域要求**：使用 Kerberos 约束委派（KCD）单一登录到已发布的应用程序要求运行连接器的服务器和运行该应用程序的服务器都已加入域，并且属于同一域或信任域。
有关该主题的详细信息，请参阅[KCD for 单一登录](application-proxy-configure-single-sign-on-with-kcd.md)和应用程序代理。 连接器服务在本地系统的上下文中运行，不应配置为使用自定义标识。

* **Url 的 DNS 记录**

   * 在应用程序代理中使用自定义域之前，必须在公用 DNS 中创建一个 CNAME 记录，以便客户端能够将自定义的外部 URL 解析为预定义的应用程序代理地址。 如果无法为使用自定义域的应用程序创建 CNAME 记录，将阻止远程用户连接到应用程序。 添加 CNAME 记录所需的步骤可能因 DNS 提供程序而异，因此，请了解如何[使用 Azure 门户管理 dns 记录和记录集](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal)。

   * 同样，连接器主机必须能够解析发布的应用程序的内部 URL。

* **管理权限和角色**

   * **连接器安装**需要对其上安装的 Windows 服务器具有本地管理权限。 它还需要至少一个*应用程序管理员*角色来进行身份验证，并将连接器实例注册到 Azure AD 租户。

   * **应用程序发布和管理**需要*应用程序管理员*角色。 应用程序管理员可以管理目录中的所有应用程序，包括注册、SSO 设置、用户和组分配以及许可、应用程序代理设置和许可。 它不能授予管理条件访问的能力。 *云应用程序管理员*角色具有应用程序管理员的所有功能，但它不允许管理应用程序代理设置。

* **许可**：可通过 Azure AD Premium 订阅获取应用程序代理。 有关许可选项和功能的完整列表，请参阅[Azure Active Directory 定价页](https://azure.microsoft.com/pricing/details/active-directory/)。

### <a name="application-discovery"></a>应用程序发现

通过收集以下信息，编译通过应用程序代理发布的所有范围内的应用程序的清单：

| 信息类型| 要收集的信息 |
|---|---|
| 服务类型| 例如： SharePoint、SAP、CRM、自定义 Web 应用程序、API |
| 应用程序平台 | 例如： Windows IIS、Linux 上的 Apache、Tomcat、NGINX |
| 域成员身份| Web 服务器的完全限定的域名（FQDN） |
| 应用程序位置 | Web 服务器或场位于你的基础结构中的位置 |
| 内部访问 | 内部访问应用程序时使用的准确 URL。 <br> 如果在场，使用哪种类型的负载平衡？ <br> 应用程序是否从本身的源中提取内容。<br> 确定应用程序是否在 Websocket 上运行。 |
| 外部访问 | 应用程序已从外部公开到的供应商解决方案。 <br> 要用于外部访问的 URL。 如果是 SharePoint，请确保根据[本指南](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings)配置备用访问映射。 如果没有，则需要定义外部 Url。 |
| 公用证书 | 如果使用自定义域，则使用相应的使用者名称购买证书。 如果证书存在，请记下可获取该证书的序列号和位置。 |
| 身份验证类型| 应用程序支持的身份验证类型，如基本、Windows 集成身份验证、基于窗体、基于标头和声明。 <br>如果将应用程序配置为在特定的域帐户下运行，请注意服务帐户的完全限定的域名（FQDN）。<br> 如果基于 SAML，则为标识符和回复 Url。 <br> 如果基于标头，则为供应商解决方案和处理身份验证类型的特定要求。 |
| 连接器组名称 | 将指定向此后端应用程序提供管道和 SSO 的连接器组的逻辑名称。 |
| 用户/组访问权限 | 将被授予对应用程序的外部访问权限的用户或用户组。 |
| 其他需求 | 请注意发布应用程序时应考虑的任何其他远程访问或安全要求。 |

您可以下载此[应用程序清单电子表格](https://aka.ms/appdiscovery)来清点您的应用程序。

### <a name="define-organizational-requirements"></a>定义组织要求

以下是应该为其定义组织业务要求的区域。 每个区域均包含要求的示例

 **访问**

* 已加入域或 Azure AD 加入设备的远程用户可以使用无缝单一登录（SSO）安全访问发布的应用程序。

* 具有批准的个人设备的远程用户可以安全地访问已发布的应用程序，前提是这些用户已在 MFA 中注册，并已在其移动电话上将 Microsoft Authenticator 应用注册为身份验证方法。

**调控**

* 管理员可以定义和监视用户分配到通过应用程序代理发布的应用程序的生命周期。

**安全性**

* 只有通过组成员身份或单独分配到应用程序的用户可以访问这些应用程序。

**性能**

* 与从内部网络访问应用程序相比，应用程序性能不会有所下降。

**用户体验**

* 用户了解如何通过在任何设备平台上使用熟悉的公司 Url 来访问其应用程序。

**审核**
* 管理员可以审核用户访问活动。


### <a name="best-practices-for-a-pilot"></a>试验的最佳实践

确定完全通过单一登录（SSO）对单个应用程序进行远程访问所需的时间和工作量。 执行此操作的方法是运行一个可考虑其初始发现、发布和常规测试的试验。 使用已预先配置为集成 Windows 身份验证（IWA）的基于 IIS 的简单 web 应用程序有助于建立基准，因为此安装程序需要尽量少地试验远程访问和 SSO。

以下设计元素应直接在生产租户中增加试点实现的成功与否。

**连接器管理**：

* 连接器在向应用程序提供本地管道时发挥着重要作用。 在将发布的应用程序授权到生产环境之前，使用**默认**连接器组足以满足对它们的初始试验测试。 然后，已成功测试的应用程序可以移动到生产连接器组。

**应用程序管理**：

* 你的员工最有可能记得某个外部 URL 熟悉且相关。 避免使用预定义的 msappproxy.net 或 onmicrosoft.com 后缀发布应用程序。 相反，请提供熟悉的顶级验证域，并以逻辑主机名（例如 intranet）作为前缀 *。 <customers_domain>。*

* 通过隐藏其在 Azure MyApps 门户中的 "启动" 图标，限制试点应用程序的图标对试点组的可见性。 准备好生产时，可以将应用范围限定为其相应的目标受众，无论是在同一预生产租户中，还是在生产租户中发布应用程序。

**单一登录设置**：某些 SSO 设置具有特定的依赖关系，可能需要一些时间进行设置，因此请确保提前解决依赖关系，从而避免更改控制延迟。 这包括域加入连接器主机使用 Kerberos 约束委派（KCD）执行 SSO，并负责处理其他耗时的活动。 例如，如果需要基于标头的 SSO，则设置 PING 访问实例。

**连接器主机和目标应用程序之间的 TLS**：安全性非常重要，因此应始终使用连接器主机和目标应用程序之间的 tls。 特别是，如果为基于窗体的身份验证（FBA）配置了 web 应用程序，则会有效地以明文形式传输用户凭据。

**增量实现并测试每个步骤**。
在发布应用程序后执行基本功能测试，以确保满足所有用户和业务要求，请遵循以下说明：

1. 测试和验证已禁用预身份验证的对 web 应用程序的常规访问。
2. 如果成功，则启用预身份验证并分配用户和组。 测试和验证访问权限。
3. 然后添加应用程序的 SSO 方法，并再次测试以验证访问权限。
4. 根据需要应用条件性访问和 MFA 策略。 测试和验证访问权限。

**故障排除工具**：故障排除时，请始终先通过从连接器主机上的浏览器验证对已发布应用程序的访问权限，并确认应用程序按预期方式工作。 你的设置越简单，确定根本原因会更容易，因此请考虑尝试使用最小配置（例如仅使用单个连接器，而不使用 SSO）重现问题。 在某些情况下，web 调试工具（如 Telerik 的 Fiddler）可以在通过代理访问的应用程序中排除访问或内容问题的不需要。 Fiddler 还可以充当代理来帮助跟踪和调试移动平台（如 iOS 和 Android）的流量，以及可以配置为通过代理进行路由的几乎所有内容。 有关详细信息，请参阅[故障排除指南](application-proxy-troubleshoot.md)。

## <a name="implement-your-solution"></a>实现你的解决方案

### <a name="deploy-application-proxy"></a>部署应用程序代理

本教程介绍了部署应用程序代理的步骤，[用于添加用于远程访问的本地应用程序](application-proxy-add-on-premises-application.md)。 如果安装未成功，请在门户中选择 "**应用程序代理故障排除**" 或使用故障排除指南[来了解有关安装应用程序代理程序连接器的问题](application-proxy-connector-installation-problem.md)。

### <a name="publish-applications-via-application-proxy"></a>通过应用程序代理发布应用程序

发布应用程序假定您已经满足所有先决条件，并且您有多个连接器在应用程序代理页中显示为已注册和活动状态。

你还可以使用[PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)发布应用程序。

下面是发布应用程序时需要遵循的一些最佳做法：

* **使用连接器组**：分配已指定用于发布每个单独的应用程序的连接器组。 建议每个连接器组至少有两个连接器，以提供高可用性和规模。 如果在任何时候都需要为计算机服务，则具有三个连接器是最佳的。 此外，请参阅[使用连接器组在单独的网络和位置上发布应用程序](application-proxy-connector-groups.md)，了解如何使用连接器组按网络或位置对连接器进行分段。

* **设置后端应用程序超时**：如果应用程序可能需要超过75秒的时间来处理客户端事务，则此设置非常有用。 例如，当客户端将查询发送到充当数据库前端的 web 应用程序时。 前端将此查询发送到其后端数据库服务器并等待响应，但在收到响应时，会话的客户端将超时。将 "超时" 设置为 "长" 可提供180秒，以完成较长的事务。

* **使用适当的 Cookie 类型**

   * **仅限 HTTP 的 Cookie**：通过让应用程序代理在 set-Cookie HTTP 响应标头中包含 HTTPOnly 标志来提供额外的安全性。 此设置有助于缓解攻击，如跨站点脚本（XSS）。 对于需要访问会话 cookie 的客户端/用户代理，将此设置为 "否"。 例如，RDP/MTSC 客户端连接到通过应用程序代理发布的远程桌面网关。

   * **安全 cookie**：当使用 secure 特性设置 cookie 时，如果通过 TLS 安全通道传输请求，则用户代理（客户端应用）将仅在 HTTP 请求中包含 cookie。 这有助于降低 cookie 在明文通道上泄露的风险，因此应启用此功能。

   * **永久性 Cookie**：允许应用程序代理会话 Cookie 在浏览器闭包之间保持不变，直至过期或删除为止。 用于富应用程序（如 office）访问已发布的 web 应用程序中的文档，而无需重新提示用户进行身份验证。 使用时务必小心，因为如果不将服务与其他补偿控制结合使用，则持久性 cookie 最终会使服务面临未经授权的访问的风险。 此设置应该仅用于无法在进程之间共享 cookie 的较旧的应用程序。 更好的做法是，更新应用程序以处理进程之间的共享 cookie，而不是使用此设置。

* **转换标头中的 url**：在无法配置内部 DNS 以匹配组织的公共命名空间（a k. a Split dns）的情况下启用此操作。 除非你的应用程序需要客户端请求中的原始主机标头，否则请将此值设置为 "是"。 另一种方法是让连接器使用内部 URL 中的 FQDN 来路由实际流量，并使用外部 URL 中的 FQDN 作为主机标头。 在大多数情况下，这种替代方法应允许应用程序在远程访问时正常工作，但用户失去了在 URL 外 & 匹配的好处。

* **转换应用程序主体中的 url**：如果想要将应用程序中的链接从该应用转换回客户端，请打开应用的应用程序正文链接转换。 如果启用此功能，则会尽力转换应用代理在 HTML 和返回到客户端的 CSS 响应中查找的所有内部链接。 当发布包含内容中硬编码的绝对或 NetBIOS 短名称链接的应用程序或包含链接到其他本地应用程序的内容的应用时，此方法非常有用。

对于发布的应用链接到其他已发布应用的方案，为每个应用程序启用链接转换，以便您可以在每个应用程序级别控制用户体验。

例如，假设你有三个通过应用程序代理发布的应用程序，所有这些应用程序都链接在一起：权益、支出和旅行，外加第四个不通过应用程序代理发布的应用。

![图片1](media/App-proxy-deployment-plan/link-translation.png)

当你为权益应用启用链接转换时，指向支出和旅游的链接将重定向到这些应用的外部 Url，以便从公司网络外部访问应用程序的用户可以访问这些应用。 由于尚未对这两个应用启用链接转换，因此从支出到福利的链接不起作用。 不会重定向反馈链接，因为没有外部 URL，因此使用权益应用的用户将无法从公司网络外部访问反馈应用。 有关[链接转换和其他重定向选项](application-proxy-configure-hard-coded-link-translation.md)的详细信息，请参阅。

### <a name="access-your-application"></a>访问应用程序

有多个选项可用于管理对应用程序代理已发布资源的访问，因此，请选择最适合您的给定方案和伸缩性需求的选项。 常见的方法包括：使用正在通过 Azure AD Connect 同步的本地组，根据用户属性在 Azure AD 中创建动态组，使用由资源所有者管理的自助服务组或所有这些组的组合。 请参阅链接的资源，了解每种资源的优点。

为用户分配对应用程序的访问权限，最直接的方法是从已发布应用程序的左窗格转到 "**用户和组**" 选项，然后直接分配组或个人。

![图片 24](media/App-proxy-deployment-plan/add-user.png)

你还可以通过分配当前不是其成员的组并配置自助服务选项，使用户能够对你的应用程序进行自助访问。

![图片25](media/App-proxy-deployment-plan/allow-access.png)

如果启用，则用户将能够登录到 MyApps 门户并请求访问权限，并将其自动批准并添加到已允许的自助服务组，或者需要指定审批者的批准。

还可以[通过 AZURE AD B2B 邀请来宾用户访问通过应用程序代理发布的内部应用程序](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker)。

对于通常以匿名方式访问的本地应用程序，不需要进行身份验证，你可能更愿意禁用位于应用程序**属性**中的选项。

![图片26](media/App-proxy-deployment-plan/assignment-required.png)


如果将此选项设置为 "否"，则用户无需权限即可通过 Azure AD 应用代理访问本地应用程序，因此请谨慎使用。

发布应用程序后，应该可以通过在浏览器中键入其外部 URL，或通过其在上[https://myapps.microsoft.com](https://myapps.microsoft.com/)键入其图标来访问该应用程序。

### <a name="enable-pre-authentication"></a>启用预身份验证

验证是否可通过应用程序代理访问应用程序，方法是通过外部 URL 访问应用程序。

1. 导航到 " **Azure Active Directory** > **企业应用程序** > " "**所有应用程序**"，并选择要管理的应用。

2. 选择“应用程序代理”****。

3. 在 "**预身份验证**" 字段中，使用下拉列表选择 " **Azure Active Directory**"，然后选择 "**保存**"。

启用预身份验证后，Azure AD 将首先质询用户进行身份验证，如果配置了单一登录，则后端应用程序也会在授予对应用程序的访问权限之前验证用户。 将预身份验证模式从 Passthrough 改为 Azure AD 还会通过 HTTPS 配置外部 URL，因此，最初为 HTTP 配置的所有应用程序现在都将通过 HTTPS 进行保护。

### <a name="enable-single-sign-on"></a>启用单一登录

SSO 可提供最佳的用户体验和安全性，因为用户在访问 Azure AD 时只需登录一次。 用户预身份验证后，应用程序代理连接器将以用户身份向本地应用程序进行身份验证。 后端应用程序将处理登录，就好像它是用户本身一样。

选择 "**直通**" 选项可允许用户访问已发布的应用程序，而无需对 Azure AD 进行身份验证。

仅当 Azure AD 可以识别请求访问资源的用户时，才可以执行 SSO，因此必须将应用程序配置为使用 SSO 访问 Azure AD 对用户进行预身份验证，否则 SSO 选项将被禁用。

读取[Azure AD 中的应用程序的单一登录](what-is-single-sign-on.md)，以帮助你在配置应用程序时选择最适合的 SSO 方法。

###  <a name="working-with-other-types-of-applications"></a>使用其他类型的应用程序

Azure AD 应用程序代理还可以支持开发使用 Azure AD 身份验证库（[ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)）或 Microsoft 身份验证库（[MSAL](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/)）的应用程序。 它通过使用在客户端请求的标头信息中收到 Azure AD 颁发的令牌，来代表用户执行预身份验证，从而支持本机客户端应用。

若要了解应用程序代理的可用配置，请阅读[发布本机和移动客户端应用](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client)程序和[基于声明的应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps)。

### <a name="use-conditional-access-to-strengthen-security"></a>使用条件性访问增强安全性

应用程序安全性需要一组高级安全功能，这些功能可防止本地和云中的复杂威胁。 攻击者通常使用弱、默认或被盗的用户凭据来获取企业网络访问权限。  Microsoft 标识驱动的安全性通过管理和保护特权和非特权标识来减少使用盗用的凭据。

以下功能可用于支持 Azure AD 应用程序代理：

* 基于用户和位置的条件访问：使用[基于位置的条件性访问策略](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)限制基于地理位置或 IP 地址的用户访问来保护敏感数据。

* 基于设备的条件性访问：确保只有已注册、已批准和合规的设备才能使用[基于设备的条件访问](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications)来访问公司数据。

* 基于应用程序的条件性访问：如果用户不在公司网络上，则不需要停止工作。 [安全访问公司云和本地应用](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)，并使用条件访问维护控制。

* 基于风险的条件性访问：使用基于风险的条件性访问策略（无论是在本地还是在云中），使用[基于风险的条件性访问策略](https://www.microsoft.com/cloud-platform/conditional-access)来保护数据免受恶意黑客的攻击。

* Azure AD 访问面板：通过部署的应用程序代理服务和安全发布的应用程序，为用户提供一个简单的中心，用于发现和访问其所有应用程序。 使用自助服务功能提高工作效率，如通过[访问面板](https://aka.ms/AccessPanelDPDownload)请求访问新的应用程序和组或代表其他用户管理对这些资源的访问权限。

## <a name="manage-your-implementation"></a>管理实现

### <a name="required-roles"></a>必需的角色

Microsoft 在为 Azure AD 提供执行所需任务的最低权限的原则。 [查看可用的不同 Azure 角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)，并选择正确的角色以满足每个角色的需求。 某些角色可能需要在部署完成后暂时应用并删除。

| 业务角色| 业务任务| Azure AD 角色 |
|---|---|---|
| 咨询台管理员 | 通常限制为仅限制最终用户报告的问题并执行有限的任务，例如更改用户的密码、使刷新令牌失效以及监视服务运行状况。 | 支持管理员 |
| 标识管理员| 阅读 Azure AD 登录报表和审核日志以调试应用程序代理相关的问题。| 安全读取者 |
| 应用程序所有者| 创建和管理企业应用程序、应用程序注册和应用程序代理设置的所有方面。| 应用程序管理员 |
| 基础结构管理 | 证书滚动更新所有者 | 应用程序管理员 |

最大程度地减少有权访问安全信息或资源的人员的数量，有助于减少恶意执行组件获取未经授权的访问或被授权的用户无意中影响敏感资源的可能性。

但是，用户仍需要执行日常的特权操作，因此强制基于实时（JIT）的[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)策略来提供对 Azure 资源和 Azure AD 的按需权限访问，这是我们建议的方法，以便有效地管理管理访问和审核。

### <a name="reporting-and-monitoring"></a>报告和监视

Azure AD 通过[审核日志和报告，](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)更深入地了解组织的应用程序使用情况和操作运行状况。 应用程序代理还可以轻松地从 Azure AD 门户和 Windows 事件日志监视连接器。

#### <a name="application-audit-logs"></a>应用程序审核日志

这些日志提供有关通过应用程序代理和设备以及访问应用程序的用户的应用程序登录的详细信息。 [审核日志](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)位于 "Azure 门户" 和 "[审核 API](https://docs.microsoft.com/graph/api/resources/directoryaudit?view=graph-rest-beta) " 中的 "导出"。 此外，应用程序还可以[使用 "使用情况" 和 "见解报表](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context)"。

#### <a name="application-proxy-connector-monitoring"></a>应用程序代理连接器监视

连接器和服务负责处理所有的高可用性任务。 可以通过 Azure AD 门户中的 "应用程序代理" 页来监视连接器的状态。 有关连接器维护的详细信息，请参阅[了解 Azure AD 应用程序代理连接器](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#maintenance)。

![示例： Azure AD 应用程序代理连接器](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Windows 事件日志和性能计数器

连接器具有管理日志和会话日志。 管理日志包括关键事件及其错误。 会话日志包括所有事务及其处理详细信息。 日志和计数器位于 Windows 事件日志中。有关详细信息，请参阅[了解 Azure AD 应用程序代理连接器](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#under-the-hood)。 按照本[教程进行操作，在 Azure Monitor 中配置事件日志数据源](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events)。

### <a name="troubleshooting-guide-and-steps"></a>疑难解答指南和步骤

详细了解常见问题，以及[如何解决这些](application-proxy-troubleshoot.md)问题。

以下文章介绍了一些常见方案，这些方案还可用于为你的支持组织创建故障排除指南。

* [显示应用页时出现问题](application-proxy-page-appearance-broken-problem.md)
* [应用程序加载时间过长](application-proxy-page-load-speed-problem.md)
* [应用程序页上的链接不起作用](application-proxy-page-links-broken-problem.md)
* [要为应用打开哪些端口](application-proxy-connectivity-ports-how-to.md)
* [应用的连接器组中没有正常工作的连接器](application-proxy-connectivity-no-working-connector.md)
* [在管理门户中配置](application-proxy-config-how-to.md)
* [为应用配置单一登录](application-proxy-config-sso-how-to.md)
* [在管理门户中创建应用时出现问题](application-proxy-config-problem.md)
* [配置 Kerberos 约束委派](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [使用 PingAccess 配置](application-proxy-back-end-ping-access-how-to.md)
* [“无法访问此企业应用程序”错误](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [安装应用程序代理程序连接器时出现问题](application-proxy-connector-installation-problem.md)
* [登录问题](application-sign-in-problem-on-premises-application-proxy.md)
