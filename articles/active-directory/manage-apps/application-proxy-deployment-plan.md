---
title: 规划 Azure Active Directory 应用程序代理部署
description: 规划您的组织内的应用程序代理的部署的端到端指南
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04-04-2019
ms.author: barbaraselden
ms.reviewer: ''
ms.openlocfilehash: d8686b9296c8b1d7c5232e2e46a0e66a9896656b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113028"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>计划的 Azure AD 应用程序代理部署

Azure Active Directory (Azure AD) 应用程序代理是在本地应用程序的安全且经济高效的远程访问解决方案。 它提供了"云优先"的组织可以管理对旧的访问的直接转换路径上的本地应用程序还没有准备好能够使用现代的协议。 有关介绍性的其他信息，请参阅[什么是应用程序代理](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)。

对于允许远程用户访问内部资源，建议应用程序代理。 需要使用 VPN 或反向代理，这些远程访问使用情况下，将替换应用程序代理。 这不被专为企业网络上的用户。 使用应用程序代理进行 intranet 访问这些用户可能会遇到意外性能问题。

本文包括计划、 操作和管理 Azure AD 应用程序代理所需资源。 

## <a name="plan-your-implementation"></a>规划实施

以下部分提供了规划将为有效的部署体验进行设置的元素的键的广泛视图。 

### <a name="prerequisites"></a>必备组件

您需要在开始您的实现之前满足以下先决条件。 您可以设置你的环境，其中这些系统必备组件，包括在此查看详细信息[教程](application-proxy-add-on-premises-application.md)。

* **连接器**:连接器是可以将部署到的轻型代理：
   * 物理硬件在本地
   * 任何虚拟机监控程序解决方案中托管的虚拟机
   * 若要启用到应用程序代理服务的出站连接的 Azure 中托管的 VM。

* 请参阅[了解 Azure AD 应用代理连接器](application-proxy-connectors.md)的更详细的概述。

     * 连接器的计算机必须[启用 TLS 1.2](application-proxy-add-on-premises-application.md)之前安装连接器。

     * 如果可能，请在中部署连接器[同一个网络](application-proxy-network-topology.md)和作为后端 web 应用程序服务器的段。 最好是部署连接器后完成的应用程序的发现。
     * 我们建议每个连接器组具有至少两个连接器，以便提供高可用性和缩放。 可能需要在任意点的计算机提供服务的情况下，具有三个连接器是最佳的。 审阅[连接器容量表](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning)来帮助确定在哪种类型的计算机上安装的连接器。 越大机更多的缓冲区和高性能将连接器。

* **网络访问设置**:Azure AD 应用程序代理连接器[通过 HTTPS (TCP 端口 443) 和 HTTP (TCP 端口 80) 连接到 Azure](application-proxy-add-on-premises-application.md)。 

   * 终止连接器 TLS 流量不受支持，并且将阻止连接器建立安全通道使用其各自的 Azure 应用代理终结点。

   * 避免在连接器与 Azure 之间的出站 TLS 通信的内联检查的所有窗体。 连接器和后端应用程序之间的内部检查是可能的但可能会降低用户体验，并在这种情况下，不建议。

   * 负载平衡的连接器本身也不是受支持，或甚至有必要。

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>配置 Azure AD 应用程序代理之前的重要注意事项

若要配置和实现 Azure AD 应用程序代理，必须满足以下的核心要求。

*  **Azure 载入**:在部署之前应用程序代理，必须从本地目录同步或直接在 Azure AD 租户中创建的用户标识。 标识同步使 Azure AD 进行预身份验证用户，才能授予其访问应用程序代理发布的应用程序和拥有必要的用户标识符信息才能执行单一登录 (SSO)。

* **条件性访问要求**:不建议使用 intranet 访问应用程序代理，因为这会增加将影响用户的延迟。 我们建议使用预身份验证和条件性访问策略的应用程序代理用于从 internet 进行远程访问。  一个方法，以提供 intranet 中使用的条件性访问是更新应用程序，以便他们可以 diretly 使用 AAD 进行身份验证。 请参阅[资源迁移到 AAD 应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources)有关详细信息。 

* **服务限制**:若要防范的各个用户存在资源过度消耗是每个应用程序和租户设置的限制。 若要查看这些限制是指[Azure AD 服务限制和局限性](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)。 这些限制基于对远高于典型的用法卷基准，大多数部署提供足够的缓冲区。

