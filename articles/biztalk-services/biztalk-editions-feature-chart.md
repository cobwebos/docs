---
title: "了解 BizTalk 服务各版本中功能 |Microsoft 文档"
description: "比较 BizTalk 服务各版本的功能： 释放，开发人员、 基本、 标准和高级。 MABS，WABS。"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: c589629f-06b1-44bb-b8ca-1db71826ea59
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 718b57a801a9ba62a0154ae42da2ac0c0741f203
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="biztalk-services-editions-chart"></a>BizTalk 服务： 版本图表

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Azure BizTalk 服务提供多个版本。 使用本文来确定哪个版本最适合您的方案和业务需要。

## <a name="compare-the-editions"></a>比较版本
**免费 （预览版）**

可以创建和管理混合连接。 混合连接是连接到本地系统，类似于 SQL Server 的 Azure 网站的简单办法。

**开发人员**

通过 X12 和 AS2 包括能够轻松使用贸易合作伙伴管理门户，并支持常见的 EDI 架构的混合连接、 EAI 和 EDI 消息处理和丰富的 EDI 处理。 可以创建使用任何 HTTP/S、 REST、 FTP、 WCF 和 SFTP 协议读取和写入消息连接云中服务的常见 EAI 方案。  准备就绪，可以使用 SAP、 Oracle eBusiness、 Oracle DB、 Siebel 和 SQL Server 适配器利用与本地 LOB 系统连接。 使用与 Visual Studio 工具的开发人员为中心的环境，用于更轻松的开发和部署。 限制为仅与任何服务级别协议 (SLA) 的开发和测试目的。

**基本**

包括的大多数开发人员功能，还增加了混合连接、 EAI 桥、 EDI 协议和 BizTalk 适配器包的连接。 此外提供高可用性，以及用于缩放服务级别协议 (SLA) 选项。

**Standard**

包括所有基本功能，还增加了混合连接、 EAI 桥、 EDI 协议和 BizTalk 适配器包的连接。 此外提供高可用性，以及用于缩放服务级别协议 (SLA) 选项。

**高级**

包括所有标准功能，还增加了混合连接、 EAI 桥、 EDI 协议和 BizTalk 适配器包的连接。 此外包括存档、 高可用性和缩放服务级别协议 (SLA) 的选项。

## <a name="editions-chart"></a>版本图表
下表列出了这些差异。

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>免费 （预览版）</th>
        <th>开发人员</th>
        <th>基本</th>
        <th>Standard</th>
        <th>Premium</th>
</tr>

