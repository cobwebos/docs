---
title: Log Analytics 数据安全 | Microsoft 文档
description: 了解 Log Analytics 如何保护隐私和数据安全。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: a33bb05d-b310-4f2c-8f76-f627e600c8e7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 4cf04ceeb8650b2978389cefb561ae31e88bc853
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282430"
---
# <a name="log-analytics-data-security"></a>Log Analytics 数据安全
本文档旨在提供特定于 Azure Log Analytics 的信息来补充有关 [Azure 信任中心](../security/security-microsoft-trust-center.md)的信息。  

本文介绍 Log Analytics 如何收集、处理和保护数据。 可以使用代理连接到 Web 服务、使用 System Center Operations Manager 收集操作数据或从 Azure 诊断中检索数据供 Log Analytics 使用。 

Log Analytics 服务使用以下方法安全地管理你基于云的数据：

* 数据隔离
* 数据保留
* 物理安全性
* 事件管理
* 合规性
* 安全标准认证

如有任何有关以下任何信息（包括 [Azure 支持选项](http://azure.microsoft.com/support/options/)的安全政策）的疑问、建议或问题，请与我们联系。

## <a name="sending-data-securely-using-tls-12"></a>使用 TLS 1.2 安全地发送数据 

为了确保传输到 Log Analytics 的数据的安全性，我们强烈建议你将代理配置为至少使用传输层安全性 (TLS) 1.2。 我们发现旧版 TLS/安全套接字层 (SSL) 容易受到攻击，尽管出于向后兼容，这些协议仍可正常工作，但我们**不建议使用**，并且行业即将放弃对这些旧协议的支持。 

[PCI 安全标准委员会](https://www.pcisecuritystandards.org/)规定 [2018 年 6 月 30 日](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf)是停用旧版 TLS/SSL 并升级到更安全协议的截止时间。 在 Azure 放弃旧版支持后，如果代理无法通过最低版本 TLS 1.2 进行通信，则你无法将数据发送到 Log Analytics。 

除非绝对必要，否则我们不建议将代理显式设置为仅使用 TLS 1.2，因为这可能会破坏平台级安全功能，导致无法自动检测并利用推出的更新且更安全的协议，例如 TLS 1.3。 

### <a name="platform-specific-guidance"></a>平台特定的指南

|平台/语言 | 支持 | 更多信息 |
| --- | --- | --- |
|Linux | Linux 分发版往往依赖于 [OpenSSL](https://www.openssl.org) 来提供 TLS 1.2 支持。  | 请检查 [OpenSSL 变更日志](https://www.openssl.org/news/changelog.html)，确认你的 OpenSSL 版本是否受支持。|
| Windows 8.0 - 10 | 受支持，并且默认已启用。 | 确认是否仍在使用[默认设置](https://docs.microsoft.com/en-us/windows-server/security/tls/tls-registry-settings)。  |
| Windows Server 2012 - 2016 | 受支持，并且默认已启用。 | 确认是否仍在使用[默认设置](https://docs.microsoft.com/en-us/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 和 Windows Server 2008 R2 SP1 | 受支持，但默认未启用。 | 有关启用方法的详细信息，请参阅[传输层安全性 (TLS) 注册表设置](https://docs.microsoft.com/en-us/windows-server/security/tls/tls-registry-settings)页。  |
| Windows Server 2008 SP2 | 对 TLS 1.2 的支持需要更新。 | 请参阅 Windows Server 2008 SP2 中的[更新以添加对 TLS 1.2 的支持](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s)。 |

## <a name="data-segregation"></a>数据隔离
Log Analytics 服务引入数据后，数据将在该服务的每个组件上都保持逻辑隔离。 所有数据按工作区进行标记。 此标记方式贯穿数据的整个生命周期，在服务的每个层强制实施。 数据存储在所选区域的群集存储中的专用数据库内。

## <a name="data-retention"></a>数据保留
编入索引的日志搜索数据基于定价计划进行存储和保留。 有关详细信息，请参阅 [Log Analytics 定价](https://azure.microsoft.com/pricing/details/log-analytics/)。

在履行[订阅协议](https://azure.microsoft.com/support/legal/subscription-agreement/)的过程中，Microsoft 会根据协议条款保留数据。  删除数据时，Microsoft 也会删除承载该数据的 Azure 存储帐户。  在删除客户数据时，不会销毁任何物理驱动器。  

下表列出了可用的一些解决方案并提供了它们会收集的数据类型示例。

| **解决方案** | **数据类型** |
| --- | --- |
| 容量和性能 |性能数据和元数据 |
| 更新管理 |元数据和状态数据 |
| 日志管理 |用户定义的事件日志、Windows 事件日志和/或 IIS 日志 |
| 更改跟踪 |软件库存、Windows 服务和 Linux 守护程序元数据，以及 Windows/Linux 文件元数据 |
| SQL 和 Active Directory 评估 |WMI 数据、注册表数据、性能数据和 SQL Server 动态管理视图结果 |

下表显示了数据类型的示例：

| **数据类型** | **字段** |
| --- | --- |
| 警报 |Alert Name、Alert Description、BaseManagedEntityId、Problem ID、IsMonitorAlert、RuleId、ResolutionState、Priority、Severity、Category、Owner、ResolvedBy、TimeRaised、TimeAdded、LastModified、LastModifiedBy、LastModifiedExceptRepeatCount、TimeResolved、TimeResolutionStateLastModified、TimeResolutionStateLastModifiedInDB、RepeatCount |
| 配置 |CustomerID、AgentID、EntityID、ManagedTypeID、ManagedTypePropertyID、CurrentValue、ChangeDate |
| 事件 |EventId、EventOriginalID、BaseManagedEntityInternalId、RuleId、PublisherId、PublisherName、FullNumber、Number、Category、ChannelLevel、LoggingComputer、EventData、EventParameters、TimeGenerated、TimeAdded <br>**注意：** 当将具有自定义字段的事件写入到 Windows 事件日志中时，Log Analytics 会收集它们。 |
| 元数据 |BaseManagedEntityId、ObjectStatus、OrganizationalUnit、ActiveDirectoryObjectSid、PhysicalProcessors、NetworkName、IPAddress、ForestDNSName、NetbiosComputerName、VirtualMachineName、LastInventoryDate、HostServerNameIsVirtualMachine、IP Address、NetbiosDomainName、LogicalProcessors、DNSName、DisplayName、DomainDnsName、ActiveDirectorySite、PrincipalName、OffsetInMinuteFromGreenwichTime |
| 性能 |ObjectName、CounterName、PerfmonInstanceName、PerformanceDataId、PerformanceSourceInternalID、SampleValue、TimeSampled、TimeAdded |
| 省/直辖市/自治区 |StateChangeEventId、StateId、NewHealthState、OldHealthState、Context、TimeGenerated、TimeAdded、StateId2、BaseManagedEntityId、MonitorId、HealthState、LastModified、LastGreenAlertGenerated、DatabaseTimeModified |

## <a name="physical-security"></a>物理安全性
Log Analytics 服务由 Microsoft 人员负责操控，将记录所有活动并且可进行审核。 Log Analytics 作为 Azure 服务运行，满足所有 Azure 符合性与安全要求。 可以在 [Microsoft Azure 安全性概述](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf)的第 18 页上找到有关 Azure 资产的物理安全性的详细信息。 对于不再负责 Log Analytics 服务的任何人员，会在一个工作日内更改该人员对安全区域的物理访问权限，包括传输和终止。 可在 [Microsoft 数据中心](https://azure.microsoft.com/en-us/global-infrastructure/)阅读有关我们使用的全球物理基础设施的信息。

## <a name="incident-management"></a>事件管理
Log Analytics 具有所有 Microsoft 服务都遵循的事件管理过程。 总而言之，我们：

* 使用共担责任模型，其中一部分安全职责归于 Microsoft，一部分安全职责归于客户
* 管理 Azure 安全事件：
  * 在检测到事件时启动调查
  * 由即时事件响应团队成员评估事件的影响和严重性。 基于证据，评估有可能会进一步上报给安全响应团队。
  * 由安全响应专家诊断事件，指导进行技术或取证调查，确定抑制、缓解和解决威胁的各种策略。 如果安全团队认为客户数据可能已泄漏给非法或未经授权的个人，会同时开始并行执行客户事件通知过程。  
  * 稳定事件并从中恢复。 事件响应团队会创建用于缓解问题的恢复计划。 诸如隔离受影响的系统之类的危机管理步骤可能会立即执行，同时开始进行诊断。 可能会计划眼前的风险解决之后采取的长期缓解措施。  
  * 关闭事件，并进行事后调查。 事件响应团队会进行事后调查，概述事件的细节，旨在修订策略、过程和流程以防止事件的再次发生。
* 通知客户安全事件：
  * 确定受影响客户的范围，并向受影响的任何人提供尽可能详细的通知
  * 创建一条向客户提供足够详细信息的通知，以便他们可以执行对其终端的调查，并履行他们对其最终用户所做的任何承诺，同时不会不当地延迟通知流程。
  * 必要时确认并声明事件。
  * 告知客户事件通知，不会无故拖延，并遵循任何法律或合同承诺。 将以 Microsoft 选择的任何方式（包括通过电子邮件）向一个或多个客户的管理员发送安全事件的通知。
* 进行团队准备和培训：
  * 会要求 Microsoft 员工完成安全和认知培训，这有助于他们识别并报告可疑的安全问题。  
  * 基于 Microsoft Azure 服务运作的运营商对于访问承载客户数据的敏感系统有额外的培训义务。
  * Microsoft 安全响应人员会接受针对其所担当角色的专门培训

如果发生任何客户数据丢失情况，我们会在一天内通知每个客户。 不过，使用服务从未发生过客户数据丢失的情况。 

有关 Microsoft 如何响应安全事件的详细信息，请参阅[在云中进行 Microsoft Azure 安全响应](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf)。

## <a name="compliance"></a>合规性
Log Analytics 软件开发和服务团队的信息安全和监管计划支持其业务需求，并遵循 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/)和 [Microsoft 信任中心符合性](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx)中所述的法律和法规。 其中还介绍了 Log Analytics 如何建立安全要求、识别安全控制、管理和监控风险。 每年，我们都会对策略、标准、过程和指导原则进行评审。

每个开发团队成员都会接受正式的应用程序安全培训。 在内部，我们将版本控制系统用于软件开发。 每个软件项目都受版本控制系统保护。

Microsoft 有安全性和合规性团队，负责对 Microsoft 中所有服务进行监控和评估。 由信息安全专员组成该团队，他们不会加入开发 Log Analytics 的工程部门。 安全专员有其自己的管理链，负责对产品和服务进行独立评估，以确保安全性和合规性。

Microsoft 会向 Microsoft 董事会呈报有关所有信息安全计划的年度报告。

为了获得各种认证，Log Analytics 软件开发和服务团队正积极与 Microsoft 法律和法规团队以及其他行业合作伙伴合作。

## <a name="certifications-and-attestations"></a>认证和证明
Azure Log Analytics 满足以下要求：

* [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* PCI 安全标准委员会制定的[支付卡行业（PCI 标准）数据安全标准 (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI)。
* [服务组织控制 (SOC) 1 类型 1 和 SOC 2 类型 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) 标准
* [HIPAA 和 HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa)（对于具有 HIPAA 业务合作协议的公司）
* Windows 通用工程标准
* Microsoft 可信计算
* 作为一项 Azure 服务，Log Analytics 使用的组件满足 Azure 符合性要求。 可以通过阅读 [Microsoft 信任中心合规性](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx)了解详细信息。

> [!NOTE]
> 在某些认证/证明中，Log Analytics 使用其以前的名称 *Operational Insights* 列出。
>
>

## <a name="cloud-computing-security-data-flow"></a>云计算安全数据流
下图将云安全体系结构显示为公司的信息流以及信息移动到 Log Analytics 服务（最终在 Azure 门户中呈现）时如何确保它的安全。 有关每一步的详细信息，请遵循该图所示。

![Log Analytics 数据收集和安全性的图像](./media/log-analytics-data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1.注册 Log Analytics 并收集数据
要使组织能够将数据发送到 Log Analytics，请配置在 Azure 虚拟机中运行的，或者在自己环境或其他云提供程序中的虚拟机或物理机上运行的 Windows/Linux 代理。  如果使用 Operations Manager，请通过管理组配置 Operations Manager 代理。 用户（可能是你、其他个别用户，也可能是一组用户）会创建一个或多个 Log Analytics 工作区，并使用以下帐户之一注册代理：

* [组织 ID](../active-directory/fundamentals/sign-up-organization.md)
* [Microsoft 帐户 - Outlook、Office Live、MSN](https://account.microsoft.com/account)

在 Log Analytics 工作区中对数据进行收集、聚合、分析和呈现。 工作区主要用作划分数据的一种方式，每个工作区都是唯一的。 例如，可能希望生产数据受一个工作区管理，测试数据受另一个工作区管理。 工作区还有助于管理员控制用户对数据的访问。 每个工作区可以有多个与它关联的用户帐户，每个用户帐户可以访问多个 Log Analytics 工作区。 基于数据中心区域创建工作区。 会将每个工作区复制到区域中的其他数据中心，主用于实现 Log Analytics 服务可用性。

对于 Operations Manager，Manager 管理组会与 Log Analytics 服务建立连接。 然后，可以配置允许管理组中的哪些代理托管系统收集数据以及向服务发送数据。 根据已启用的解决方案，这些解决方案的数据可直接从 Operations Manager 管理服务器发送到 Log Analytics 服务，或者出于在代理管理系统上收集的数据量，直接从代理发送到服务。 对于不受 Operations Manager 监视的系统，每个系统可以安全地直接连接到 Log Analytics 服务。

会对已连接系统与 Log Analytics 服务之间的所有通信进行加密。 TLS (HTTPS) 协议用于加密。  遵循 Microsoft SDL 过程，已确保 Log Analytics 是最新的，加密协议也是最新的。

每种类型的代理都会收集 Log Analytics 的数据。 收集的数据类型取决于所用解决方案的类型。 可以查看[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中的数据收集的摘要。 此外，会针对大多数解决方案提供更详细的收集信息。 解决方案包含一组预定义的视图、日志搜索查询、数据收集规则和处理逻辑。 只有管理员可以使用 Log Analytics 导入解决方案。 解决方案完成导入后，会将它移到 Operations Manager 管理服务器（如果使用），再移到你选定的任何代理。 然后，该代理收集数据。

## <a name="2-send-data-from-agents"></a>2.从代理发送数据
将所有代理类型注册到加密密钥，会在代理和 Log Analytics 之间使用基于证书的身份验证和 SSL 以及端口 443 建立安全连接。 Log Analytics 使用机密存储生成和维护密钥。 私钥每隔 90 天会进行轮换、存储在 Azure 中，并受遵循严格的法规和合规性操作的 Azure Operations 管理。

借助 Operations Manager，注册到 Log Analytics 工作区的管理组可与 Operations Manager 管理服务器建立安全 HTTPS 连接。

对于在 Azure 虚拟机上运行的 Windows 或 Linux 代理，只读存储密钥用于读取 Azure 表中的诊断事件。  

由于任何代理会向与 Log Analytics 集成的 Operations Manager 管理组报告，因此，如果管理服务器出于任何原因无法与该服务通信，收集的数据将存储在管理服务器本地的临时缓存中。   管理服务器尝试在两小时内每隔八分钟重新发送数据。  对于绕过管理服务器直接发送到 Log Analytics 的数据，行为与 Windows 代理一致。  

Windows 或管理服务器代理缓存的数据受操作系统的凭据存储的保护。 如果服务在两小时后无法处理数据，代理会将该数据加入队列。 如果队列已满，代理会开始丢弃数据类型，从性能数据开始。 代理队列限制是一个注册表项，因此必要时可以对它进行修改。 向服务发送（绕过 Operations Manager 管理组数据库）已收集的数据时会先对数据进行压缩，因此不会向数据库添加任何负载。 已收集的数据完成发送后，会从缓存中删除它。

如上所述，来自管理服务器或直连代理的数据通过 SSL 发送到 Microsoft Azure 数据中心。 可以选择使用 ExpressRoute 为数据提供额外的安全性。 借助 ExpressRoute，可以从网络服务提供商提供的现有 WAN 网络（例如多协议标签交换 (MPLS) VPN）直接连接到 Azure。 有关详细信息，请参阅 [ExpressRoute](https://azure.microsoft.com/services/expressroute/)。

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3.Log Analytics 服务接收并处理数据
Log Analytics 服务通过使用 Azure 身份验证对证书和数据完整性进行验证，来确保传入数据来自受信任的源。 然后，将未处理的原始数据存储在区域中的 Azure 事件中心，并最终存储静态数据。 存储的数据类型取决于导入的并用于收集数据的解决方案的类型。 然后，Log Analytics 服务处理原始数据并将其引入数据库。

存储在数据库中的已收集数据的保留期取决于所选的定价计划。 对于*免费*层，收集的数据可以使用 7 天。 对于*付费*层，收集的数据默认情况下可以使用 31 天，但可以延长到 730 天。 数据以静态加密方式存储在 Azure 存储中，以确保数据保密性。 过去两周的数据也存储在基于 SSD 的缓存中，此缓存当前未加密。  我们计划在 2018 年下半年支持此加密。  

## <a name="4-use-log-analytics-to-access-the-data"></a>4.使用 Log Analytics 访问数据
若要访问 Log Analytics 工作区，请使用组织帐户或先前设置的 Microsoft 帐户登录到 Azure 门户。 门户与 Log Analytics 服务之间的所有流量通过安全 HTTPS 通道发送。 使用门户时，会在用户客户端（Web 浏览器）上生成会话 ID，会将数据存储在本地缓存中，直到该会话终止。 终止后，会删除该缓存。 不会自动删除不包含个人身份信息的客户端 Cookie。 会话 Cookie 标记为 HTTPOnly，并且受到保护。 在预先确定的空闲期过后，会终止 Azure 门户会话。

## <a name="next-steps"></a>后续步骤
* 遵循 [Azure VM 快速入门](log-analytics-quick-collect-azurevm.md)，了解如何使用 Log Analytics 收集 Azure VM 的数据。  

*  如果想要从环境中的 Windows 或 Linux 物理机或虚拟机收集数据，请参阅[适用于 Linux 计算机的快速入门](log-analytics-quick-collect-linux-computer.md)或[适用于 Windows 计算机的快速入门](log-analytics-quick-collect-windows-computer.md)