* **公共证书**:如果使用自定义域名，你必须购买非 Microsoft 受信任的证书颁发机构颁发的公共证书。 具体取决于您的组织要求，获得证书可能需要一些时间，我们建议在开始尽可能，尽早过程。 Azure 应用程序代理支持标准[通配符](application-proxy-wildcard.md)，或基于 SAN 的证书。

* **域要求**:单一登录方式登录到发布的应用程序使用 Kerberos 约束委派 (KCD) 需要将连接器主机是域加入要发布的应用程序所在的同一 AD 域。 本主题的详细信息，请参阅[进行单一登录的 KCD](application-proxy-configure-single-sign-on-with-kcd.md)使用应用程序代理。 连接器服务的本地系统上下文中运行，并不应配置为使用自定义标识。

* **Url 的 DNS 记录**

   * 使用应用程序代理中的自定义域之前必须在允许客户端解析为预定义的应用程序代理地址的自定义定义的外部 URL 的公共 DNS 中创建 CNAME 记录。 无法创建应用程序使用自定义域的 CNAME 记录将阻止远程用户连接到该应用程序。 因此，添加 CNAME 记录可以有所不同 DNS 提供商，所需的步骤了解如何[使用 Azure 门户管理 DNS 记录和记录集](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal)。

   * 同样，连接器主机必须能够解析要发布的应用程序的内部 URL。

* **管理权限和角色**

   * **连接器安装**需要到 Windows 服务器上安装的本地管理员权限。 它还需要最少*应用程序管理员*角色以进行身份验证，并将连接器实例注册到 Azure AD 租户。 

   * **应用程序发布和管理**需要*应用程序管理员*角色。 应用程序管理员可以管理包括注册、 SSO 设置、 用户和组分配和授权、 应用程序代理设置和许可的目录中的所有应用程序。 它不会授予管理条件访问的能力。 *云应用程序管理员*角色具有所有功能的应用程序管理员中，只是它不允许应用程序代理设置的管理。