<tr>
<td><strong>起价</strong></td>
<td colspan="5"><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk 服务定价</a> <br/><br/> <a HREF="http://azure.microsoft.com/pricing/calculator/?scenario=full">Azure 定价计算器</a></td>
</tr>
<tr>
<td><strong>默认最小配置</strong></td>
<td>1 个免费版单位</td>
<td>1 开发人员版单位</td>
<td>1 个基本单位</td>
<td>1 个标准版单位</td>
<td>1 个高级版单位</td>
</tr>
<tr>
<td><strong>小数位数</strong></td>
<td>无扩展</td>
<td>无扩展</td>
<td>可扩展，增量为 1 的基本单位</td>
<td>可扩展，增量为 1 个标准版单位</td>
<td>可扩展，增量为 1 个高级版单位</td>
</tr>
<tr>
<td><strong>允许向外的扩展的最大</strong></td>
<td>无扩展</td>
<td>无扩展</td>
<td>最多 8 个单位</td>
<td>最多 8 个单位</td>
<td>最多 8 个单位</td>
</tr>
<tr>
<td><strong>每个单位的 EAI 桥</strong></td>
<td>不包含的信息</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI AS2</strong>
<br/><br/>
包括 TPM 协议</td>
<td>不包含的信息</td>
<td>包含。 每个单位 10 个协议。</td>
<td>包含。 每个单位的 50 协议。</td>
<td>包含。 每个单位的 250 协议。</td>
<td>包含。 每个单位的 1000年协议数。</td>
</tr>
<tr>
<td><strong>每个单位的混合连接</strong></td>
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
<td><strong>BizTalk 适配器服务连接到本地 LOB 系统</strong></td>
<td>不包含的信息</td>
<td>1 个连接</td>
<td>2 个连接</td>
<td>5 的连接</td>
<td>25 连接</td>
</tr>
<tr>
<td align="left"><strong>支持的协议/系统：</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>服务总线 (SB)</li>
<li>Azure Blob</li>
<li>REST Api</li>
</ul>
</td>
<td>不包含的信息</td>
<td>已包括</td>
<td>已包括</td>
<td>已包括</td>
<td>已包括</td>
</tr>
<tr>
<td><strong>高可用性</strong>
<br/><br/>
有关服务级别协议 (SLA)，请参阅<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk 服务定价</a>。
</td>
<td>不包含的信息</td>
<td>不包含的信息</td>
<td>已包括</td>
<td>已包括</td>
<td>已包括</td>
</tr>
<tr>
<td><strong>备份和还原</strong></td>
<td>不包含的信息</td>
<td>已包括</td>
<td>已包括</td>
<td>已包括</td>
<td>已包括</td>
</tr>
<tr>
<td><strong>跟踪</strong></td>
<td>不包含的信息</td>
<td>已包括</td>
<td>已包括</td>
<td>已包括</td>
<td>已包括</td>
</tr>
<tr>
<td><strong>存档</strong><br/><br/>
包括不可否认性 (NRR) 和下载跟踪的消息</td>
<td>不包含的信息</td>
<td>已包括</td>
<td>不包括</td>
<td>不包括</td>
<td>已包括</td>
</tr>
<tr>
<td><strong>使用自定义代码</strong></td>
<td>不包含的信息</td>
<td>已包括</td>
<td>已包括</td>
<td>已包括</td>
<td>已包括</td>
</tr>
<tr>
<td><strong>使用转换，包括自定义 XSLT</strong></td>
<td>不包含的信息</td>
<td>已包括</td>
<td>已包括</td>
<td>已包括</td>
<td>已包括</td>
</tr>
</table>

> [!NOTE]
> 以灵活应对硬件故障，高可用性意味着包含一个 BizTalk 单位中的多个 Vm。
> 
> 

## <a name="faqs"></a>常见问题
#### <a name="what-is-a-biztalk-unit"></a>BizTalk 单位是什么？
"单位"是 Azure BizTalk 服务部署的原子级别。 每个版本均附带一个具有不同的计算能力和内存的单元。 例如，基本单位具有比开发人员的多个计算，标准版具有更多计算比基本，依次类推。 当你缩放 BizTalk 服务时，将按单位缩放。

#### <a name="what-is-the-difference-between-biztalk-services-and-azure-biztalk-vm"></a>BizTalk 服务和 Azure BizTalk VM 之间的区别是什么？
BizTalk 服务用于构建集成解决方案在云中提供一个真正的平台作为-服务 (PaaS) 体系结构。 使用 PaaS 模型中，完全专注于应用程序逻辑和所有基础结构管理留给 Microsoft，包括：

* 无需管理或修补虚拟机。
* Microsoft 确保可用性。
* 控制缩放。 按需只需请求更多或更少容量通过 Azure 门户。

Azure 虚拟机上的 BizTalk Server 提供基础结构作为-服务 (IaaS) 体系结构。 你创建虚拟机和配置它们完全一样在本地环境，使其更方便地在云中，而不需要更改代码中运行现有应用程序。 使用 IaaS，你负责仍配置虚拟机、 管理虚拟机 （例如，安装软件和操作系统修补程序） 和构建应用程序的高可用性。

如果你正在寻求通过生成最小化你的基础结构管理工作的新集成解决方案，使用 BizTalk 服务。 如果你希望快速迁移现有 BizTalk 解决方案或寻找按需环境以开发和测试 BizTalk Server 应用程序，使用 BizTalk Server 在 Azure 虚拟机。

