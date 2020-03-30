---
title: 规划 Azure 活动目录应用程序代理部署
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
ms.reviewer: ''
ms.openlocfilehash: edd607c4d708df9fcfd3cbd5fdb71f0a7652d6c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330913"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>规划 Azure AD 应用程序代理部署

Azure 活动目录 （Azure AD） 应用程序代理是本地应用程序的安全且经济高效的远程访问解决方案。 它为"云优先"组织提供了一个即时过渡路径，用于管理对尚未能够使用现代协议的旧式本地应用程序的访问。 有关其他介绍性信息，请参阅[什么是应用程序代理](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)。

建议使用应用程序代理来允许远程用户访问内部资源。 应用程序代理取代了这些远程访问用例对 VPN 或反向代理的需求。 它不适用于公司网络上的用户。 这些使用应用程序代理进行 Intranet 访问的用户可能会遇到不良的性能问题。

本文包括规划、操作和管理 Azure AD 应用程序代理所需的资源。 

## <a name="plan-your-implementation"></a>规划实施

以下部分提供了关键规划元素的广泛视图，这些元素将为您设置高效的部署体验。 

### <a name="prerequisites"></a>先决条件

在开始实现之前，您需要满足以下先决条件。 在[本教程](application-proxy-add-on-premises-application.md)中，您可以看到有关设置环境的详细信息，包括这些先决条件。

* **连接器**：连接器是轻量级代理，您可以部署到：
   * 本地物理硬件
   * 托管在任何虚拟机管理程序解决方案中的 VM
   * 托管在 Azure 中的 VM，用于启用到应用程序代理服务的出站连接。

* 有关更详细的概述[，请参阅了解 Azure AD 应用代理连接器](application-proxy-connectors.md)。

     * 在安装连接器之前，[必须为 TLS 1.2 启用](application-proxy-add-on-premises-application.md)连接器计算机。

     * 如果可能，请将连接器部署在同一[网络中](application-proxy-network-topology.md)，并将连接器与后端 Web 应用程序服务器进行段化。 最好在完成应用程序的发现后部署连接器。
     * 我们建议每个连接器组至少有两个连接器，以提供高可用性和扩展。 拥有三个连接器是最佳选择，以防您随时需要维修机器。 查看[连接器容量表](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning)，以帮助确定要安装连接器的计算机类型。 机器越大，缓冲器和执行力就越大。

* **网络访问设置**：Azure AD 应用程序代理连接器[通过 HTTPS（TCP 端口 443）和 HTTP（TCP 端口 80）连接到 Azure。](application-proxy-add-on-premises-application.md) 

   * 不支持终止连接器 TLS 流量，并将阻止连接器使用其各自的 Azure 应用代理终结点建立安全通道。

   * 避免对连接器和 Azure 之间的出站 TLS 通信进行各种形式的内联检查。 连接器和后端应用程序之间可以进行内部检查，但可能会降低用户体验，因此不建议这样做。

   * 连接器本身的负载平衡也不受支持，甚至是必需的。

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>配置 Azure AD 应用程序代理之前的重要注意事项

为了配置和实现 Azure AD 应用程序代理，必须满足以下核心要求。

*  **Azure 载入**：在部署应用程序代理之前，必须从本地目录同步用户标识，或直接在 Azure AD 租户中创建。 标识同步允许 Azure AD 在授予用户对应用代理发布的应用程序的访问权限之前对用户进行预身份验证，并获得执行单一登录 (SSO) 所需的用户标识符信息。

* **条件访问要求**：我们不建议使用应用程序代理进行 Intranet 访问，因为这会增加影响用户的延迟。 我们建议将应用程序代理用于从 Internet 进行远程访问的预身份验证和条件访问策略。  为 Intranet 使用提供条件访问的方法是使应用程序现代化，以便它们可以直接使用 AAD 进行身份验证。 有关详细信息，请参阅[参考参考资料将应用程序迁移到 AAD。](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) 

* **服务限制**：为了防止单个租户过度消耗资源，每个应用程序和租户都设置了限制限制。 要查看这些限制，请参阅[Azure AD 服务限制和限制](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)。 这些限制限制基于远高于典型使用量的基准，并为大多数部署提供了充足的缓冲区。

