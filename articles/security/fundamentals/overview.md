---
title: Azure 安全性简介 |Microsoft Docs
description: 了解 Azure 安全性以及其服务和工作原理。
services: security
documentationcenter: na
author: UnifyCloud
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2019
ms.author: TomSh
ms.openlocfilehash: b39eb24b5611e36d14b151aaec96941ff52f3dfa
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82731689"
---
# <a name="introduction-to-azure-security"></a>Azure 安全性简介
## <a name="overview"></a>概述
我们知道，安全是云中的首要任务，及时找到有关 Azure 安全性的准确信息极其重要。 将 Azure 用于应用程序和服务的最合理原因之一是可以利用其各种安全工具和功能。 这些工具和功能可帮助在安全的 Azure 平台上创建安全的解决方案。 Microsoft Azure 提供具备保密性、完整性和可用性的客户数据，同时还能实现透明的问责制。

本文全面介绍了 Azure 的安全性。

### <a name="azure-platform"></a>Azure 平台
Azure 是一个公有云服务平台，支持极为广泛的操作系统、编程语言、框架、工具、数据库和设备选择。 它可运行与 Docker 集成的 Linux 容器；使用 JavaScript、Python、.NET、PHP、Java 和 Node.js 生成应用；生成适用于 iOS、Android 和 Windows 设备的后端。

Azure 公有云服务支持数百万开发人员和 IT 专业人士已经有所依赖并信任的相同技术。 构建 IT 资产或将其迁移到公有云服务提供商处时，需要借助该组织的能力来保护应用程序和数据，并使用该组织提供的服务和控制机制来管理基于云的资产的安全性。

Azure 的基础结构（从设备到应用程序）经过设计，可同时托管数百万的客户，并为企业提供可靠的基础，使之能够满足其安全要求。

此外，Azure 还提供广泛的可配置安全选项以及对这些选项进行控制的功能，方便用户自定义安全措施来满足组织部署的独特要求。 本文档可帮助用户了解 Azure 安全功能如何帮助满足这些要求。

> [!Note]
> 本文档重点介绍面向客户的控件，客户可以使用这些控件自定义和提高应用程序和服务的安全性。
>
> 有关 Microsoft 如何保护 Azure 平台本身的信息，请参阅[azure 基础结构安全](infrastructure.md)。

## <a name="summary-of-azure-security-capabilities"></a>Azure 安全功能摘要

### <a name="features-to-secure-the-azure-platform"></a>用于保护 Azure 平台的功能
以下功能是你可以查看的功能，可确保以安全的方式管理 Azure 平台。 提供了一些链接，进一步向下钻取 Microsoft 如何解决四个方面的客户信任问题：安全平台、隐私 & 控件、合规性和透明度。

