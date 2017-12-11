---
title: "适用于 Hyper-V 到 Azure 部署的 Azure Site Recovery 部署规划器成本估算详细信息 | Microsoft Docs"
description: "本文介绍使用适用于 Hyper-V 到 Azure 方案的 Azure Site Recovery 部署规划器生成的报表的成本估算详细信息。"
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/02/2017
ms.author: nisoneji
ms.openlocfilehash: 47cdbf31e6b01055405cefedda11d5eeef82f32e
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2017
---
# <a name="cost-estimation-report-of-azure-site-recovery-deployment-planner"></a>Azure Site Recovery 部署规划器成本估算报表  

部署规划器报表以[建议](site-recovery-hyper-v-deployment-planner-analyze-report.md#recommendations)表的形式提供成本估算摘要，以“成本估算”表的形式提供详细的成本分析。 它按 VM 提供详细的成本分析。 

### <a name="cost-estimation-summary"></a>成本估算摘要 
下图显示的摘要视图包含灾难恢复 (DR) 到所选目标区域的 Azure 时的总估算成本，以及生成报表时所用的指定货币。
![成本估算摘要](media/site-recovery-hyper-v-deployment-planner-analyze-report/cost-estimation-summary-h2a.png) 可以通过此摘要了解在使用 Azure Site Recovery 将所有兼容的 VM 转移到 Azure 进行保护时，需要支付的存储、计算、网络和许可证成本。 计算成本时，只针对兼容的 VM，不针对所有受分析的 VM。  
 
可以按月或按年查看成本。 详细了解[支持的目标区域](./site-recovery-hyper-v-deployment-planner-cost-estimation.md#supported-target-regions)和[支持的货币](./site-recovery-hyper-v-deployment-planner-cost-estimation.md#supported-currencies)。

**按组件成本**：总 DR 成本分为四个部分：计算成本、存储成本、网络成本和 Azure Site Recovery 许可证成本。 成本计算基于在复制时和 DR 演练时上述四个部分（计算、存储（高级和标准）、在本地站点和 Azure 之间配置的 ExpressRoute/VPN，以及 Azure Site Recovery 许可证）所对应的使用量。

**按状态成本**：总灾难恢复 (DR) 成本按两种不同的状态（“复制”和“DR 演练”）分类。 

**复制成本**：复制期间产生的成本， 涵盖存储成本、网络成本、Azure Site Recovery 许可证成本。 

**DR 演练成本**：测试性故障转移期间产生的成本。 Azure Site Recovery 会在测试性故障转移期间启动 VM。 DR 演练成本涵盖正在运行的 VM 的计算和存储成本。 

**每月/年的 Azure 存储成本**：表示针对高级和标准存储进行复制和 DR 演练时产生的总存储成本。

## <a name="detailed-cost-analysis"></a>详细成本分析
计算、存储、网络等方面的 Azure 价格因 Azure 区域而异。 可以根据订阅、与订阅相关联的产品/服务、指定的目标 Azure 区域和指定的货币，使用最新的 Azure 价格生成成本估算报表。 默认情况下，此工具使用“美国西部 2”Azure 区域和美元 (USD) 货币。 如果使用了任何其他区域和货币，则下一次在不使用订阅 ID、产品/服务 ID、目标区域和货币的情况下生成报表时，该报表会使用上次使用的目标区域和上次使用的货币所对应的价格进行成本估算。
此部分显示用于生成报表的订阅 ID 和产品/服务 ID。  如果某项未使用过，则为空白。

在整个报表中，标为灰色的单元格为只读。 白色的单元格可以根据要求修改。

![成本估算详细信息 1](media/site-recovery-hyper-v-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-cost-by-components"></a>按组件估算的总体 DR 成本
第一部分显示按组件估算的总体 DR 成本和按状态估算的总体 DR 成本。 

**计算**：根据 DR 需要在 Azure 上运行的 IaaS VM 的成本， 其中包括由 Azure Site Recovery 在 DR 演练（测试性故障转移）期间创建的 VM，以及在 Azure 上运行的 VM，例如启用了 Always On 可用性组和域控制器/域名服务器的 SQL Server。

**存储**：符合 DR 需要的 Azure 存储使用成本， 其中包括用于复制和 DR 演练的存储。
网络：符合 DR 需要的 ExpressRoute 和站点到站点 VPN 成本。 

**ASR 许可证**：针对所有兼容 VM 的 Azure Site Recovery 许可证成本。 如果在详细的成本分析表中手动输入了一个 VM，则还会包括该 VM 的 Azure Site Recovery 许可证成本。

### <a name="overall-dr-cost-by-states"></a>按状态估算的总体 DR 成本
总 DR 成本按两种不同的状态（“复制”和“DR 演练”）分类。

**复制成本**：在复制时产生的成本。 涵盖存储成本、网络成本、Azure Site Recovery 许可证成本。 

**DR 演练成本**：在进行 DR 演练时产生的成本。 Azure Site Recovery 会在 DR 演练期间启动 VM。 DR 演练成本涵盖正在运行的 VM 的计算和存储成本。
一年的总 DR 演练持续时间 = DR 演练次数 x 每次 DR 演练的持续时间（天） 平均 DR 演练成本（每月）= 总 DR 演练成本/12

### <a name="storage-cost-table"></a>存储成本表：
此表显示在有和没有折扣的情况下，进行复制和 DR 演练时高级存储和标准存储的成本。

### <a name="site-to-azure-network"></a>站点到 Azure 网络
根据要求选择适当的设置。 

**ExpressRoute**：默认情况下，此工具选择最接近的 ExpressRoute 计划，只要该计划符合进行增量复制所需的网络带宽条件即可。 可以根据要求更改此计划。

**VPN 网关**：选择 VPN 网关（如果环境中有）。 默认为不可用。

**目标区域**：指定用于 DR 的 Azure 区域。 报表中使用的计算、存储、网络和许可证价格基于该区域的 Azure 定价。 

### <a name="vm-running-on-azure"></a>在 Azure 上运行的 VM
如果有任何域控制器或 DNS VM 或 SQL Server VM 的 Always On 可用性组运行在用于 DR 的 Azure 上，则可提供 VM 数目和大小，以便估算在总 DR 成本中，其计算成本为多少。 

### <a name="apply-overall-discount-if-applicable"></a>在适用情况下应用总折扣
如果你是 Azure 合作伙伴或客户，有资格获得 Azure 总体定价方面的折扣，则可使用此字段。 此工具对所有组件应用该折扣（以 % 表示）。

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>按类型的虚拟机数和计算成本（每年）
此表显示 Windows 和非 Windows VM 数及其 DR 演练计算成本。

### <a name="settings"></a>设置 
**使用托管磁盘**：指定是否在 DR 演练时使用托管磁盘。 默认值为“是”。 如果已将 -UseManagedDisks 设置为“No”，则会使用非托管磁盘价格来计算成本。

**货币**：生成报表时使用的货币。 成本持续时间：可以查看一个月或一整年的所有成本。 

## <a name="detailed-cost-analysis-table"></a>详细成本分析表
![详细成本分析](media/site-recovery-hyper-v-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png) 此表列出了每个兼容 VM 的成本明细。 也可通过手动添加 VM 的方式，使用此表获取非分析 VM 的 Azure DR 估算成本。 在需要为新的灾难恢复部署估算 Azure 成本而又不做详细分析的情况下，这很有用。
若要手动添加 VM，请执行以下操作： 
1.  单击“插入行”按钮，在“开始”行与“结束”行之间插入新行。

2.  根据与此配置相符的大致 VM 大小和 VM 数填充以下列： 

* VM 数、IaaS 大小(你的选择)
* 存储类型(标准/高级)
* VM 总存储大小(GB)
* 一年的 DR 演练次数 
* 每次 DR 演练的持续时间(天) 
* OS 类型
* 数据冗余 
* Azure 混合使用权益

3.  可以将同一值应用到表中的所有 VM，只需针对“一年的 DR 演练次数”、“每次 DR 演练的持续时间(天)”、“数据冗余”、“Azure 混合使用权益”单击“应用到所有项”按钮即可。

4.  单击“重新计算成本”即可更新成本。

**VM 名称**：VM 的名称。

**VM 数**：与配置相符的 VM 数。 可以更新现有 VM 的数目，前提是不对类似的配置 VM 进行分析，而会对其进行保护。

**IaaS 大小(建议)**：此工具建议的兼容 VM 的 VM 角色大小。 

**IaaS 大小(你的选择)**：默认情况下与建议的 VM 角色大小相同。 可以根据需要更改角色。 计算成本取决于所选 VM 角色大小。

**存储类型**：VM 使用的存储类型， 可以是标准存储或高级存储。

**VM 总存储大小(GB)**：VM 的总存储。

**一年的 DR 演练次数**：一年中进行 DR 演练的次数， 默认为一年 4 次。 可以修改特定 VM 的持续时间或将新值应用到所有 VM，只需在顶部行中输入新值，然后单击“应用到所有项”按钮即可。 可以根据一年的 DR 演练次数和每次 DR 演练的持续时间来计算总 DR 演练成本。  

**每次 DR 演练的持续时间(天)**：每次 DR 演练的持续时间。 默认情况下，根据[灾难恢复软件保障权益](https://azure.microsoft.com/en-in/pricing/details/site-recovery)的规定，每 90 天演练一次，每次演练的持续时间为 7 天。 可以修改特定 VM 的持续时间或将新值应用到所有 VM，只需在顶部行中输入新值，然后单击“应用到所有项”按钮即可。 可以根据一年的 DR 演练次数和每次 DR 演练的持续时间来计算总 DR 演练成本。
  
**OS 类型**：VM 的 OS 类型。 可以是 Windows 或 Linux。 如果 OS 类型为 Windows，则可将 Azure 混合使用权益应用到该 VM。 

**数据冗余**：可以是以下项之一 - 本地冗余存储 (LRS)、异地冗余存储 (GRS) 或读取访问异地冗余存储 (RA-GRS)。 默认值为 LRS。 可以根据特定 VM 的存储帐户来更改类型，也可以将新类型应用到所有 VM，只需更改顶部行的类型，然后单击“应用到所有项”按钮即可。  根据所选数据冗余的价格来计算复制的存储成本。 

**Azure 混合使用权益**：可以在适用情况下将 Azure 混合使用权益应用到 Windows VM。  默认值为“是”。 可以更改特定 VM 的设置，也可以通过单击“应用到所有项”按钮更新所有 VM。

**总 Azure 使用量**：包括用于 DR 的计算、存储和 Azure Site Recovery 许可证成本。 此项会根据你的选择显示每月或每年的成本。

**稳定状态复制成本**：包括复制的存储成本。

**总 DR 演练成本(平均)**：包括 DR 演练的计算和存储成本。

**ASR 许可证成本**：Azure Site Recovery 许可证成本。

## <a name="supported-target-regions"></a>支持的目标区域
Azure Site Recovery 部署规划器为以下 Azure 区域提供成本估算。 如果你的区域没有列在下面，可以使用下面定价最接近你的区域的任何区域。

eastus、eastus2、westus、centralus、northcentralus、southcentralus、northeurope、westeurope、eastasia、southeastasia、japaneast、japanwest、australiaeast、australiasoutheast、brazilsouth、southindia、centralindia、westindia、canadacentral、canadaeast、westus2、westcentralus、uksouth、ukwest、koreacentral、koreasouth 

## <a name="supported-currencies"></a>支持的货币
Azure Site Recovery 部署规划器可以使用以下任何货币生成成本报表。

|货币|名称||货币|名称||货币|名称|
|---|---|---|---|---|---|---|---|
|ARS|阿根廷比索 ($)||AUD|澳大利亚元 ($)||BRL|巴西雷亚尔 (R$)|
|CAD|加拿大元 ($)||CHF|瑞士法郎 (chf)||DKK|丹麦克朗 (kr)|
|EUR|欧元 (€)||GBP|英镑 (£)||HKD|港元 (HK$)|
|IDR|印度尼西亚卢比 (Rp)||INR|印度卢比 (₹)||JPY|日元 (¥)|
|KRW|韩元 (₩)||MXN|墨西哥比索 (MX$)||MYR|马来西亚林吉特 (RM$)|
|NOK|挪威克朗 (kr)||NZD|新西兰元 ($)||RUB|俄罗斯卢布 (руб)|
|SAR|沙特里亚尔 (SR)||SEK|瑞典克朗 (kr)||TWD|台币 (NT$)|
|TRY|土耳其里拉 (TL)||USD| 美元 ($)||ZAR|南非兰特 (R)|

## <a name="next-steps"></a>后续步骤
详细了解如何[使用 Azure Site Recovery 将 Hyper-V VM 转移到 Azure 进行保护](https://docs.microsoft.com/en-us/azure/site-recovery/tutorial-hyper-v-to-azure)。