* **公共证书**：如果您使用的是自定义域名，则必须采购 TLS/SSL 证书。 根据您的组织要求，获取证书可能需要一些时间，我们建议尽早开始该过程。 Azure 应用程序代理支持基于 SAN 的标准证书、[通配符](application-proxy-wildcard.md)或基于 SAN 的证书。 有关详细信息，请参阅[使用 Azure AD 应用程序代理配置自定义域](application-proxy-configure-custom-domain.md)。

* **域要求**：使用 Kerberos 约束委派 （KCD） 对已发布的应用程序进行单一登录，要求运行连接器的服务器和运行应用的服务器都加入域，并加入同一域或信任域的一部分。
有关该主题的详细信息，请参阅[KCD，了解使用应用程序代理的单次登录](application-proxy-configure-single-sign-on-with-kcd.md)。 连接器服务在本地系统的上下文中运行，不应配置为使用自定义标识。

* **URL 的 DNS 记录**

   * 在应用程序代理中使用自定义域之前，必须在公共 DNS 中创建 CNAME 记录，允许客户端将自定义定义的外部 URL 解析为预定义的应用程序代理地址。 未能为使用自定义域的应用程序创建 CNAME 记录将阻止远程用户连接到该应用程序。 添加 CNAME 记录所需的步骤可能因 DNS 提供程序而异，因此了解如何使用[Azure 门户管理 DNS 记录和记录集](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal)。

   * 同样，连接器主机必须能够解析要发布的应用程序的内部 URL。

* **行政权利和角色**

   * **连接器安装**需要对正在安装它的 Windows 服务器的本地管理员权限。 它还需要至少应用程序*管理员*角色来对连接器实例进行身份验证并将注册到 Azure AD 租户。 

   * **应用程序发布和管理**需要*应用程序管理员*角色。 应用程序管理员可以管理目录中的所有应用程序，包括注册、SSO 设置、用户和组分配和许可、应用程序代理设置和同意。 它不能授予管理条件访问的能力。 *云应用程序管理员*角色具有应用程序管理员的所有功能，只不过它不允许管理应用程序代理设置。