| [安全平台](https://www.microsoft.com/trustcenter/Security/default.aspx)  | [隐私和控制](https://www.microsoft.com/trustcenter/Privacy/default.aspx)  |[遵从性](https://www.microsoft.com/trustcenter/Compliance/default.aspx)   | [透明度](https://www.microsoft.com/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [安全开发周期](https://www.microsoft.com/sdl/)，内部审核 | [随时进行数据管理](https://www.microsoft.com/trustcenter/Privacy/You-own-your-data) | [信任中心](https://www.microsoft.com/trustcenter/default.aspx) |[Microsoft 如何保护 Azure 服务中的客户数据](https://www.microsoft.com/trustcenter/Transparency/default.aspx) |
| [强制安全培训，背景检查](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) |  [控制数据位置](https://www.microsoft.com/trustcenter/Privacy/Where-your-data-is-located) |  [通用控制中心](https://www.microsoft.com/trustcenter/Common-Controls-Hub) |[Microsoft 如何管理 Azure 服务中的数据位置](https://azuredatacentermap.azurewebsites.net/)|
| [渗透测试](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx)，[入侵检测，DDoS](https://www.microsoft.com/trustcenter/Security/ThreatManagement)，[审核和日志记录](https://www.microsoft.com/trustcenter/Security/AuditingAndLogging) | [根据条件提供数据访问](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [云服务审慎调查清单](https://www.microsoft.com/trustcenter/Compliance/Due-Diligence-Checklist) |[Microsoft 中的哪些人员可以根据哪些条款访问数据](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [最先进的数据中心](https://www.microsoft.com/cloud-platform/global-datacenters)、物理安全性、[安全网络](network-overview.md) | [响应执法部门](https://www.microsoft.com/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [服务、位置和行业的符合性](https://www.microsoft.com/trustcenter/Compliance/default.aspx) |[Microsoft 如何保护 Azure 服务中的客户数据](https://www.microsoft.com/trustcenter/Transparency/default.aspx)|
|  [安全事件响应](https://aka.ms/SecurityResponsepaper)，[共担责任](https://aka.ms/sharedresponsibility) |[严格的隐私标准](https://www.microsoft.com/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [查看 Azure 服务和透明度中心的认证](https://www.microsoft.com/trustcenter/Compliance/default.aspx)|

### <a name="features-to-secure-data-and-application"></a>用于保护数据和应用程序的功能
根据云服务模型，负责管理应用程序或服务的安全的人员需承担各种不同的责任。 Azure 平台中提供的功能可帮助用户通过内置功能以及可部署到 Azure 订阅中的合作伙伴解决方案来履行这些职责。

内置功能分为六个功能区域：操作、应用程序、存储、网络、计算和标识。 有关此六个领域中的 Azure 平台提供的功能的更多详细信息，请访问摘要信息。

## <a name="operations"></a>操作
本部分提供了关于安全操作中主要特性的其他信息以及有关这些功能的摘要信息。

### <a name="security-and-audit-dashboard"></a>“安全和审核”仪表板
[安全和审核解决方案](../../security-center/security-center-intro.md)借助[内置搜索查询](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/)找到需要关注的重要问题，从而提供有关组织的 IT 安全态势的全面观点。 [安全和审核](https://technet.microsoft.com/library/mt484091.aspx)仪表板是 Azure Monitor 日志中与安全性相关的所有内容的主屏幕。 它提供计算机安全状态的高级洞见。 还允许查看过去 24 小时、7 天或任何自定义时间范围的所有事件。

此外，检测到特定事件时，可以将安全性和符合性配置为[自动执行特定操作](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/)。

### <a name="azure-resource-manager"></a>Azure 资源管理器
[Azure 资源管理器](../../azure-resource-manager/management/deployment-models.md)使你能够以组的形式处理解决方案中的资源。 可以通过一个协调的操作为解决方案部署、更新或删除所有资源。 可以使用 [Azure 资源管理器模板](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/)来完成部署，该模板适用于测试、过渡和生产等不同环境。 Resource Manager 提供安全、审核和标记功能，以帮助你在部署后管理资源。

基于 Azure 资源管理器模板的部署因其标准的安全控制设置，有助于提高 Azure 中部署的解决方案的安全性，并且还可以集成到基于标准化模板的部署中。 这样可以降低手动部署期间可能发生的安全配置错误风险。

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/)是面向 web 开发人员的可扩展应用程序性能管理（APM）服务。 用户可以使用 Application Insights 监视实时 Web 应用程序并自动检测性能异常。 Application Insights 内含强大的分析工具，有助于诊断问题并了解用户在应用中实际执行的操作。 它在应用程序运行时全程进行监视，包括测试期间以及发布或部署之后。

Application Insights 可创建图表和表格来显示多种信息，例如，一天中的哪些时间用户最多、应用的响应能力如何，以及应用依赖的任何外部服务是否顺利地为其提供服务。

如果出现崩溃、故障或性能问题，可以搜索详细的遥测数据来诊断原因。 此外，如果应用的可用性和性能有任何变化，该服务还会向用户发送电子邮件。 Application Insight 就是这样因其有助于实现保密性、完整性和可用性安全三元素的可用性而成为有价值的安全工具。

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) 对来自 Azure 基础结构（[活动日志](../../azure-monitor/platform/platform-logs-overview.md)）和每个单独的 Azure 资源（[诊断日志](../../azure-monitor/platform/platform-logs-overview.md)）的数据提供可视化效果、查询、路由、警报、自动缩放和自动化功能。 可以使用 Azure Monitor 对 Azure 日志中生成的与安全相关的事件发出警报。

### <a name="azure-monitor-logs"></a>Azure Monitor 日志
[Azure Monitor 日志](https://azure.microsoft.com/documentation/services/log-analytics/)–除了 Azure 资源之外，还为本地和第三方基于云的基础结构（例如 AWS）提供 IT 管理解决方案。 可以将 Azure Monitor 中的数据直接路由到 Azure Monitor 日志，以便在一个位置查看整个环境的指标和日志。

在鉴证和其他安全分析中，Azure Monitor 日志可能是一个有用的工具，因为使用该工具，您可以通过灵活的查询方法快速搜索大量与安全相关的条目。 此外，本地[防火墙和代理日志可以导出到 Azure 中，并可使用 Azure Monitor 日志进行分析。](../../log-analytics/log-analytics-agent-windows.md)

### <a name="azure-advisor"></a>Azure 顾问
[Azure 顾问](../../advisor/index.yml)是一种个性化的云顾问，可帮助优化 Azure 部署。 它分析资源配置和使用情况遥测数据。 然后，它建议解决方案帮助提高资源的[性能](../../advisor/advisor-performance-recommendations.md)、[安全性](../../advisor/advisor-security-recommendations.md)和[高可用性](../../advisor/advisor-high-availability-recommendations.md)，同时寻找机会[减少总体 Azure 支出](../../advisor/advisor-cost-recommendations.md)。 Azure 顾问提供安全建议，可显著提高在 Azure 中部署的解决方案的总体安全状况。 这些建议来自于 [Azure 安全中心](../../security-center/security-center-intro.md)执行的安全分析。

### <a name="azure-security-center"></a>Azure 安全中心
[安全中心](../../security-center/security-center-intro.md)有助于预防、检测和响应威胁，同时提高 Azure 资源的可见性并控制其安全性。 它提供 Azure 订阅之间的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。

此外，安全中心还提供了一个仪表板，该仪表板显示可立即采取措施的警报和建议。 通常，只需要在安全中心控制台中单击即可解决问题。
## <a name="applications"></a>应用程序
本部分提供了关于应用程序安全中主要特性的其他信息以及有关这些功能的摘要信息。

### <a name="web-application-vulnerability-scanning"></a>Web 应用程序漏洞扫描
开始对[应用服务应用](../../app-service/overview.md)进行漏洞测试最简单的一种方法是使用[与 Tinfoil Security 的集成](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)对应用执行一键式漏洞扫描。 可以查看易于理解的报告中的测试结果，并了解如何按照分步说明修复每个安全漏洞。

### <a name="penetration-testing"></a>渗透测试
如果想要执行自己的渗透测试，或者想要使用其他扫描程序套件或提供程序，则必须按照 [Azure 渗透测试审批流程](https://docs.microsoft.com/azure/security/fundamentals/pen-testing )来进行并获得事先批准才能执行所需的渗透测试。

### <a name="web-application-firewall"></a>Web 应用程序防火墙
[Azure 应用程序网关](https://azure.microsoft.com/services/application-gateway/)中的 Web 应用程序防火墙 (WAF) 可帮助保护 Web 应用程序，使其免受常见基于 Web 的攻击威胁，例如 SQL 注入、跨站点脚本攻击和会话劫持。 同时预先配置保护，免受 [Open Web Application Security Project (OWASP) 标识为前 10 种常见漏洞](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)的威胁攻击。

### <a name="authentication-and-authorization-in-azure-app-service"></a>Azure 应用服务中的身份验证和授权
[应用服务身份验证/授权](../../app-service/overview-authentication-authorization.md)是一项功能，方便应用程序登录用户，避免在应用后端更改代码。 该功能可以方便地保护应用程序和处理每个用户的数据。

### <a name="layered-security-architecture"></a>分层安全体系结构
由于[应用服务环境](../../app-service/environment/app-service-app-service-environment-intro.md)提供部署到 [Azure 虚拟网络](../../virtual-network/virtual-networks-overview.md)的隔离运行时环境，因此开发人员能够创建分层安全体系结构，针对每个应用层提供不同级别的网络访问权限。 常见的需求之一是要隐藏对 API 后端的常规 Internet 访问，而只允许由上游 Web 应用调用 API。 可以在包含应用服务环境的 Azure 虚拟网络子网上使用[网络安全组 (NSG)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)，限制对 API 应用程序的公共访问。

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Web 服务器诊断和应用程序诊断
应用服务 Web 应用为 Web 服务器和 Web 应用程序中的日志记录信息提供诊断功能。 它们在逻辑上分为[web 服务器诊断](../../app-service/troubleshoot-diagnostic-logs.md)和[应用程序诊断](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx)。 Web 服务器包括诊断和排查站点和应用程序这两大改进方面。

第一个新特点是有关应用程序池、工作进程、站点、应用程序域和运行请求的实时状态信息。 第二个新特点是在整个请求和响应过程中跟踪请求的详细跟踪事件。

要启用这些跟踪事件的收集，可以将 IIS 7 配置为根据运行时间或错误响应代码自动捕获任何特定请求的完整跟踪日志（采用 XML 格式）。

#### <a name="web-server-diagnostics"></a>Web 服务器诊断
可以启用或禁用以下种类的日志：

-   详细错误日志记录 - 指示故障的 HTTP 状态代码（状态代码 400 或更大数字）的详细错误消息。 其中可能包含有助于确定服务器返回错误代码的原因的信息。

-   失败请求跟踪 - 有关失败请求的详细信息，包括对用于处理请求的 IIS 组件和每个组件所用的时间的跟踪。 在尝试提高站点性能或隔离导致要返回特定 HTTP 错误的内容时，此信息很有用。

-   Web 服务器日志记录 - 使用 W3C 扩展日志文件格式的 HTTP 事务信息。 这在确定整体站点度量值（如处理的请求数量或来自特定 IP 地址的请求数）时非常有用。

#### <a name="application-diagnostics"></a>应用程序诊断
使用[应用程序诊断](../../app-service/troubleshoot-diagnostic-logs.md)可以捕获 web 应用程序生成的信息。 ASP.NET 应用程序可使用 [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) 类将信息记录到应用程序诊断日志。 在应用程序诊断中，有两种主要类型的事件，即与应用程序性能相关的事件以及与应用程序故障和错误相关的事件。 故障和错误可以进一步分为连接性、安全性和故障问题。 故障问题通常与应用程序代码问题相关。

在应用程序诊断中，可以查看按以下方式分组的事件：

-   全部（显示所有事件）
-   应用程序错误（显示异常事件）
-   性能（显示性能事件）

## <a name="storage"></a>存储
本部分提供了关于 Azure 存储安全中主要特性的其他信息以及有关这些功能的摘要信息。

### <a name="role-based-access-control-rbac"></a>基于角色的访问控制 (RBAC)
可以使用基于角色的访问控制 (RBAC) 来保护存储帐户。 对于想要实施数据访问安全策略的组织而言，根据[需要了解](https://en.wikipedia.org/wiki/Need_to_know)和[最低权限](https://en.wikipedia.org/wiki/Principle_of_least_privilege)安全原则限制访问权限是必不可少的。 这些访问权限是通过将相应的 RBAC 角色分配给特定范围内的组和应用程序来授予的。 可以使用[内置 RBAC 角色](../../role-based-access-control/built-in-roles.md)（例如存储帐户参与者）将权限分配给用户。 可通过基于角色的访问控制 (RBAC)，控制借助 [Azure 资源管理器](../../storage/blobs/security-recommendations.md)模型访问存储帐户的存储密钥的情况。

### <a name="shared-access-signature"></a>共享访问签名
[共享访问签名（SAS）](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)用于对存储帐户中的资源进行委托访问。 使用 SAS，意味着可以授权客户端在指定时间段内，以一组指定权限有限访问存储帐户中的对象。 可以授予这些有限的权限，而不必共享帐户访问密钥。

### <a name="encryption-in-transit"></a>传输中加密
传输中加密是通过网络传输数据时用于保护数据的一种机制。 在 Azure 存储中，可以使用以下加密方式来保护数据：
-   [传输级加密](../../storage/blobs/security-recommendations.md)，如在将数据传入或传出 Azure 存储时的 HTTPS。

-   [线路加密](../../storage/blobs/security-recommendations.md)，例如[Azure 文件共享](../../storage/files/storage-dotnet-how-to-use-files.md)的[SMB 3.0 加密](../../storage/blobs/security-recommendations.md)。

-   客户端加密，在将数据传输到存储之前加密数据，以及从存储传出数据后解密数据。

### <a name="encryption-at-rest"></a>静态加密
对许多组织而言，静态数据加密是实现数据隐私性、符合性和数据所有权的必要措施。 有三项 Azure 存储安全功能可提供“静态”数据加密：

-   [存储服务加密](../../storage/common/storage-service-encryption.md)可以请求存储服务在将数据写入 Azure 存储时自动加密数据。

-   [客户端加密](../../storage/common/storage-client-side-encryption.md)还提供静态加密功能。

-   [Azure 磁盘加密](../azure-security-disk-encryption-overview.md)允许加密 IaaS 虚拟机使用的 OS 磁盘和数据磁盘。

### <a name="storage-analytics"></a>存储分析
[Azure 存储分析](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)执行日志记录并为存储帐户提供指标数据。 可以使用此数据跟踪请求、分析使用情况趋势以及诊断存储帐户的问题。 存储分析记录成功和失败的存储服务请求的详细信息。 可以使用该信息监视各个请求和诊断存储服务问题。 将最大程度地记录请求。 将记录以下类型的已经过身份验证的请求：
-   成功的请求。

-   失败的请求，包括超时、限制、网络、授权和其他错误。

-   使用共享访问签名 (SAS) 的请求，包括失败和成功的请求。

-   分析数据请求。

### <a name="enabling-browser-based-clients-using-cors"></a>使用 CORS 启用基于浏览器的客户端
[跨源资源共享 (CORS)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) 是一种允许域授予彼此资源访问权限的机制。 用户代理发送额外的标头，以确保允许从特定域中加载的 JavaScript 代码访问位于另一个域的资源。 然后，后一个域使用额外标头进行回复，允许或拒绝原始域访问其资源。

Azure 存储服务现支持 CORS，因此，为服务设置 CORS 规则后，便会对从另一个域对服务发出的经过正确验证的请求进行评估，以根据指定的规则确定是否允许该请求。

## <a name="networking"></a>网络
本部分提供了关于 Azure 网络安全中主要特性的其他信息以及有关这些功能的摘要信息。

### <a name="network-layer-controls"></a>网络层控制
网络访问控制是限制特定设备或子网之间的连接的行为，代表了网络安全的核心。 网络访问控制旨在确保虚拟机和服务仅让你指定可访问的用户和设备进行访问。

#### <a name="network-security-groups"></a>网络安全组
[网络安全组 (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) 是基本的静态数据包筛选防火墙，使用户能够基于 [5 元组](https://www.techopedia.com/definition/28190/5-tuple)控制访问权限。 NSG 不提供应用程序层检查或经过身份验证的访问控制。 它们可用于控制在 Azure 虚拟网络中的子网之间移动的流量以及控制 Azure 虚拟网络和 Internet 之间的流量。

#### <a name="route-control-and-forced-tunneling"></a>路由控制和强制隧道
在 Azure 虚拟网络上控制路由行为的能力是关键的网络安全和访问控制功能。 例如，如果要确保与 Azure 虚拟网络之间的所有流量都通过该虚拟安全设备，则必须能够控制和自定义路由行为。 可以通过在 Azure 中配置用户定义的路由实现此操作。

[用户定义的路由](../../virtual-network/virtual-networks-udr-overview.md)允许用户为进出单个虚拟机或子网的流量自定义入站和出站路径，以确保最安全的路由。 [强制隧道](https://www.petri.com/azure-forced-tunneling)是一种机制，可用于确保服务不能启动到 Internet 上的设备的连接。

这不同于能够接受传入连接然后对其作出响应。 前端 Web 服务器需要响应来自 Internet 主机的请求，因此允许源自 Internet 的流量传入到这些 Web 服务器，而且这些 Web 服务器可以作出响应。

强制隧道通常用于强制到 Internet 的外部流量通过本地安全代理和防火墙。

#### <a name="virtual-network-security-appliances"></a>虚拟网络安全设备
虽然网络安全组、用户定义的路由和强制隧道在 [OSI 模型](https://en.wikipedia.org/wiki/OSI_model)的网络层和传输层为用户提供了一定程度的安全性，但有时可能想要启用堆栈的更高级别安全性。 可以使用 Azure 合作伙伴安全设备解决方案访问这些增强的网络安全功能。 通过访问[Azure Marketplace](https://azure.microsoft.com/marketplace/)并搜索 "安全" 和 "网络安全"，可以找到最新的 azure 合作伙伴网络安全解决方案。

### <a name="azure-virtual-network"></a>Azure 虚拟网络
Azure 虚拟网络 (VNet) 是自己的网络在云中的表示形式。 它是对专用于订阅的 Azure 网络结构进行的逻辑隔离。 可以完全控制该网络中的 IP 地址块、DNS 设置、安全策略和路由表。 可以将 VNet 细分成各个子网，并在 Azure 虚拟网络上放置 Azure IaaS 虚拟机 (VM) 和/或[云服务（PaaS 角色实例）](../../cloud-services/cloud-services-choose-me.md)。

此外，还可以使用 Azure 中提供的[连接选项](../../vpn-gateway/index.yml)之一将虚拟网络连接到本地网络。 实际上，可以将网络扩展到 Azure，对 IP 地址块进行完全的控制，并享受企业级 Azure 带来的好处。

Azure 网络支持各种安全远程访问方案。 其中包括：

-   [将单独的工作站连接到 Azure 虚拟网络](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

-   [通过 VPN 将本地网络连接到 Azure 虚拟网络](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

-   [通过专用 WAN 链接将本地网络连接到 Azure 虚拟网络](../../expressroute/expressroute-introduction.md)

-   [相互连接 Azure 虚拟网络](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

### <a name="vpn-gateway"></a>VPN 网关
若要在 Azure 虚拟网络与本地站点之间发送网络流量，必须为 Azure 虚拟网络创建 VPN 网关。 [VPN 网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md)是一种虚拟网络网关，它通过公共连接发送加密流量。 也可以使用 VPN 网关在基于 Azure 网络结构的 Azure 虚拟网络之间发送流量。

### <a name="express-route"></a>Express Route
Microsoft Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) 是专用 WAN 链接，可让用户通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。

![Express Route](./media/overview/azure-security-fig1.png)

使用 ExpressRoute 可与 Microsoft Azure、Office 365 和 CRM Online 等 Microsoft 云服务建立连接。 可以从任意位置之间的 (IP VPN) 网络、点到点以太网或在共置设施上通过连接服务提供商的虚拟交叉连接来建立这种连接。

ExpressRoute 连接不会通过公共 Internet，因此可以认为它比基于 VPN 的解决方案更安全。 与通过 Internet 的典型连接相比，ExpressRoute 连接提供更高的可靠性、更快的速度、更低的延迟和更高的安全性。


### <a name="application-gateway"></a>应用程序网关
Microsoft [Azure 应用程序网关](../../application-gateway/overview.md)以服务形式提供[应用程序传送控制器 (ADC)](https://en.wikipedia.org/wiki/Application_delivery_controller)，借此为应用程序提供第 7 层各种负载均衡功能。

![应用程序网关](./media/overview/azure-security-fig2.png)

它可让你通过将 CPU 密集型 TLS 终止功能卸载到应用程序网关（也称为 "TLS 卸载" 或 "TLS 桥接"）来优化 web 场工作效率。 它还提供第 7 层其他路由功能，包括传入流量的轮循机制分配、基于 Cookie 的会话相关性、基于 URL 路径的路由，以及在单个应用程序网关后面托管多个网站的能力。 Azure 应用程序网关是第 7 层负载均衡器。

它在不同服务器之间提供故障转移和性能路由 HTTP 请求，而不管它们是在云中还是本地。

应用程序提供许多应用程序传送控制器（ADC）功能，包括 HTTP 负载均衡、基于 cookie 的会话相关性、 [TLS 卸载](../../application-gateway/tutorial-restrict-web-traffic-powershell.md)、自定义运行状况探测、多站点支持，以及许多其他功能。

### <a name="web-application-firewall"></a>Web 应用程序防火墙
Web 应用程序防火墙是 [Azure 应用程序网关](../../application-gateway/overview.md)的一项功能，它为使用应用程序网关实现标准应用程序传递控制 (ADC) 功能的 Web 应用程序提供保护。 Web 应用程序防火墙通过保护这些应用程序，免受 OWASP 前 10 个常见的 Web 漏洞中的大多数漏洞的威胁，来实现此目的。

![Web 应用程序防火墙](./media/overview/azure-security-fig1.png)

-   SQL 注入保护

-   常见 Web 攻击保护，例如命令注入、HTTP 请求走私、HTTP 响应拆分和远程文件包含攻击

-   防止 HTTP 协议违反行为

-   防止 HTTP 协议异常行为，例如缺少主机用户代理和接受标头

-   防止自动程序、爬网程序和扫描程序

-   检测常见应用程序错误配置（即 Apache、IIS 等）

可防止 Web 攻击的集中式 Web 应用程序防火墙，可简化安全管理，并可针对入侵威胁为应用程序提供更好的保障。 相较保护每个单独的 Web 应用程序，WAF 解决方案还可通过在中央位置修补已知漏洞，更快地响应安全威胁。 现有应用程序网关可以轻松地转换为带有 Web 应用程序防火墙的应用程序网关。

### <a name="traffic-manager"></a>流量管理器
使用 Microsoft [Azure 流量管理器](../../traffic-manager/traffic-manager-overview.md)，可以控制用户流量在不同数据中心内的服务终结点上的分布。 流量管理器支持的服务终结点包括 Azure VM、Web 应用和云服务。 也可将流量管理器用于外部的非 Azure 终结点。 流量管理器根据[流量路由方法](../../traffic-manager/traffic-manager-routing-methods.md)和终结点的运行状况，使用域名系统（DNS）将客户端请求定向到最合适的终结点。

流量管理器提供多种流量路由方法来满足不同的应用程序需求、终结点运行状况[监视](../../traffic-manager/traffic-manager-monitoring.md)和自动故障转移。 流量管理器能够灵活应对故障，包括整个 Azure 区域的故障。

### <a name="azure-load-balancer"></a>Azure 负载均衡器
[Azure 负载均衡器](../../load-balancer/load-balancer-overview.md)为应用程序提供高可用性和网络性能。 它是第 4 层（TCP、UDP）类型的负载均衡器，可在负载均衡集中定义的运行状况良好的服务实例之间分配传入流量。 可以将 Azure 负载均衡器配置为：

-   对传入到虚拟机的 Internet 流量进行负载均衡。 此配置称为[公共负载平衡](../../load-balancer/components.md#frontend-ip-configurations)。

-   对虚拟网络中虚拟机之间的流量、云服务中虚拟机之间的流量或本地计算机和跨界虚拟网络中虚拟机之间的流量进行负载均衡。 此配置称为[内部负载均衡](../../load-balancer/components.md#frontend-ip-configurations)。

- 将外部流量转发到特定的虚拟机

### <a name="internal-dns"></a>内部 DNS
可以在管理门户或网络配置文件中管理 VNet 中使用的 DNS 服务器列表。 客户最多可以为每个 VNet 添加 12 个 DNS 服务器。 指定 DNS 服务器时，请务必按照客户环境的正确顺序列出客户的 DNS 服务器。 DNS 服务器列表不采用循环机制。 将按指定服务器的顺序使用这些服务器。 如果可访问列表上的第一个 DNS 服务器，则无论该 DNS 服务器是否运行正常，客户端都将使用该服务器。 要更改客户的虚拟网络的 DNS 服务器顺序，请从列表中删除 DNS 服务器，并按客户希望的顺序重新添加这些服务器。 DNS 支持“CIA”安全三因素的可用性方面。

### <a name="azure-dns"></a>Azure DNS
[域名系统](https://technet.microsoft.com/library/bb629410.aspx)或 DNS 负责将网站或服务名称转换（或解析）为它的 IP 地址。 [Azure DNS](../../dns/dns-overview.md) 是 DNS 域的托管服务，它使用 Microsoft Azure 基础结构提供名称解析。 通过在 Azure 中托管域，可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理 DNS 记录。 DNS 支持“CIA”安全三因素的可用性方面。

### <a name="azure-monitor-logs-nsgs"></a>Azure Monitor 日志 Nsg
可以为 NSG 启用以下诊断日志类别：

-   事件：包含根据 MAC 地址向 VM 和实例角色应用的 NSG 规则条目。 每隔 60 秒收集一次这些规则的状态。

-   规则计数器：包含应用每个 NSG 规则以拒绝或允许流量的次数的条目。

### <a name="security-center"></a>安全中心
[Azure 安全中心](../../security-center/security-center-intro.md)会持续分析 azure 资源的安全状态，以了解网络安全最佳实践。 当安全中心识别到潜在的安全漏洞时，它会创建[建议](../../security-center/security-center-recommendations.md)，指导你完成配置所需控件的过程，以强化和保护资源。

## <a name="compute"></a>计算
本部分提供了关于此区域中主要特性的其他信息以及有关这些功能的摘要信息。

### <a name="antimalware--antivirus"></a>反恶意软件和防病毒软件
借助 Azure IaaS，可以使用来自 Microsoft、Symantec、Trend Micro、McAfee 和 Kaspersky 等安全性供应商的反恶意软件，以保护虚拟机免受恶意文件、广告软件和其他威胁的侵害。 适用于 Azure 云服务和虚拟机的 [Microsoft 反恶意软件](antimalware.md)是一种保护功能，可帮助识别并删除病毒、间谍软件和其他恶意软件。 Microsoft 反恶意软件提供了已知恶意或不需要的软件试图安装自身或在 Azure 系统上运行时的可配置警报。 此外可以使用 Azure 安全中心部署 Microsoft 反恶意软件

### <a name="hardware-security-module"></a>硬件安全模块
加密和身份验证不会提高安全性，除非密钥本身受到保护。 你可以通过将关键机密和密钥存储在[Azure Key Vault](../../key-vault/general/overview.md)中来简化其管理和安全。 Key Vault 可将用户密钥存储在已通过 FIPS 140-2 Level 2 标准认证的硬件安全模块 (HSM) 中。 用于备份或[透明数据加密](https://msdn.microsoft.com/library/bb934049.aspx)的 SQL Server 加密密钥均可存储在密钥保管库中，此外还可存储应用程序中的任意密钥或密码。 对这些受保护项的权限和访问权限通过 [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/) 进行管理。

### <a name="virtual-machine-backup"></a>虚拟机备份
[Azure 备份](../../backup/backup-overview.md)是一种解决方案，无需资本投资便可保护应用程序数据，最大限度降低运营成本。 应用程序错误可能损坏数据，人为错误可能将 bug 引入应用程序，从而导致安全问题。 借助 Azure 备份，可以保护运行 Windows 和 Linux 的虚拟机。

### <a name="azure-site-recovery"></a>Azure Site Recovery
组织的[业务连续性/灾难恢复 (BCDR)](../../best-practices-availability-paired-regions.md) 策略的其中一个重要部分是，找出在发生计划内和计划外的中断时让企业工作负荷和应用保持启动并运行的方法。 [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) 可帮助协调工作负荷和应用的复制、故障转移及恢复，因此能够在主要位置发生故障时通过辅助位置来提供工作负荷和应用。

### <a name="sql-vm-tde"></a>SQL VM TDE
SQL Server 加密功能包括[透明数据加密 (TDE)](../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)和列级加密 (CLE)。 这种加密形式要求客户管理和存储用于加密的加密密钥。

Azure 密钥保管库 (AKV) 服务专用于在一个高度可用的安全位置改进这些密钥的安全性和管理。 SQL Server 连接器使 SQL Server 能够使用 Azure Key Vault 中的这些密钥。

如果在本地计算机上运行 SQL Server，请按照此处提供的步骤通过本地 SQL Server 计算机访问 Azure Key Vault。 但对于 Azure VM 中的 SQL Server，可以使用 Azure Key Vault 集成功能节省时间。 通过使用几个 Azure PowerShell cmdlet 来启用此功能，可以自动为 SQL VM 进行必要的配置以便访问密钥保管库。

### <a name="vm-disk-encryption"></a>VM 磁盘加密
[Azure 磁盘加密](../azure-security-disk-encryption-overview.md)是一种新功能，可帮助你加密 Windows 和 Linux IaaS 虚拟机磁盘。 它应用 Windows 的行业标准 BitLocker 功能和 Linux 的 DM-Crypt 功能，为 OS 和数据磁盘提供卷加密。 该解决方案与 Azure Key Vault 集成，帮助用户控制和管理 Key Vault 订阅中的磁盘加密密钥和机密。 此解决方案还可确保虚拟机磁盘上的所有数据在 Azure 存储中静态加密。

### <a name="virtual-networking"></a>虚拟网络
虚拟机需要网络连接。 要支持该要求，Azure 要求将虚拟机连接到 Azure 虚拟网络。 Azure 虚拟网络是一个构建于物理 Azure 网络结构之上的逻辑构造。 每个逻辑[Azure 虚拟网络](../../virtual-network/virtual-networks-overview.md)都独立于所有其他 azure 虚拟网络。 这种隔离可帮助确保部署中的网络流量对于其他 Microsoft Azure 客户不可访问。

### <a name="patch-updates"></a>修补程序更新
修补程序更新可以减少必须在企业中部署的软件更新数目并提高监视符合性的能力，从而提供查找及修复潜在问题的基础并简化软件更新管理过程。

### <a name="security-policy-management-and-reporting"></a>安全策略管理和报告
[安全中心](../../security-center/security-center-intro.md)帮助你预防、检测和应对威胁，并使你能够更好地查看和控制 Azure 资源的安全性。 它提供对 Azure 订阅的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。

## <a name="identity-and-access-management"></a>标识和访问管理
保护系统、应用程序和以基于标识的访问控制开始的数据。 Microsoft 企业产品和服务内置的标识和访问管理功能有助于保护组织和个人信息免受未经授权的访问，同时向合法用户提供随时随地访问权限。

### <a name="secure-identity"></a>安全标识
Microsoft 在其产品和服务中使用多种安全实践和技术来管理标识和访问权限。

-   [多重身份验证](https://azure.microsoft.com/services/multi-factor-authentication/)要求用户在本地和云中使用多种方法进行访问。 它提供强大的身份验证和一系列简单的验证选项，同时满足用户对简单登录过程的需求。

-   [Microsoft Authenticator ](https://aka.ms/authenticator) 提供了一种用户友好型多重身份验证体验，它可与 Microsoft Azure Active Directory 和 Microsoft 帐户兼容，并支持可穿戴设备和基于指纹的批准。

-   [强制实施密码策略](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/)通过强制执行长度和复杂性要求、强制定期轮换和身份验证尝试失败后的帐户锁定来提高传统密码的安全性。

-   [基于令牌的身份验证](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/)启用通过 Azure Active Directory 进行身份验证。

-   [基于角色的访问控制 (RBAC)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) 能够根据用户分配的角色来授予访问权限，从而轻松为用户仅提供执行作业所需的访问量。 可以根据组织的业务模型和风险允许范围自定义 RBAC。

-   [集成标识管理（混合标识）](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/)能够保持对用户在内部数据中心和云平台中的访问控制，并为所有资源的身份验证和授权创建单个用户标识。

### <a name="secure-apps-and-data"></a>保护应用和数据
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 是综合性的标识和访问管理云解决方案，可帮助确保安全访问站点和云中的应用程序数据，并简化对用户和组的管理。 它结合了核心目录服务、高级 Identity Governance、安全性以及应用程序访问管理，使开发人员可以轻松在其应用中构建基于策略的标识管理。 若要增强 Azure Active Directory，可以使用 Azure Active Directory Basic、Premium P1、和 Premium P2 版添加付费功能。

| 免费/常用功能     | 基本功能    |高级 P1 功能 |高级 P2 功能 | Azure Active Directory Join – 仅适用于 Windows 10 的相关功能|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Directory 对象](../../active-directory/active-directory-whatis.md)，[用户/组管理（添加/更新/删除）/基于用户的预配，设备注册](../../active-directory/active-directory-whatis.md)，[单一登录（SSO）](../../active-directory/active-directory-whatis.md)，[云用户的自助密码更改](../../active-directory/active-directory-whatis.md)，[连接（将本地目录扩展到 Azure Active Directory 的同步引擎）](../../active-directory/active-directory-whatis.md)，[安全/使用情况报告](../../active-directory/active-directory-whatis.md)       |   [基于组的访问管理/预配](../../active-directory/active-directory-whatis.md)，[云用户的自助服务密码重置](../../active-directory/active-directory-whatis.md)，[公司品牌（登录页/访问面板自定义）](../../active-directory/active-directory-whatis.md)，[应用程序代理](../../active-directory/active-directory-whatis.md)， [SLA 99.9%](../../active-directory/active-directory-whatis.md) |  [自助组和应用管理/自助应用程序添加件/动态组](../../active-directory/active-directory-whatis.md)，[通过本地写回进行自助密码重置/更改/解锁](../../active-directory/active-directory-whatis.md)，[多重身份验证（云和本地（MFA 服务器））](../../active-directory/active-directory-whatis.md)， [MIM CAL + MIM 服务器](../../active-directory/active-directory-whatis.md)， [Cloud App Discovery](../../active-directory/active-directory-whatis.md)，[连接运行状况](../../active-directory/active-directory-whatis.md)，[组帐户的自动密码滚动更新](../../active-directory/active-directory-whatis.md)|    [标识保护](../../active-directory/identity-protection/overview.md)， [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)|   [将设备加入到 Azure AD、桌面 SSO、Microsoft Passport 用于 Azure AD、管理员 BitLocker 恢复](../../active-directory/active-directory-whatis.md)、 [MDM 自动注册、自助 bitlocker 恢复、通过 Azure AD 联接附加到 Windows 10 设备的其他本地管理员](../../active-directory/active-directory-whatis.md)|


- [Cloud App Discovery](../../active-directory/cloudappdiscovery-get-started.md) 是 Azure Active Directory 的一项高级功能，能够识别组织中的人员所使用的云应用程序。

- [Azure Active Directory Identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/)是一种安全服务，它使用 Azure Active Directory 异常检测功能来提供合并查看风险检测的视图和可能影响组织标识的潜在漏洞。

- [Azure Active Directory 域服务](https://azure.microsoft.com/services/active-directory-ds/)让用户可以将 Active VM 加入一个域，且无需部署域控制器。 用户可使用他们的企业 Active Directory 凭证登录这些 VM，且可以无缝访问资源。

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) 是一个高度可用的全局性标识管理服务，该服务适用于面向用户且可通过伸缩来处理数以亿计标识的应用程序，并可跨移动平台和 Web 平台集成。 客户可以通过使用现有社交媒体帐户的自定义体验登录所有应用，也可以创建新的独立凭据。

- [Azure Active Directory B2B 协作](https://aka.ms/aad-b2b-collaboration)是一种安全的合作伙伴集成解决方案，可让合作伙伴使用其自行管理的标识有选择性地访问企业应用程序和数据，为跨公司合作关系提供支持。

- [Azure Active Directory Join](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) 可以将云功能扩展到 Windows 10 设备进行集中管理。 它使用户可以通过 Azure Active Directory 连接到企业或组织云，并简化对应用和资源的访问。

- [Azure Active Directory 应用程序代理](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/)为本地托管的 Web 应用程序提供 SSO 和安全远程访问。

## <a name="next-steps"></a>后续步骤

- 了解你[在云中的共同责任](shared-responsibility.md)。

- 了解[Azure 安全中心](https://azure.microsoft.com/services/security-center/)如何帮助你预防、检测和响应威胁，同时提高 azure 资源的可见性并控制其安全性。
