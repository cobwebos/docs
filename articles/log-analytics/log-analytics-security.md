---
title: "Log Analytics 数据安全 | Microsoft 文档"
description: "了解 Log Analytics 如何保护你的隐私和数据安全。"
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: a33bb05d-b310-4f2c-8f76-f627e600c8e7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: de674af369080ad7eb608608685e293f2326c8e6
ms.openlocfilehash: fa33a031a9e05f3079f1ed68d2ac0902b3070fa6
ms.contentlocale: zh-cn
ms.lasthandoff: 05/04/2017


---
# <a name="log-analytics-data-security"></a>Log Analytics 数据安全
Microsoft 致力于保护你的隐私和数据安全，同时提供软件和服务来帮助你管理组织的 IT 基础结构。 我们认识到，当你授信他人访问你的数据时，这种信任需要极高的安全性。 从服务的编码到运营，Microsoft 都严格遵守相关法规与安全准则。

保护数据是 Microsoft 的头等大事。 如有任何有关以下任何信息（包括 [Azure 支持选项](http://azure.microsoft.com/support/options/)的安全政策）的疑问、建议或问题，请与我们联系。

本文介绍了如何通过使用 Operations Management Suite (OMS) 中的 Log Analytics 收集、处理和保护数据。 可以使用代理连接到 Web 服务、使用 System Center Operations Manager 收集操作数据或从 Azure 诊断中检索数据供 Log Analytics 使用。 通过 Internet 使用基于证书的身份验证和 SSL 3 将收集的数据发送到 Microsoft Azure 中托管的 Log Analytics 服务。 数据在发送前会由代理进行压缩。

Log Analytics 服务使用以下方法安全地管理你基于云的数据：

* 数据隔离
* 数据保留
* 物理安全性
* 事件管理
* 合规性
* 安全标准认证

## <a name="data-segregation"></a>数据隔离
客户数据在 OMS 服务的每个组件上都保持逻辑隔离。 所有数据均按组织进行标记。 此标记方式贯穿数据的整个生命周期，在服务的每个层强制实施。 每个用户都具有用于存储长期数据的专用 Azure blob

## <a name="data-retention"></a>数据保留
编入索引的日志搜索数据基于你的定价计划进行存储和保留。 有关详细信息，请参阅 [Log Analytics 定价](https://azure.microsoft.com/pricing/details/log-analytics/)。

Microsoft 会在 OMS 工作区关闭的 30 天后删除客户数据。 Microsoft 还会删除承载该数据的 Azure 存储帐户。 在删除客户数据时，不会销毁任何物理驱动器。

下表列出了 OMS 中可用的一些解决方案并列举了它们会收集的数据的类型。

| **解决方案** | **数据类型** |
| --- | --- |
| 配置评估 |配置数据、元数据和状态数据 |
| 容量规划 |性能数据和元数据 |
| 反恶意软件 |配置数据和元数据 |
| 系统更新评估 |元数据和状态数据 |
| 日志管理 |用户定义的事件日志、Windows 事件日志和/或 IIS 日志 |
| 更改跟踪 |软件清单和 Windows 服务元数据 |
| SQL 和 Active Directory 评估 |WMI 数据、注册表数据、性能数据和 SQL Server 动态管理视图结果 |

下表显示了数据类型的示例：

| **数据类型** | **字段** |
| --- | --- |
| 警报 |Alert Name、Alert Description、BaseManagedEntityId、Problem ID、IsMonitorAlert、RuleId、ResolutionState、Priority、Severity、Category、Owner、ResolvedBy、TimeRaised、TimeAdded、LastModified、LastModifiedBy、LastModifiedExceptRepeatCount、TimeResolved、TimeResolutionStateLastModified、TimeResolutionStateLastModifiedInDB、RepeatCount |
| 配置 |CustomerID、AgentID、EntityID、ManagedTypeID、ManagedTypePropertyID、CurrentValue、ChangeDate |
| 事件 |EventId、EventOriginalID、BaseManagedEntityInternalId、RuleId、PublisherId、PublisherName、FullNumber、Number、Category、ChannelLevel、LoggingComputer、EventData、EventParameters、TimeGenerated、TimeAdded <br>**注意：**当将具有自定义字段的事件写入到 Windows 事件日志中时，OMS 会收集它们。 |
| 元数据 |BaseManagedEntityId、ObjectStatus、OrganizationalUnit、ActiveDirectoryObjectSid、PhysicalProcessors、NetworkName、IPAddress、ForestDNSName、NetbiosComputerName、VirtualMachineName、LastInventoryDate、HostServerNameIsVirtualMachine、IP Address、NetbiosDomainName、LogicalProcessors、DNSName、DisplayName、DomainDnsName、ActiveDirectorySite、PrincipalName、OffsetInMinuteFromGreenwichTime |
| 性能 |ObjectName、CounterName、PerfmonInstanceName、PerformanceDataId、PerformanceSourceInternalID、SampleValue、TimeSampled、TimeAdded |
| 状态 |StateChangeEventId、StateId、NewHealthState、OldHealthState、Context、TimeGenerated、TimeAdded、StateId2、BaseManagedEntityId、MonitorId、HealthState、LastModified、LastGreenAlertGenerated、DatabaseTimeModified |

## <a name="physical-security"></a>物理安全性
OMS 服务中的 Log Analytics 由 Microsoft 人员负责操控，将记录所有活动并且可进行审核。 该服务完全在 Azure 中运行，并且遵循 Azure 通用工程标准。 可以在 [Microsoft Azure 安全性概述](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf)的第 18 页上找到有关 Azure 资产的物理安全性的详细信息。 对于不再负责 OMS 服务的任何人员，会在一个工作日内更改该人员对安全区域的物理访问权限，包括传输和终止。 可在 [Microsoft 数据中心](https://www.microsoft.com/en-us/server-cloud/cloud-os/global-datacenters.aspx)阅读有关我们使用的全球物理基础设施的信息。

## <a name="incident-management"></a>事件管理
OMS 具有所有 Microsoft 服务都遵循的事件管理过程。 总而言之，我们：

* 使用共担责任模型，其中一部分安全职责归于 Microsoft，一部分安全职责归于客户
* 管理 Azure 安全事件
  * 在检测到事件时启动调查
  * 由即时事件响应团队成员评估事件的影响和严重性。 基于证据，评估有可能会进一步上报给安全响应团队。
  * 由安全响应专家诊断事件，指导进行技术或取证调查，确定抑制、缓解和解决威胁的各种策略。 如果安全团队认为客户数据可能已泄漏给非法或未经授权的个人，会同时开始并行执行客户事件通知过程。  
  * 稳定事件并从中恢复。 事件响应团队会创建用于缓解问题的恢复计划。 诸如隔离受影响的系统之类的危机管理步骤可能会立即执行，同时开始进行诊断。 可能会计划眼前的风险解决之后采取的长期缓解措施。  
  * 关闭事件，并进行事后调查。 事件响应团队会进行事后调查，概述事件的细节，旨在修订策略、过程和流程以防止事件的再次发生。
* 通知客户安全事件
  * 确定受影响客户的范围，并向受影响的任何人提供尽可能详细的通知
  * 创建一条向客户提供足够详细信息的通知，以便他们可以执行对其终端的调查，并履行他们对其最终用户所做的任何承诺，同时不会不当地延迟通知流程。
  * 必要时确认并声明事件。
  * 告知客户事件通知，不会无故拖延，并遵循任何法律或合同承诺。 将以 Microsoft 选择的任何方式（包括通过电子邮件）向一个或多个客户的管理员发送安全事件的通知。
* 进行团队准备和培训
  * 会要求 Microsoft 员工完成安全和认知培训，这有助于他们识别并报告可疑的安全问题。  
  * 基于 Microsoft Azure 服务运作的运营商对于访问承载客户数据的敏感系统有额外的培训义务。
  * Microsoft 安全响应人员会接受针对其所担当角色的专门培训

如果发生任何客户数据丢失情况，我们会在一天内通知每个客户。 不过，使用 OMS 从未发生过客户数据丢失的情况。 此外，我们会保留已创建数据的副本，并且它在地理上分散。

有关 Microsoft 如何响应安全事件的详细信息，请参阅[在云中进行 Microsoft Azure 安全响应](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/1/Microsoft Azure Security Response in the cloud.pdf)。

## <a name="compliance"></a>合规性
OMS 软件开发和服务团队的信息安全和监管计划支持其业务需求，并遵循 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/)和 [Microsoft 信任中心合规性](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx)中所述的法律和法规。 其中还介绍了 OMS 如何建立安全要求、识别安全控制、管理和监控风险。 每年，我们都会对策略、标准、过程和指导原则进行评审。

每个 OMS 开发团队成员都会接受正式的应用程序安全培训。 在内部，我们将版本控制系统用于软件开发。 每个软件项目都受版本控制系统保护。

Microsoft 有安全性和合规性团队，负责对 Microsoft 中所有服务进行监控和评估。 由信息安全专员组成该团队，他们不会加入开发 OMS 的工程部门。 安全专员有其自己的管理链，负责对产品和服务进行独立评估，以确保安全性和合规性。

Microsoft 会向 Microsoft 董事会呈报有关所有信息安全计划的年度报告。

为了获得各种认证，OMS 软件开发和服务团队会积极地与 Microsoft 法律和合规性团队以及其他行业合作伙伴合作。

## <a name="certifications-and-attestations"></a>认证和证明
OMS Log Analytics 满足以下要求：

* [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/en-us/blog/iso22301/)
* PCI 安全标准委员会制定的[支付卡行业（PCI 标准）数据安全标准 (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI)。
* [服务组织控制 (SOC) 1 类型 1 和 SOC 2 类型 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) 标准
* [HIPAA 和 HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA)（对于具有 HIPAA 业务合作协议的公司）
* Windows 通用工程标准
* Microsoft 可信计算
* 作为一项 Azure 服务，OMS 使用的组件符合 Azure 合规性要求。 可以通过阅读 [Microsoft 信任中心合规性](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx)了解详细信息。

> [!NOTE]
> 在某些认证/证明中，Log Analytics 使用其以前的名称 *Operational Insights* 列出。
>
>


## <a name="cloud-computing-security-data-flow"></a>云计算安全数据流
下图将云安全体系结构显示为公司的信息流以及信息移动到 Log Analytics 服务（最终在 OMS 门户中呈现）时如何确保它的安全。 有关每一步的详细信息，请遵循该图所示。

![OMS 数据收集和安全性的图像](./media/log-analytics-security/log-analytics-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1.注册 Log Analytics 并收集数据
若要使组织将数据发送到 Log Analytics，请配置 Windows 代理、在 Azure 虚拟机上运行的代理或 OMS Agent for Linux。 如果使用 Operations Manager 代理，则你将在 Operations 控制台中使用配置向导对代理进行配置。 用户（可能是你、其他个别用户，也可能是一组用户）会创建一个或多个 OMS 帐户（即 OMS 工作区），然后使用以下帐户之一注册代理：

* [组织 ID](../active-directory/sign-up-organization.md)
* [Microsoft 帐户 - Outlook、Office Live、MSN](http://www.microsoft.com/account/default.aspx)

在 OMS 工作区中对数据进行收集、聚合、分析和呈现。 工作区主要用作划分数据的一种方式，每个工作区都是唯一的。 例如，可能希望生产数据受一个 OMS 工作区管理，测试数据受另一个工作区管理。 工作区还有助于管理员控制用户对数据的访问。 每个工作区可以有多个与它关联的用户帐户，每个用户帐户可以访问多个 OMS 工作区。 基于数据中心区域创建工作区。 会将每个工作区复制到区域中的其他数据中心，主要用于实现 OMS 服务可用性。

对于 Operations Manager，配置向导完成后，每个 Operations Manager 管理组会与 Log Analytics 服务建立连接。 然后可以使用“添加计算机”向导选择允许管理组中哪些计算机向该服务发送数据。 对于其他代理类型，每一个都会安全地连接到 OMS 服务。

会对已连接系统与 Log Analytics 服务之间的所有通信进行加密。  TLS (HTTPS) 协议用于加密。  遵循 Microsoft SDL 过程，已确保 Log Analytics 是最新的，加密协议也是最新的。

每种类型的代理都会收集 Log Analytics 的数据。 收集的数据类型取决于所用解决方案的类型。 可以查看[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中的数据收集的摘要。 此外，会针对大多数解决方案提供更详细的收集信息。 解决方案包含一组预定义的视图、日志搜索查询、数据收集规则和处理逻辑。 只有管理员可以使用 Log Analytics 导入解决方案。 解决方案完成导入后，会将它移到 Operations Manager 管理服务器（如果使用），然后再移到你选定的任何代理。 然后，该代理收集数据。

## <a name="2-send-data-from-agents"></a>2.从代理发送数据
将所有代理类型注册到加密密钥，会在代理和 Log Analytics 之间使用基于证书的身份验证和 SSL 以及端口 443 建立安全连接。 OMS 使用机密存储生成和维护密钥。 私钥每隔 90 天会进行轮换、存储在 Azure 中，并受遵循严格的法规和合规性操作的 Azure Operations 管理。

借助 Operations Manager，可以将工作区注册到 Log Analytics 服务，然后与 Operations Manager 管理服务器建立安全 HTTPS 连接。

对于在 Azure 虚拟机上运行的 Windows 代理，只读存储密钥用于读取 Azure 表中的诊断事件。

如果出于任何原因，任何代理都无法与该服务进行通信，已收集的数据会以本地方式存储在临时缓存中，然后管理服务器会尝试在两小时内每隔八分钟重新发送数据。 代理的缓存数据受操作系统的凭据存储的保护。 如果服务在两小时后无法处理数据，代理会将该数据加入队列。 如果队列已满，OMS 会开始丢弃数据类型，从性能数据开始。 代理队列限制是一个注册表项，因此必要时可以对它进行修改。 向服务发送（不通过本地数据库）已收集的数据时会先对数据进行压缩，因此不会向数据库添加任何负载。 已收集的数据完成发送后，会从缓存中删除它。

如上所述，通过 SSL 向 Microsoft Azure 数据中心发送你的代理中的数据。 可以选择使用 ExpressRoute 为数据提供额外的安全性。 借助 ExpressRoute，可以从网络服务提供商提供的现有 WAN 网络（例如多协议标签交换 (MPLS) VPN）直接连接到 Azure。 有关详细信息，请参阅 [ExpressRoute](https://azure.microsoft.com/services/expressroute/)。

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3.Log Analytics 服务接收并处理数据
Log Analytics 服务通过使用 Azure 身份验证对证书和数据完整性进行验证，来确保传入数据来自受信任的源。 将未处理的原始数据作为 blob 存储在 [Microsoft Azure 存储](../storage/storage-introduction.md)中，未对这些数据进行加密。 不过，每个 Azure 存储 blob 都具有一组唯一的密钥集，只允许相应用户进行访问。 存储的数据类型取决于导入的并用于收集数据的解决方案的类型。 然后，Log Analytics 服务处理 Azure 存储 blob 的原始数据。

## <a name="4-use-log-analytics-to-access-the-data"></a>4.使用 Log Analytics 访问数据
可以通过使用组织帐户或先前设置的 Microsoft 帐户登录到 OMS 门户中的 Log Analytics。 OMS 门户和 OMS 中的 Log Analytics 之间的所有通信流量都通过安全 HTTPS 通道进行发送。 使用 OMS 门户时，将在用户客户端（Web 浏览器）上生成会话 ID，会将数据存储在本地缓存中，直到该会话终止。 终止后，会删除该缓存。 不会自动删除不包含个人身份信息的客户端 Cookie。 会话 Cookie 标记为 HTTPOnly，并且受到保护。 在预先确定的空闲期过后，会终止 OMS 门户会话。

通过使用 OMS 门户，可以将数据导出为 CSV 文件，还可以使用搜索 API 访问数据。 CSV 导出限制为每次导出 50000 行，API 数据限制为每次搜索 5000 行。

## <a name="next-steps"></a>后续步骤
* [开始使用 Log Analytics](log-analytics-get-started.md)，以快速了解有关 Log Analytics 的更多信息并使 Log Analytics 开始运行。