* **许可**：应用程序代理可通过 Azure AD 高级订阅提供。 有关许可选项和功能的完整列表，请参阅[Azure 活动目录定价页](https://azure.microsoft.com/pricing/details/active-directory/)。  

### <a name="application-discovery"></a>应用程序发现

通过收集以下信息，编制通过应用程序代理发布的所有在册应用程序清单：

| 信息类型| 要收集的信息 |
|---|---|
| 服务类型| 例如：共享点、SAP、CRM、自定义 Web 应用程序、API |
| 应用程序平台 | 例如：Windows IIS、Linux 上的 Apache、Tomcat、NGINX |
| 域成员身份| Web 服务器完全限定的域名 （FQDN） |
| 应用程序位置 | Web 服务器或服务器场位于基础结构中的位置 |
| 内部访问 | 在内部访问应用程序时使用的确切 URL。 <br> 如果服务器场正在使用哪种类型的负载平衡？ <br> 应用程序是否从自身以外的源提取内容。<br> 确定应用程序是否通过 WebSocket 运行。 |
| 外部访问 | 应用程序已对外公开的供应商解决方案。 <br> 要用于外部访问的 URL。 如果 SharePoint，请确保根据[本指南](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings)配置备用访问映射。 如果没有，则需要定义外部 URL。 |
| 公用证书 | 如果使用自定义域，则采购具有相应主题名称的证书。 如果存在证书，请注意序列号和位置，从何处可以获取证书。 |
| 身份验证类型| 应用程序支持支持的身份验证类型，如基本身份验证、Windows 集成身份验证、基于窗体、基于标头和声明。 <br>如果应用程序配置为在特定域帐户下运行，请注意服务帐户的完全限定域名 （FQDN）。<br> 如果基于 SAML，则标识符和回复 URL。 <br> 如果基于标头，则供应商解决方案和处理身份验证类型的特定要求。 |
| 连接器组名称 | 将指定为此后端应用程序提供导管和 SSO 的连接器组的逻辑名称。 |
| 用户/组访问 | 将被授予应用程序外部访问权限的用户或用户组。 |
| 其他需求 | 请注意在发布应用程序时应考虑的任何其他远程访问或安全要求。 |

您可以下载此[应用程序清单电子表格](https://aka.ms/appdiscovery)来清点您的应用程序。

### <a name="define-organizational-requirements"></a>定义组织要求

以下是您应定义组织的业务需求的领域。 每个区域都包含需求示例

 **访问**

* 具有域加入或 Azure AD 联接设备的远程用户可以通过无缝的单一登录 （SSO） 安全地访问已发布的应用程序。

* 具有已批准个人设备的远程用户可以安全地访问已发布的应用程序，前提是他们已注册在 MFA 中，并在其移动电话上注册了 Microsoft 身份验证器应用作为身份验证方法。

**治理** 

* 管理员可以定义和监视用户分配到通过应用程序代理发布的应用程序的生命周期。

**安全性**

* 只有通过组成员身份或单独分配给应用程序的用户可以访问这些应用程序。

**性能**

* 与从内部网络访问应用程序相比，应用程序性能没有下降。

**用户体验**

* 用户知道如何在任何设备平台上使用熟悉的公司 URL 来访问其应用程序。

**审核**
* 管理员能够审核用户访问活动。


### <a name="best-practices-for-a-pilot"></a>飞行员的最佳做法

确定使用单一登录 （SSO） 完全调试单个应用程序进行远程访问所需的时间和精力。 为此，运行一个考虑其初始发现、发布和常规测试的试验。 使用已预配置为集成 Windows 身份验证 （IWA） 的简单基于 IIS 的 Web 应用程序将有助于建立基线，因为此设置只需很少的努力才能成功引导远程访问和 SSO。

以下设计元素应直接在生产租户中提高试验实现的成功。  

**连接器管理**：  

* 连接器在为您的应用程序提供本地管道方面发挥着关键作用。 使用**默认**连接器组在将已发布应用程序调试到生产之前，足以进行初始试验测试。 然后，可以将成功测试的应用程序移动到生产连接器组。

**应用程序管理**：

* 您的员工最有可能记住外部 URL 是熟悉且相关的。 避免使用预定义的msappproxy.net或onmicrosoft.com后缀发布应用程序。 相反，提供一个熟悉的顶级验证域，预缀于逻辑主机名，如*intranet.<customers_domain>.com*。

* 通过将引导应用程序图标隐藏在 Azure MyApps 门户中的启动图标，将试用应用程序图标的可见性限制为试验组。 准备生产时，可以将应用限定为其各自的目标受众（在同一生产前租户中）或在生产租户中发布应用程序。

**单一登录设置**：某些 SSO 设置具有特定的依赖项，可能需要一些时间来设置，因此通过确保提前解决依赖项来避免更改控制延迟。 这包括域联接连接器主机，用于使用 Kerberos 约束委派 （KCD） 执行 SSO，并处理其他耗时的活动。 例如，如果需要基于标头的 SSO，则设置 PING Access 实例。

**连接器主机和目标应用程序之间的 TLS：** 安全性至关重要，因此应始终使用连接器主机和目标应用程序之间的 TLS。 特别是如果 Web 应用程序配置为基于表单的身份验证 （FBA），因为用户凭据随后以明文有效传输。

**逐步实现并测试每个步骤**。 发布应用程序后执行基本功能测试，以确保满足所有用户和业务需求，请按照以下说明操作：

1. 在禁用预身份验证后，测试和验证对 Web 应用程序的总体访问。
2. 如果成功启用预身份验证并分配用户和组。 测试和验证访问。
3. 然后为应用程序添加 SSO 方法，然后再次测试以验证访问。
4. 根据需要应用条件访问和 MFA 策略。 测试和验证访问。

**故障排除工具**：故障排除时，始终从连接器主机上的浏览器验证对已发布应用程序的访问权限，并确认应用程序按预期运行。 设置越简单，就越容易确定根本原因，因此请考虑尝试使用最小配置重现问题，例如仅使用单个连接器和不使用 SSO。 在某些情况下，Web 调试工具（如 Telerik 的 Fiddler）对于解决通过代理访问的应用程序中的访问或内容问题进行故障排除可能是必不可少的。 Fiddler 还可以充当代理，帮助跟踪和调试移动平台（如 iOS 和 Android）的流量，以及几乎任何可以配置为通过代理路由的流量。 有关详细信息，请参阅[故障排除指南](application-proxy-troubleshoot.md)。

## <a name="implement-your-solution"></a>实施您的解决方案

### <a name="deploy-application-proxy"></a>部署应用程序代理

本教程中介绍了部署应用程序代理的步骤[，用于添加用于远程访问的本地应用程序](application-proxy-add-on-premises-application.md)。 如果安装不成功，请在门户中选择 **"疑难解答应用程序代理"，** 或使用故障排除指南[解决安装应用程序代理代理连接器时的问题](application-proxy-connector-installation-problem.md)。

### <a name="publish-applications-via-application-proxy"></a>通过应用程序代理发布应用程序

发布应用程序假定您已满足所有先决条件，并且在应用程序代理页中显示多个连接器为已注册和活动。

您还可以使用[PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)发布应用程序。

以下是发布应用程序时应遵循的一些最佳做法：

* **使用连接器组**：分配已指定用于发布每个子应用程序的连接器组。 我们建议每个连接器组至少有两个连接器，以提供高可用性和扩展。 拥有三个连接器是最佳选择，以防您随时需要维修机器。 此外，请参阅[使用连接器组在单独的网络和位置上发布应用程序](application-proxy-connector-groups.md)，了解如何使用连接器组按网络或位置对连接器进行分段。

* **设置后端应用程序超时**：此设置在应用程序可能需要超过 75 秒来处理客户端事务的情况下非常有用。 例如，当客户端向充当数据库前端的 Web 应用程序发送查询时。 前端将此查询发送到其后端数据库服务器并等待响应，但当它收到响应时，会话的客户端超时。将超时设置为 Long 提供了 180 秒才能完成的较长事务。

* **使用适当的 Cookie 类型**

   * **HTTP-仅 Cookie**： 通过让应用程序代理在设置 Cookie HTTP 响应标头中包含 HTTPOnly 标志来提供其他安全性。 此设置有助于缓解跨站点脚本 （XSS） 等漏洞。 对于确实需要访问会话 Cookie 的客户端/用户代理，请保留此集为"否"。 例如，RDP/MTSC 客户端连接到通过应用代理发布的远程桌面网关。

   * **安全 Cookie：** 当使用 Secure 属性设置 Cookie 时，用户代理（客户端应用）将仅在通过 TLS 安全通道传输请求时在 HTTP 请求中包含 Cookie。 这有助于降低 Cookie 在明文通道上泄露的风险，因此应启用。

   * **持久性 Cookie**： 允许应用程序代理会话 Cookie 在浏览器关闭之间保留，直到过期或被删除。 用于大量应用程序（如 Office）访问已发布的 Web 应用程序中的文档，而无需重新提示用户进行身份验证的情况。 但是，请谨慎启用，因为持久性 Cookie 最终可能会使服务面临未经授权的访问风险，如果不与其他补偿控制一起使用。 此设置应仅用于无法在进程之间共享 Cookie 的旧应用程序。 最好更新应用程序以处理进程之间的共享 Cookie，而不是使用此设置。

* **在标头中翻译 URL：** 对于无法将内部 DNS 配置为与组织的公共命名空间（即拆分 DNS）匹配的方案启用此选项。 除非应用程序在客户端请求中需要原始主机标头，否则请将此值设置为"是"。 另一种方法是让连接器使用内部 URL 中的 FQDN 路由实际流量，在外部 URL 中使用 FQDN 作为主机头。 在大多数情况下，此替代方法应允许应用程序在远程访问时正常运行，但用户失去了在外部 URL 中匹配&的好处。

* **在应用程序正文中翻译 URL：** 当您希望将该应用程序中的链接翻译成响应返回客户端时，请打开应用的应用程序正文链接转换。 如果启用，此函数将尽力尝试翻译应用代理在 HTML 中找到的所有内部链接，并将 CSS 响应返回给客户端。 在发布包含内容中硬编码绝对或 NetBIOS 短名称链接的应用或包含链接到其他本地应用程序的内容的应用时，它非常有用。

对于已发布的应用链接到其他已发布应用的方案，请为每个应用程序启用链接转换，以便您能够控制每个应用级别的用户体验。

例如，假设您有三个应用程序通过应用程序代理发布，所有这些应用程序都相互链接：福利、费用和差旅，以及第四个应用程序"反馈"，这些应用程序不是通过应用程序代理发布的。

![图片 1](media/App-proxy-deployment-plan/link-translation.png)

当您为"福利"应用启用链接转换时，指向"支出"和"差旅"的链接将重定向到这些应用的外部 URL，以便从公司网络外部访问应用程序的用户可以访问它们。 从"支出"和"旅行"到"福利"的链接不起作用，因为尚未为这两个应用启用链接转换。 反馈的链接不会重定向，因为没有外部 URL，因此使用"福利"应用的用户将无法从公司网络外部访问反馈应用。 请参阅[有关链接翻译和其他重定向选项](application-proxy-configure-hard-coded-link-translation.md)的详细信息。

### <a name="access-your-application"></a>访问您的应用程序

存在几种选项来管理对应用代理已发布资源的访问权限，因此请选择最适合给定方案和可伸缩性需求的选项。 常见方法包括：使用通过 Azure AD Connect 同步的本地组、基于用户属性在 Azure AD 中创建动态组、使用资源所有者管理的自助服务组，或所有这些组合。 有关每个资源的好处，请参阅链接资源。

向用户分配对应用程序的访问权限的最直接方法是从已发布应用程序的左侧窗格进入 **"用户和组"** 选项，并直接分配组或个人。

![图片 24](media/App-proxy-deployment-plan/add-user.png)

还可以允许用户通过分配他们当前不是其成员的组来自助访问应用程序，并配置自助服务选项。

![图片 25](media/App-proxy-deployment-plan/allow-access.png)

如果启用，用户将能够登录到 MyApps 门户并请求访问，并且可以自动批准并添加到已允许的自助服务组，或者需要指定审批方的批准。

还可以邀请来宾用户[访问通过 Azure AD B2B 通过应用程序代理发布的内部应用程序](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker)。

对于通常匿名访问且不需要身份验证的本地应用程序，您可能更喜欢禁用位于应用程序**属性**中的选项。

![图片 26](media/App-proxy-deployment-plan/assignment-required.png)


将此选项设置为"否"允许用户在没有权限的情况下通过 Azure AD 应用代理访问本地应用程序，因此请谨慎使用。

应用程序发布后，应在浏览器中键入其外部 URL 或在其图标上[https://myapps.microsoft.com](https://myapps.microsoft.com/)访问它。

### <a name="enable-pre-authentication"></a>启用预身份验证

验证应用程序是否可通过应用程序代理通过外部 URL 访问它。 

1. 导航到**Azure 活动目录** > **企业应用程序** > **所有应用程序**并选择要管理的应用。

2. 选择“应用程序代理”****。

3. 在 **"预身份验证"** 字段中，使用下拉列表选择**Azure 活动目录**，然后选择 **"保存**"。

启用预身份验证后，Azure AD 将首先向用户发起身份验证挑战，如果配置了单一登录，则后端应用程序还将在授予对应用程序的访问之前验证用户。 将预身份验证模式从"直通"更改为 Azure AD 还会使用 HTTPS 配置外部 URL，因此现在使用 HTTPS 保护最初为 HTTPS 配置的任何应用程序。

### <a name="enable-single-sign-on"></a>启用单一登录

SSO 提供了最佳的用户体验和安全性，因为用户在访问 Azure AD 时只需登录一次。 用户经过预身份验证后，SSO 将由应用程序代理连接器代表用户执行对本地应用程序进行身份验证。 后端应用程序处理登录，就好像它是用户本身一样。 

选择 **"传递"** 选项允许用户访问已发布的应用程序，而无需对 Azure AD 进行身份验证。

仅当 Azure AD 能够标识请求访问资源的用户时，才能执行 SSO，因此，应用程序必须在访问 SSO 时将应用程序配置为使用 Azure AD 对用户进行预身份验证才能运行，否则将禁用 SSO 选项。

读取[Azure AD 中的应用程序的单一登录](what-is-single-sign-on.md)，以帮助您在配置应用程序时选择最合适的 SSO 方法。

###  <a name="working-with-other-types-of-applications"></a>使用其他类型的应用程序

Azure AD 应用程序代理还可以支持已开发以使用 Azure AD 身份验证库[（ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)） 或 Microsoft 身份验证库[（MSAL）](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/)的应用程序。 它支持本机客户端应用，使用客户端请求标头信息中收到的 Azure AD 颁发的令牌，以便代表用户执行预身份验证。

阅读[发布本机和移动客户端应用](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client)以及[基于声明的应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps)，了解应用程序代理的可用配置。

### <a name="use-conditional-access-to-strengthen-security"></a>使用条件访问加强安全性

应用程序安全需要一套高级的安全功能，这些功能可以抵御和响应本地和云中的复杂威胁。 攻击者通常通过弱、默认或被盗的用户凭据获得公司网络访问权限。  Microsoft 身份驱动的安全性通过管理和保护特权和非特权标识来减少被盗凭据的使用。

以下功能可用于支持 Azure AD 应用程序代理：

* 基于地理位置的条件访问：通过限制基于地理位置的用户访问或具有[基于位置的条件访问策略的](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)IP 地址来保护敏感数据。

* 基于设备的条件访问：确保只有已注册、已批准和合规的设备才能使用[基于设备的条件访问](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications)访问访问公司数据。

* 基于应用程序的条件访问：当用户不在公司网络上时，不必停止工作。 [通过条件访问安全访问企业云和本地应用](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)并保持控制。

* 基于风险的条件访问：使用[基于风险的条件访问策略](https://www.microsoft.com/cloud-platform/conditional-access)保护您的数据免受恶意黑客的攻击，该策略可应用于所有应用和所有用户，无论是本地还是云中。

* Azure AD 访问面板：部署应用程序代理服务并安全地发布应用程序后，为用户提供一个简单的中心来发现和访问其所有应用程序。 通过自助服务功能提高工作效率，例如通过[访问面板](https://aka.ms/AccessPanelDPDownload)请求访问新应用和组或代表他人管理对这些资源的访问。

## <a name="manage-your-implementation"></a>管理您的实现

### <a name="required-roles"></a>所需角色

Microsoft 主张授予使用 Azure AD 执行所需任务的权限尽可能少的原则。 [查看可用的不同 Azure 角色，](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)并选择正确的角色来满足每个角色的需求。 某些角色可能需要临时应用并在部署完成后删除。

| 业务角色| 业务任务| Azure AD 角色 |
|---|---|---|
| 帮助台管理员 | 通常仅限于限定最终用户报告的问题和执行有限的任务，如更改用户的密码、无效刷新令牌和监视服务运行状况。 | 支持管理员 |
| 身份管理员| 读取 Azure AD 登录报告和审核日志以调试应用代理相关问题。| 安全读取者 |
| 应用程序所有者| 创建和管理企业应用程序、应用程序注册和应用程序代理设置的所有方面。| 应用程序管理员 |
| 基础设施管理员 | 证书滚动所有者 | 应用程序管理员 |

尽量减少有权访问安全信息或资源的人数将有助于减少恶意参与者获取未经授权的访问权限或授权用户无意中影响敏感资源的可能性。 
 
但是，用户仍然需要执行日常的特权操作，因此强制实施基于实时 （JIT）[的特权标识管理](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)策略，以便按需对 Azure 资源和 Azure AD 提供特权访问，是我们有效管理管理访问和审核的建议方法。

### <a name="reporting-and-monitoring"></a>报告和监视

Azure AD 通过[审核日志和报告](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)，提供了对组织应用程序使用情况和操作运行状况的其他见解。 应用程序代理还使从 Azure AD 门户和 Windows 事件日志监视连接器变得非常容易。

#### <a name="application-audit-logs"></a>应用程序审核日志

这些日志提供有关使用应用程序代理配置的应用程序以及设备和访问应用程序的用户登录的详细信息。 [审核日志](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)位于 Azure 门户和审核[API](https://docs.microsoft.com/graph/api/resources/directoryaudit?view=graph-rest-beta)中，用于导出。 此外，[您的应用程序还提供使用情况和见解报告](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。

#### <a name="application-proxy-connector-monitoring"></a>应用程序代理连接器监控

连接器和服务负责处理所有的高可用性任务。 可以从 Azure AD 门户中的应用程序代理页监视连接器的状态。 有关连接器维护的详细信息，[请参阅 Azure AD 应用程序代理连接器](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#maintenance)。

![示例：Azure AD 应用程序代理连接器](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Windows 事件日志和性能计数器

连接器同时具有管理员和会话日志。 管理日志包括关键事件及其错误。 会话日志包括所有事务及其处理详细信息。 日志和计数器位于 Windows 事件日志中，有关详细信息，[请参阅了解 Azure AD 应用程序代理连接器](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#under-the-hood)。 请按照[本教程在 Azure 监视器 中配置事件日志数据源](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events)。

### <a name="troubleshooting-guide-and-steps"></a>故障排除指南和步骤

通过我们的[错误消息故障排除](application-proxy-troubleshoot.md)指南，详细了解常见问题以及如何解决这些问题。 

以下文章介绍常见方案，这些方案还可用于为支持组织创建故障排除指南。 

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
