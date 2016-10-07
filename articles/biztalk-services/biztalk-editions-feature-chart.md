<properties
	pageTitle="了解 BizTalk 服务各版本中的功能 | Microsoft Azure"
	description="比较 BizTalk 服务各个版本的功能：免费版、开发人员版、基本版、标准版和高级版。MABS，WABS。"
	services="biztalk-services"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="erikre"
	editor=""/>

<tags
	ms.service="biztalk-services"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/15/2016"
	ms.author="mandia"/>


# BizTalk 服务：版本图表

Azure BizTalk 服务提供多个版本。借助本文确定适合你的方案和业务需求的版本。


## 比较版本

**免费(预览版)**

可以创建和管理混合连接。使用混合连接可以轻松地将 Azure 网站连接到 SQL Server 等本地系统。

**开发人员**

提供混合连接、EAI 和 EDI 消息处理功能，其中包括易于使用的贸易合作伙伴管理门户，并支持常见的 EDI 架构以及通过 X12 和 AS2 处理丰富的 EDI 内容。可创建通过任何 HTTP/S、REST、FTP、WCF 和 SFTP 协议连接云中服务以读写消息的常见 EAI 方案。利用与本地 LOB 系统的连接（通过随时可用的 SAP、Oracle eBusiness、Oracle DB、Siebel 和 SQL Server 适配器）。在以开发人员为中心的环境中，使用 Visual Studio 工具轻松地开发和部署。仅限开发和测试用途，不提供服务级别协议 (SLA)。

**基本**

包括开发人员版的大多数功能，还增加了混合连接、EAI 桥、EDI 协议和 BizTalk 适配器包连接。另外，还提供高可用性，以及用于缩放服务级别协议 (SLA) 的选项。

**标准**

包括基本版的所有功能，还增加了混合连接、EAI 桥、EDI 协议和 BizTalk 适配器包连接。另外，还提供高可用性，以及用于缩放服务级别协议 (SLA) 的选项。

**高级**

包括标准版的所有功能，还增加了混合连接、EAI 桥、EDI 协议和 BizTalk 适配器包连接。另外，还提供存档和高可用性，以及用于缩放服务级别协议 (SLA) 的选项。


## 版本图表
下表列出差异。

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>免费(预览版)</th>
        <th>开发人员</th>
        <th>基本</th>
        <th>标准</th>
        <th>高级</th>
</tr>

<tr>
<td><strong>起价</strong></td>
<td colspan="5"><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk 服务定价</a> <br/><br/> <a HREF="http://azure.microsoft.com/pricing/calculator/?scenario=full">Azure 价格计算器</a></td>
</tr>
<tr>
<td><strong>默认最低配置</strong></td>
<td>1 个免费版单位</td>
<td>1 个开发人员版单位</td>
<td>1 个基本版单位</td>
<td>1 个标准版单位</td>
<td>1 个高级版单位</td>
</tr>
<tr>
<td><strong>缩放</strong></td>
<td>无扩展</td>
<td>无扩展</td>
<td>可扩展，增量为 1 个基本版单位</td>
<td>可扩展，增量为 1 个标准版单位</td>
<td>可扩展，增量为 1 个高级版单位</td>
</tr>
<tr>
<td><strong>允许的最大扩大</strong></td>
<td>无扩展</td>
<td>无扩展</td>
<td>最多 8 个单位</td>
<td>最多 8 个单位</td>
<td>最多 8 个单位</td>
</tr>
<tr>
<td><strong>每个单位的 EAI 桥数</strong></td>
<td>不包括</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI，AS2</strong>
<br/><br/>
包括 TPM 协议</td>
<td>不包括</td>
<td>包括。每个单位 10 个协议。</td>
<td>包括。每个单位 50 个协议。</td>
<td>包括。每个单位 250 个协议。</td>
<td>包括。每个单位的 1000 协议数。</td>
</tr>
<tr>
<td><strong>每个单位的混合连接数</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>每个单位的混合连接数据传输 (GB)</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>本地 LOB 系统的 BizTalk 适配器服务连接数</strong></td>
<td>不包括</td>
<td>1 个连接</td>
<td>2 个连接</td>
<td>5 个连接</td>
<td>25 个连接</td>
</tr>
<tr>
<td align="left"><strong>支持的协议/系统：</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Service Bus (SB)</li>
<li>Azure Blob</li>
<li>REST API</li>
</ul>
</td>
<td>不包括</td>
<td>附送</td>
<td>附送</td>
<td>附送</td>
<td>附送</td>
</tr>
<tr>
<td><strong>高可用性</strong>
<br/><br/>
有关服务级别协议 (SLA)，请参阅 <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk 服务定价</a>。
</td>
<td>不包括</td>
<td>不包括</td>
<td>附送</td>
<td>附送</td>
<td>附送</td>
</tr>
<tr>
<td><strong>备份和还原</strong></td>
<td>不包括</td>
<td>附送</td>
<td>附送</td>
<td>附送</td>
<td>附送</td>
</tr>
<tr>
<td><strong>跟踪</strong></td>
<td>不包括</td>
<td>附送</td>
<td>附送</td>
<td>附送</td>
<td>附送</td>
</tr>
<tr>
<td><strong>存档</strong><br/><br/>
包括接收不可否认 (NRR) 和下载跟踪的消息</td>
<td>不包括</td>
<td>附送</td>
<td>不包括</td>
<td>不包括</td>
<td>附送</td>
</tr>
<tr>
<td><strong>使用自定义代码</strong></td>
<td>不包括</td>
<td>附送</td>
<td>附送</td>
<td>附送</td>
<td>附送</td>
</tr>
<tr>
<td><strong>使用转换，包括自定义 XSLT</strong></td>
<td>不包括</td>
<td>附送</td>
<td>附送</td>
<td>附送</td>
<td>附送</td>
</tr>
</table>