* **许可**：应用程序代理是通过 Azure AD 基本版订阅提供。 请参阅[Azure Active Directory 定价页](https://azure.microsoft.com/pricing/details/active-directory/)有关的授权选项和功能的完整列表。  

### <a name="application-discovery"></a>应用程序发现

编译通过应用程序代理收集以下信息要发布的所有范围内应用程序的清单：

| 信息类型| 若要收集的信息 |
|---|---|
| 服务类型| 例如:SharePoint、 SAP、 CRM、 自定义 Web 应用程序、 API |
| 应用程序平台 | 例如:Windows IIS、 Apache 在 Linux、 Tomcat、 NGINX |
| 域成员身份| Web 服务器的完全限定的域名 (FQDN) |
| 应用程序的位置 | Web 服务器或场基础结构中位于何处 |
| 内部访问 | 在内部访问应用程序时，使用正确的 URL。 <br> 如果场中，哪种类型的负载平衡是在使用？ <br> 是否在应用程序从非自身的源绘制内容。<br> 确定是否应用程序通过 Websocket 进行操作。 |
| 外部访问 | 应用程序已从外部遭受供应商解决方案。 <br> 你想要使用的外部访问的 URL。 如果 SharePoint 中，确保每个配置备用访问映射[本指南](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings)。 如果没有，你将需要定义的外部 Url。 |
| 公共证书 | 如果使用自定义域，请购买具有相应的使用者名称的证书。 如果证书存在，则请注意的序列号和位置可获取。 |
| 身份验证类型| 支持 Basic、 Windows 集成身份验证、 基于窗体的、 基于标头和声明等的应用程序支持的身份验证类型。 <br>如果应用程序配置为在特定的域帐户下运行，请注意完全限定域 (FQDN) 的服务帐户。<br> 如果基于 SAML 的标识符和回复 Url。 <br> 如果基于标头的供应商解决方案和特定要求处理身份验证类型。 |
| 连接器组名称 | 将指定到此后端应用程序提供的管道和 SSO 的连接器组的逻辑名称。 |
| 用户/组访问权限 | 用户或用户组授予对应用程序的外部访问的。 |
| 其他要求 | 请注意任何其他远程访问或都应考虑到发布的应用程序的安全要求。 |

你可以下载这个[应用程序清单电子表格](https://aka.ms/appdiscovery)来清点您的应用程序。

### <a name="define-organizational-requirements"></a>定义组织的要求

以下是应为其定义你组织的业务要求的区域。 每个区包含要求的示例

 **Access**

* 远程用户使用已加入域或已加入 Azure AD 的设备用户可以访问发布的应用程序安全地使用无缝单一登录 (SSO)。

* 使用已批准的个人设备的远程用户可以安全地访问发布的应用程序，前提是它们在 MFA 中注册并且已注册其移动电话上的 Microsoft Authenticator 应用作为身份验证方法。

**管理** 

* 管理员可以定义和监视的用户分配到通过应用程序代理发布应用程序生命周期。

**安全性**

* 只有用户分配给通过组成员身份的应用程序或单独可以访问这些应用程序。

**性能**

* 没有任何性能下降的应用程序相较于访问内部网络中的应用程序。

**用户体验**

* 用户是知道如何通过在任何设备平台上使用熟悉的公司 Url 来访问其应用程序。

**审核**
* 管理员将能够审核用户访问活动。


### <a name="best-practices-for-a-pilot"></a>试验的最佳做法

确定时间和精力完全委员会进行远程访问，单一登录 (SSO) 的单个应用程序所需的量。 为此运行会考虑其初始发现、 发布和常规测试试验。 使用已预配置集成 Windows 身份验证 (IWA) 的简单的基于 IIS 的 web 应用程序将帮助建立基线，因为此安装程序需要最小精力来成功试验的远程访问和 SSO。

以下的设计元素应增加成功的试点实现直接在生产租户中。  

**连接器管理**:  

* 连接器提供到您的应用程序的本地管道至关重要。 使用**默认**连接器组足以满足初始试验的发布的应用程序之前测试到生产环境对它们进行调试。 已成功测试的应用程序然后可以移动到生产连接器组。

**应用程序管理**:

* 员工很有可能，请记住外部 URL 是熟悉且相关。 避免发布应用程序中使用我们的预定义的 msappproxy.net 或 onmicrosoft.com 后缀。 相反，提供熟悉顶级已验证的域，如逻辑的主机名作为前缀*intranet.< customers_domain >.com*。

* 限制对试验组的隐藏其启动图标形式在 Azure MyApps 门户试验应用程序的图标的可见性。 当准备好进行生产时，可以范围在相同的预生产租户中，或通过也在生产租户中发布应用程序及其相应目标受众到应用。

**单一登录设置**:某些 SSO 设置有可能需要时间来设置，因此应避免延迟通过确保依赖项进行寻址提前更改控件的特定依赖关系。 这包括域加入连接器主机来执行 SSO 使用 Kerberos 约束委派 (KCD) 并处理其他耗时的活动。 例如，设置 PING 访问实例，如果需要基于标头的 SSO。

**连接器主机和目标应用程序之间的 SSL**:安全性至关重要，因此，应始终使用连接器主机和目标应用程序之间的 TLS。 尤其是当 web 应用程序配置基于窗体的身份验证 (FBA)、 用户凭据以明文形式然后有效地传输。

**以增量方式实现和测试每个步骤**。 实施后发布的应用程序，以确保按照以下说明进行操作满足所有的用户和业务要求基本功能测试：

1. 测试和验证常规访问 web 应用程序使用预身份验证已禁用。
2. 如果成功，则启用预身份验证和分配用户和组。 测试和验证的访问。
3. 然后添加你的应用程序的 SSO 方法，并再次测试来验证的访问。
4. 将条件性访问和所需的 MFA 策略的应用。 测试和验证的访问。

**故障排除工具**:故障排除时，应始终首先从连接器主机上的浏览器验证到已发布的应用程序的访问，并确认应用程序能按预期方式。 越简单设置，更轻松地以确定根本原因，因此请考虑尝试重现问题的最小配置，如使用单个连接器和任何 SSO。 在某些情况下，web 调试 Telerik 的 Fiddler 等工具可以证明不可或缺通过代理访问的应用程序中的访问权限或内容问题进行疑难解答。 Fiddler 还可以充当代理来帮助跟踪和调试适用于 iOS 和 Android 等移动平台的流量，几乎任何内容，可以进行配置为通过代理路由。 请参阅[故障排除指南](application-proxy-troubleshoot.md)有关详细信息。

## <a name="implement-your-solution"></a>实现你的解决方案

### <a name="deploy-application-proxy"></a>部署应用程序代理

在此介绍的步骤部署应用程序代理[教程，了解添加为远程访问的本地应用程序](application-proxy-add-on-premises-application.md)。 如果安装不成功，则选择**应用程序代理故障排除**门户或使用故障排除指南[安装应用程序代理程序连接器出现问题](application-proxy-connector-installation-problem.md)。

### <a name="publish-applications-via-application-proxy"></a>通过应用程序代理发布应用程序

发布应用程序假定你已经满足所有先决条件，并且在应用程序代理页面中有多个连接器在注册时显示和处于活动状态。

此外可以通过使用发布的应用程序[PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)。

以下是在发布应用程序时应遵循一些最佳做法：

* **使用连接器组**:分配已被指定用于每个相应的应用程序发布的连接器组。 我们建议每个连接器组具有至少两个连接器，以便提供高可用性和缩放。 可能需要在任意点的计算机提供服务的情况下，具有三个连接器是最佳的。 此外，请参阅[发布应用程序在单独的网络和使用连接器组的位置上](application-proxy-connector-groups.md)若要查看如何还使用连接器组来划分网络或位置的连接器。

* **将后端应用程序超时值设置**:此设置是在应用程序需要超过 75 秒的时间来处理客户端事务的情况下很有用。 例如当客户端将查询发送到 web 应用程序，充当前端到数据库。 前端将此查询发送到后端数据库服务器并等待响应，但它收到的响应时，会话的客户端将会超时。将超时值设置以提供更长的事务的 180 秒，以完成长时间了。

* **使用适当的 Cookie 类型**

   * **仅限 HTTP Cookie**:通过让应用程序代理的 set-cookie HTTP 响应标头中包含 HTTPOnly 标志提供额外的安全性。 此设置有助于缓解攻击，例如跨站点脚本 (XSS)。 将此设置保留为否的客户端/用户代理，需要会话 cookie 的访问。 例如，连接到远程桌面网关的 RDP/MTSC 客户端通过应用代理发布。

   * **安全 Cookie**:当 cookie 设置具有安全特性时，用户代理 （客户端应用程序） 将仅包括该 cookie 在 HTTP 请求中如果通过 TLS 安全通道传输请求。 这有助于降低 cookie 通过明文通道，因此，应启用遭到入侵的风险。

   * **持久性 Cookie**:允许应用程序代理会话 cookie 来浏览器通过保持有效，直至过期或被删除的闭包之间保持不变。 用于方案的丰富的应用程序，例如 office 而无需重新提示进行身份验证的用户访问发布的 web 应用程序中的文档的位置。 谨慎启用但是，为持久性 cookie 可以最终会使服务面临风险的未经授权的访问，如果不与其他补偿控件结合使用。 此设置应仅用于不能共享进程之间的 cookie 的旧版应用程序。 它是更好的做法更新应用程序以处理而不是使用此设置的进程之间共享 cookie。

* **转换 Url 标头中的**:不能配置内部 DNS 以匹配组织的公共命名空间 （即拆分式 DNS） 的方案中启用此选项。 除非应用程序需要客户端请求中的原始主机标头，否则请保留此值设置为是。 替代方法是让实际流量和外部 URL，用作主机标头中的 FQDN 的路由中的内部 URL 使用 FQDN 的连接器。 在大多数情况下此替代方法应允许应用程序的功能正常，远程访问时，但你的用户丢失拥有匹配的内部和外部 URL 的好处。

* **转换应用程序主体中的 URL**：当你想从该应用程序在返回给客户端的响应中要转换的链接时打开的应用程序的应用程序正文链接转换。 如果启用，此函数提供了在将转换应用代理查找在 HTML 和 CSS 响应返回给客户端中的所有内部链接的最佳工作尝试。 当发布包含硬编码的绝对或在内容中，NetBIOS 短名称链接的应用或应用包含的内容的链接到其他本地应用程序时，它是非常有用。

链接到另一个已发布的应用在其中发布应用程序的情况下，启用链接转换每个应用程序，以便可以在每个应用级别控制用户体验。

例如，假设有三个通过应用程序代理发布的应用程序，这些应用程序均彼此链接：优点、 开支，和旅行，以及第四个应用，Feedback，但它不通过应用程序代理发布。

![图 1](media/App-proxy-deployment-plan/link-translation.png)

启用 Benefits 应用的链接转换，Expenses 和 Travel 的链接是重定向到这些应用程序的外部 Url，以便访问从企业网络外部的应用程序的用户可以访问它们。 从 Expenses 和 Travel 返回到优势的链接不起作用，因为尚未为这两个应用启用链接转换。 因为没有任何外部 URL，以便使用 Benefits 应用的用户将无法访问该反馈应用程序从企业网络之外，不是重定向 Feedback 的链接。 查看有关详细的信息[翻译和其他重定向选项链接](application-proxy-configure-hard-coded-link-translation.md)。

### <a name="access-your-application"></a>访问你的应用程序

存在多个管理访问选项到应用程序代理已发布的资源，因此请选择最适合于给定的方案和可伸缩性需求。 常用的方法包括： 使用通过 Azure AD Connect 是否已同步的本地组，在 Azure AD 中创建动态组基于用户属性，使用自助服务组管理的资源所有者或所有这些组合。 请参阅各自的好处的链接的资源。

将用户分配到应用程序的访问权限的最直接方式进入**用户和组**发布的应用程序和直接分配的组或个人的左侧窗格中的选项。

![图片 24](media/App-proxy-deployment-plan/add-user.png)

通过将它们当前不是成员的组分配和配置自助服务选项，还可以允许自助服务访问权限的用户到你的应用程序。

![图 25](media/App-proxy-deployment-plan/allow-access.png)

如果启用，用户将然后能够登录到 MyApps 门户并请求访问权限，并且可以被自动批准并从指定的审批添加到已允许自助服务组或需要审批。

也可以是来宾用户[受邀访问通过 Azure AD B2B 通过应用程序代理发布内部应用程序](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker)。

以匿名方式进行正常访问的本地应用程序，不需要进行任何身份验证，您可能宁愿使用禁用位于应用程序的选项**属性**。

![图 26](media/App-proxy-deployment-plan/assignment-required.png)


保留此选项设置为否可确保用户能够访问本地应用程序通过 Azure AD 应用代理不具有权限，请谨慎使用。

一旦发布应用程序，它应可访问通过键入其外部 URL 在浏览器中或在其图标[ https://myapps.microsoft.com ](https://myapps.microsoft.com/)。

### <a name="enable-pre-authentication"></a>启用预身份验证

验证你的应用程序通过外部 URL 对其进行访问的应用程序代理可以访问。 

1. 导航到“Azure Active Directory” > “企业应用程序” > “所有应用程序”，选择要管理的应用。

2. 选择“应用程序代理”。

3. 在中**预身份验证**字段中，使用下拉列表中选择**Azure Active Directory**，然后选择**保存**。

启用预身份验证，Azure AD 将身份验证的第一次质询用户，如果实现单一登录配置然后后端应用程序还将验证用户访问应用程序授予权限前。 从传递的预身份验证模式更改为 Azure AD 使用 HTTPS，还配置外部 URL，因此现在将使用 HTTPS 保护最初为 HTTP 配置的任何应用程序。

### <a name="enable-single-sign-on"></a>启用单一登录

SSO 提供最佳用户体验和安全性，因为用户只需登录一次访问 Azure AD 时。 后用户已预身份验证，将通过应用程序代理连接器进行身份验证的本地应用程序，代表用户执行 SSO。 后端应用程序，就好像用户自行处理登录名。 

选择**直通**选项允许用户访问发布的应用程序，而无需向 Azure AD 进行身份验证。

执行 SSO 才可能如果 Azure AD 可以确定请求资源的访问权限，因此你的应用程序，必须配置预用户进行身份验证与 Azure AD 访问时 sso 到函数的用户，否则 SSO 选项将被禁用。

读取[单一登录方式登录到 Azure AD 中的应用程序](what-is-single-sign-on.md)来帮助你配置你的应用程序时选择最合适的 SSO 方法。

###  <a name="working-with-other-types-of-applications"></a>使用其他类型的应用程序

Azure AD 应用程序代理还可以支持的应用程序已开发出使用我们的 Azure AD 身份验证库 ([ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)) 或 Microsoft 身份验证库 ([MSAL](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/))。 它通过使用 Azure AD 颁发的令牌执行代表用户的预身份验证的客户端请求的标头信息中收到支持本机客户端应用。

读取[本机和移动客户端应用程序发布](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client)并[基于声明的应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps)若要了解有关可用配置的应用程序代理。

### <a name="use-conditional-access-to-strengthen-security"></a>使用条件访问来增强安全性

应用程序安全性要求一组高级的安全功能，可以从保护和响应到复杂的威胁的本地和云中。 攻击者最常访问企业网络通过弱、 默认值或被盗的用户凭据。  Microsoft 标识驱动安全通过管理和保护特权和非特权标识，从而减少了使用的凭据被盗。

以下功能可用于支持 Azure AD 应用程序代理：

* 用户和基于位置的条件性访问：确保通过限制用户访问权限基于地理位置或 IP 地址与受保护的敏感数据[基于位置的条件性访问策略](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)。

* 基于设备的条件性访问：确保只有已注册和批准，并符合要求的设备可以访问企业数据[基于设备的条件性访问](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications)。

* 基于应用程序的条件性访问：不需要时用户不在公司网络上停止工作。 [安全访问公司在本地和云应用](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)使用条件性访问控制和维护。

* 基于风险的条件性访问：保护数据免受恶意黑客[基于风险的条件性访问策略](https://www.microsoft.com/cloud-platform/conditional-access)可应用到所有应用和所有用户，是否在本地或云中。

* Azure AD 访问面板：与您的部署，应用程序代理服务和安全地发布的应用程序，为用户提供一个简单的中心来发现和访问其应用程序。 通过自助服务功能，如请求访问新的应用程序和组，也可通过管理代表他人，对这些资源的访问权限的功能提高工作效率[访问面板](https://aka.ms/AccessPanelDPDownload)。

## <a name="manage-your-implementation"></a>管理您的实现

### <a name="required-roles"></a>所需的角色

Microsoft 大力提倡的权限授予执行使用 Azure AD 所需的任务的尽可能少特权的原则。 [查看可用的不同 Azure 角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)和选择适合解决每个角色的需求。 某些角色可能需要暂时应用并在部署完成后删除。

| 业务角色| 业务任务| Azure AD 角色 |
|---|---|---|
| 帮助支持人员管理员 | 通常限制为符合条件的最终用户报告问题和执行受限的任务，如更改用户的密码、 使失效刷新令牌和监视服务运行状况。 | 支持管理员 |
| 标识管理员| 读取 Azure AD 登录报告和审核日志来调试应用程序代理相关的问题。| 安全读取者 |
| 应用程序所有者| 创建和管理企业应用程序、 应用程序注册和应用程序代理设置的所有方面。| 应用程序管理员 |
| 基础结构管理员 | 证书滚动更新所有者 | 应用程序管理员 |

尽量减少拥有访问安全信息或资源的权限的人员将帮助降低恶意行动者获取未经授权的访问或授权的用户无意中影响敏感资源的可能性。 
 
但是，用户仍需执行一天的特权操作，因此强制执行实时 (JIT) 基于[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)策略，以提供按需对 Azure 资源的访问特权和 Azure AD 是我们建议的方式有效地管理管理访问权限和审核。

### <a name="reporting-and-monitoring"></a>报告和监视

Azure AD 可以提供更深入了解组织的用户预配使用情况和通过审核日志和报告的运行状况。 

#### <a name="application-audit-logs"></a>应用程序审核日志

这些日志提供有关登录名到应用程序使用应用程序代理和设备访问应用程序的用户配置的详细的信息。 审核日志位于在 Azure 门户中，审核 API 中的导出。

#### <a name="windows-event-logs-and-performance-counters"></a>Windows 事件日志和性能计数器

连接器提供管理和会话日志。 管理日志包括关键事件及其错误。 会话日志包括所有事务及其处理详细信息。 日志和计数器都位于在 Windows 事件日志，并按照这[教程在 Azure Monitor 中配置事件日志数据源](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events)。

### <a name="troubleshooting-guide-and-steps"></a>故障排除指南和步骤

详细了解常见的问题和如何解决这些问题的指南，了解如何[故障排除](application-proxy-troubleshoot.md)错误消息。 

以下文章介绍了常见的方案，还可用于创建支持组织的故障排除指南。 

* [显示应用页时出现问题](application-proxy-page-appearance-broken-problem.md)
* [应用程序加载时间过长](application-proxy-page-load-speed-problem.md)
* [应用程序页上的链接不起作用](application-proxy-page-links-broken-problem.md)
* [要为应用打开哪些端口](application-proxy-connectivity-ports-how-to.md)
* [应用的连接器组中没有起作用的连接器](application-proxy-connectivity-no-working-connector.md)
* [在管理门户中配置](application-proxy-config-how-to.md)
* [为应用配置单一登录](application-proxy-config-sso-how-to.md)
* [在管理门户中创建应用时出现问题](application-proxy-config-problem.md)
* [配置 Kerberos 约束委派](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [使用 PingAccess 配置](application-proxy-back-end-ping-access-how-to.md)
* [无法访问此企业应用程序错误](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [安装应用程序代理程序连接器时出现问题](application-proxy-connector-installation-problem.md)
* [登录问题](application-sign-in-problem-on-premises-application-proxy.md)