#### <a name="what-is-the-difference-between-biztalk-adapter-service-and-hybrid-connections"></a>BizTalk 适配器服务和混合连接之间的区别是什么？
Azure BizTalk 服务使用 BizTalk 适配器服务。 BizTalk 适配器服务使用 BizTalk 适配器包连接到本地业务线 (LOB) 系统。 混合连接提供了一种简单且方便的方法，以连接到本地资源的 Azure 应用程序，如在 Azure App Service 和 Azure 移动服务中的 Web Apps 功能。

#### <a name="what-does-hybrid-connection-data-transfer-gb-per-unit-mean-is-this-per-minutehourdayweekmonth-what-happens-when-the-limit-is-reached"></a>"混合连接数据传输 (GB) 每个单位"是什么意思？ 这是每分钟/小时/天/一周/月？ 达到限制时，会发生什么情况？
每个单位的混合连接成本取决于 BizTalk 服务版本。 简单地说，成本取决于传输上的数据量。 例如，每天将 10 GB 数据传输成本更低比每日传输 100 GB。 使用[定价计算器](https://azure.microsoft.com/pricing/calculator/?scenario=full)BizTalk 服务，以确定特定的成本。 通常情况下，限制会强制执行每日。 如果超出了限制，则任何超额收费的每个 GB $1 的速率。

#### <a name="when-i-create-an-agreement-in-biztalk-services-why-does-the-number-of-bridges-go-up-by-two-instead-of-just-one"></a>在 BizTalk 服务中创建协议，为什么未桥数转到上由两个而并非仅仅一个？
每个协议的两个不同的桥的过程包括： 发送端通信桥和接收端通信桥。

#### <a name="what-happens-when-i-hit-the-quota-limit-on-the-number-of-bridges-or-agreements"></a>我命中的桥或协议数的配额限制时，会发生什么情况？
你不能部署任何新桥或创建任何新协议。 若要部署的详细信息，你需要向上扩展到更多单位的 BizTalk 服务，或升级到更高版本。

#### <a name="how-do-i-migrate-from-one-tier-of-biztalk-services-to-another"></a>如何迁移从 BizTalk 服务的一个层到另一个？
可用的版本不能迁移或到另一个层，缩放和不能进行备份和还原到另一层。 如果你需要另一层，创建新的 BizTalk 服务使用新的层。 使用免费版，包括混合连接，创建任何项目需要新的 BizTalk 服务中重新创建。 

对于剩余的版本中，使用备份和还原将你的项目从一个等级迁移到另一个。 例如，备份在标准层中，你的项目，然后将它们还原到高级层。 [BizTalk 服务： 备份和还原](biztalk-backup-restore.md)介绍支持的迁移路径，并列出了哪些项目进行备份。 请注意，混合连接不会备份。 备份和还原到新层之后, 你然后重新创建混合连接。  

#### <a name="is-the-biztalk-adapter-service-included-in-the-service-how-do-i-receive-the-software"></a>是包含在服务中的 BizTalk 适配器服务？ 如何接收该软件？
是，BizTalk 适配器服务以及 BizTalk 适配器包都包含在 Azure BizTalk 服务 SDK[下载](http://www.microsoft.com/download/details.aspx?id=39087)。

## <a name="next-steps"></a>后续步骤
若要在 Azure 门户中创建 Azure BizTalk 服务，请转到[BizTalk 服务： 使用 Azure 门户进行设置](biztalk-provision-services.md)。 若要开始创建应用程序，请转到[Azure BizTalk 服务](http://go.microsoft.com/fwlink/p/?LinkID=235197)。

## <a name="additional-resources"></a>其他资源
* [使用 Azure 门户进行设置 BizTalk 服务：](biztalk-provision-services.md)<br/>
* [BizTalk 服务： 设置状态图表](biztalk-service-state-chart.md)<br/>
* [BizTalk 服务: 仪表板、 监视器和缩放选项卡](biztalk-dashboard-monitor-scale-tabs.md)<br/>
* [BizTalk 服务： 备份和还原](biztalk-backup-restore.md)<br/>
* [BizTalk 服务： 限制](biztalk-throttling-thresholds.md)<br/>
* [BizTalk 服务： 颁发者名称和颁发者密钥](biztalk-issuer-name-issuer-key.md)<br/>
* [如何开始使用 Azure BizTalk 服务 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>