> [AZURE.NOTE] 为了针对硬件故障进行恢复，高可用性意味着一个 BizTalk 单位中包含多个 VM。


## 常见问题

#### BizTalk 单位是什么？
“单位”是 Azure BizTalk 服务部署的原子级别。每个版本均附带一个具有不同的计算能力和内存的单元。例如，基本单位具有比开发人员单位更多的计算，而标准单位具有比基本单位更多的计算，依此类推。缩放 BizTalk 服务时，将按单位进行缩放。

#### BizTalk 服务和 Azure BizTalk VM 之间的差别是什么？
BizTalk 服务提供一个真正的“平台即服务”(PaaS) 体系结构，用于在云中生成集成解决方案。使用 PaaS 模型，你可以将工作重心完全放在应用程序逻辑上，而将所有基础结构管理工作都留给 Microsoft 执行，包括：

- 无需管理或修补虚拟机。
- Microsoft 确保可用性。
- 通过 Azure 门户请求更多或更少容量即可按需控制缩放。

Azure 虚拟机上的 BizTalk Server 提供基础结构即服务 (IaaS) 体系结构。你可以完全像本地环境一样创建虚拟机和配置它们，从而更轻松地在云中运行现有应用程序而不必进行代码更改。使用 IaaS 时，仍由你负责配置和管理虚拟机（例如，安装软件和操作系统修补程序）以及相应地设计应用程序以实现高可用性。

如果你考虑生成新的集成解决方案以将基础结构管理工作减至最少，那么请使用 BizTalk 服务。如果你希望快速迁移现有 BizTalk 解决方案或寻找按需环境以开发和测试 BizTalk Server 应用程序，那么请在 Azure 虚拟机中使用 BizTalk Server。

#### BizTalk 适配器服务和混合连接之间的差别是什么？
BizTalk 适配器服务由 Azure BizTalk 服务使用。BizTalk 适配器服务使用 BizTalk 适配器包连接到本地业务线 (LOB) 系统。使用混合连接可以轻松方便地将 Azure 应用程序（例如 Azure 应用服务中的 Web 应用功能和 Azure 移动服务）连接到本地资源。

#### “每个单位的混合连接数据传输 (GB)”是什么意思？ 是指每分钟/小时/天/周/月吗？ 达到限制时会发生什么？

每个单位的混合连接成本取决于 BizTalk 服务的版本。简而言之，成本取决于传输的数据量。例如，每天传输 10 GB 数据的成本比每天传输 100 GB 数据的成本低。使用 BizTalk 服务[定价计算器](https://azure.microsoft.com/pricing/calculator/?scenario=full)来确定具体成本。通常情况下，系统会每天强制执行该限制。如果超出限制，会按 $1/GB 的费率对超出部分收费。

#### 在 BizTalk 服务中创建协议时，为什么桥数增加 2 而非 1？

每个协议都由两个不同的桥组成：一个发送端通信桥和一个接收端通信桥。

####  达到桥数量或协议数量的配额限制时会发生什么？

会无法部署任何新桥或创建任何新协议。若要部署更多桥，需要增加更多 BizTalk 服务单位，或升级到更高版本。

#### 如何从 BizTalk 服务的一个等级迁移到另一个等级？

免费版无法迁移或“升级”到其他层，也无法备份和还原到其他层。如果需要其他层，请使用新层创建新的 BizTalk 服务。必须在新的 BizTalk 服务中重新创建使用免费版创建的所有项目，包括混合连接。

对于其他版本，使用备份和还原将项目从一个层迁移到另一个层。例如，在标准层中备份项目，然后将其还原到高级层。[BizTalk 服务：备份和还原](biztalk-backup-restore.md)介绍支持的迁移路径，并列出所备份的项目。请注意，不会备份混合连接。在备份和还原到新层后，需要重新创建混合连接。


#### BizTalk 适配器服务是否包含在 BizTalk 服务内？ 我如何接收该软件？

包含在内，BizTalk 适配器服务以及 BizTalk 适配器包已包含在 Azure BizTalk 服务 SDK [下载](http://www.microsoft.com/download/details.aspx?id=39087)中。

## 后续步骤

若要在 Azure 门户中创建 Azure BizTalk 服务，请转到 [BizTalk 服务：使用 Azure 门户预配](biztalk-provision-services.md)。若要开始创建应用程序，请转到 [Azure BizTalk 服务](http://go.microsoft.com/fwlink/p/?LinkID=235197)。

## 其他资源
- [BizTalk 服务：使用 Azure 门户预配](biztalk-provision-services.md)<br/>
- [BizTalk 服务：预配状态图表](biztalk-service-state-chart.md)<br/>
- [Biztalk 服务：“仪表板”、“监视”和“缩放”选项卡](biztalk-dashboard-monitor-scale-tabs.md)<br/>
- [BizTalk 服务：备份和还原](biztalk-backup-restore.md)<br/>
- [BizTalk 服务：限制](biztalk-throttling-thresholds.md)<br/>
- [BizTalk 服务：颁发者名称和颁发者密钥](biztalk-issuer-name-issuer-key.md)<br/>
- [如何开始使用 Azure BizTalk 服务 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>

<!---HONumber=AcomDC_0921_2016-->