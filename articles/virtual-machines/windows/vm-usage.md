---
title: 了解 Azure 虚拟机使用情况 | Microsoft Docs
description: 了解虚拟机使用情况详细信息
services: virtual-machines
documentationcenter: ''
author: mmccrory
manager: jeconnoc
editor: ''
tags: azure-virtual-machine
ms.assetid: ''
ms.service: ''
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/04/2017
ms.author: memccror
ms.openlocfilehash: 9980650008e2d2c2f768dcb3256ea5344119fa13
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
ms.locfileid: "31425174"
---
# <a name="understanding-azure-virtual-machine-usage"></a>了解 Azure 虚拟机使用情况
通过分析 Azure 使用情况数据，可以获得强有力的使用情况见解，根据这些见解，可以更好地在整个组织内进行成本管理和分配。 本文档深入介绍 Azure 计算使用情况详细信息。 有关 Azure 一般使用情况的更多详细信息，请导航到[了解你的帐单](../../billing/billing-understand-your-bill.md)。

## <a name="download-your-usage-details"></a>下载使用情况详细信息
首先，[下载使用情况详细信息](../../billing/billing-download-azure-invoice-daily-usage-date.md)。 下表提供了通过 Azure 资源管理器部署的虚拟机的定义和使用情况示例值。 本文档不包含通过经典模型部署的 VM 的详细信息。


| 字段             | 含义                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | 示例值                                                                                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 使用日期         | 使用资源时的日期。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |  “11/23/2017”                                                                                                                                                                                                                                                                                                                                                     |
| 测定仪 ID           | 标识此使用情况所属的最上层服务。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | “虚拟机”                                                                                                                                                                                                                                                                                                                                               |
| 测定仪子类别 | 计费测定仪标识符。 <ul><li>对于计算小时数使用情况，每个 VM 大小、OS（Windows、非 Windows）及区域都有一个测定仪。</li><li>对于高级软件使用情况，每个软件类型都有一个测定仪。 大多数高级软件映像针对每个核心大小都有不同的测定仪。 有关详细信息，请访问[计算定价页面](https://azure.microsoft.com/pricing/details/virtual-machines/)。</li></ul>                                                                                                                                                                                                                                                                                                                                         | “2005544f-659d-49c9-9094-8e0aea1be3a5”                                                                                                                                                                                                                                                                                                                           |
| 测定仪名称         | 这对于 Azure 中的每个服务都是特定的。 对于计算，则始终是“计算小时数”。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | “计算小时数”                                                                                                                                                                                                                                                                                                                                                  |
| 测定仪区域       | 指明某些服务的数据中心的位置，这些服务根据数据中心位置进行定价。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |  “日本东部”                                                                                                                                                                                                                                                                                                                                                       |
| 单位               | 指明服务的计价单位。 按小时对计算资源计费。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | “小时”                                                                                                                                                                                                                                                                                                                                                          |
| 已耗用           | 当日已耗用的资源量。 对于计算，在给定的某小时（精确到小数点后六位）内，我们会对 VM 运行的每一分钟计费。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |    “1”, “0.5”                                                                                                                                                                                                                                                                                                                                                    |
| 资源位置  | 指明资源正在其中运行的数据中心。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | “日本东部”                                                                                                                                                                                                                                                                                                                                                        |
| 已耗用的服务   | 使用的 Azure 平台服务。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | “Microsoft.Compute”                                                                                                                                                                                                                                                                                                                                              |
| 资源组     | 部署的资源正在其中运行的资源组。 有关详细信息，请参阅 [Azure 资源管理器概述](../../azure-resource-manager/resource-group-overview.md)。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |    “MyRG”                                                                                                                                                                                                                                                                                                                                                        |
| 实例 ID        | 资源的标识符。 此标识符包含你在资源创建时为其指定的名称。 对于 VM，实例 ID 包含 SubscriptionId、ResourceGroupName 和 VMName（或规模集使用情况的规模集名称）。                                                                                                                                                                                                                                                                                                                                                                                                                    | “/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1”<br><br>或<br><br>“/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1”                                                                                           |
| 标记               | 分配给资源的标记。 使用标记对计费记录进行分组。 了解如何[标记虚拟机](tag.md)。 这仅适用于资源管理器 VM。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | “{"myDepartment":"RD"，"myUser":"myName"}”                                                                                                                                                                                                                                                                                                                        |
| 其他信息    | 服务特定的元数据。 对于 VM，我们在其他信息字段中填充以下内容： <ul><li>映像类型 - 所运行的特定映像。 在“映像类型”下找到受支持字符串的完整列表。</li><li>服务类型：所部署的大小。</li><li>VMName：VM 的名称。 仅规模集 VM 才填充此字段。 如果需要规模集 VM 的 VM 名称，可在上面的实例 ID 字符串中找到。</li><li>UsageType：指定其所代表的使用情况类型。<ul><li>ComputeHR 是基础 VM（如 Standard_D1_v2）的计算小时数使用情况。</li><li>ComputeHR_SW 是 VM 使用高级软件（如 Microsoft R Server）产生的高级软件费用。</li></ul></li></ul>    | 虚拟机 {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR"}<br><br>虚拟机规模集 {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"myVM1", "UsageType":"ComputeHR"}<br><br>高级软件 {"ImageType":"","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR_SW"} |

## <a name="image-type"></a>映像类型
对于 Azure 库中的某些映像，会在其他信息字段中填充映像类型。 这可让用户了解和跟踪自己在虚拟机上部署的内容。 基于已部署映像在此字段填充的值如下所示：
  - BitRock 
  - Canonical 
  - FreeBSD 
  - Open Logic 
  - Oracle 
  - SLES for SAP 
  - SQL Server 14 Preview on Windows Server 2012 R2 Preview 
  - SUSE
  - SUSE Premium
  - StorSimple 云设备 
  - Red Hat
  - Red Hat for SAP Business Applications     
  - Red Hat for SAP HANA 
  - Windows Client BYOL 
  - Windows Server BYOL 
  - Windows Server Preview 

## <a name="service-type"></a>服务类型
其他信息字段中的服务类型字段对应所部署的 VM 的确切大小。 高级存储 VM（基于 SSD）和非高级存储 VM（基于 HDD）的定价相同。 如果部署基于 SSD 的大小（如 Standard\_DS2\_v2），则会在测定仪子类别列中看到非 SSD 大小（“Standard\_D2\_v2 VM”），在其他信息字段中看到 SSD 大小（“Standard\_DS2\_v2”）。

## <a name="region-names"></a>区域名称
使用情况详细信息“资源位置”字段中填充的区域名称与 Azure 资源管理器中使用的区域名称不同。 以下是区域值之间的映射：

|    **资源管理器区域名称**       |    **使用情况详细信息中的资源位置**    |
|--------------------------|------------------------------------------|
|    australiaeast         |    澳大利亚东部                               |
|    australiasoutheast    |    澳大利亚东南部                          |
|    brazilsouth           |    巴西南部                              |
|    CanadaCentral         |    加拿大中部                            |
|    CanadaEast            |    加拿大东部                               |
|    CentralIndia          |    印度中部                            |
|    centralus             |    美国中部                            |
|    chinaeast             |    中国东部                            |
|    chinanorth            |    中国北部                           |
|    eastasia              |    东亚                             |
|    eastus                |    美国东部                               |
|    eastus2               |    美国东部 2                             |
|    GermanyCentral        |    德国中部                            |
|    GermanyNortheast      |    德国东北部                          |
|    japaneast             |    日本东部                               |
|    japanwest             |    日本西部                               |
|    KoreaCentral          |    韩国中部                            |
|    KoreaSouth            |    韩国南部                              |
|    northcentralus        |    美国中北部                      |
|    northeurope           |    北欧                          |
|    southcentralus        |    美国中南部                      |
|    southeastasia         |    东南亚                        |
|    SouthIndia            |    印度南部                              |
|    UKNorth               |    英国北部                              |
|    uksouth               |    英国南部                              |
|    UKSouth2              |    英国南部 2                            |
|    ukwest                |    英国西部                               |
|    USDoDCentral          |    美国 DoD 中部                        |
|    USDoDEast             |    美国 DoD 东部                           |
|    USGovArizona          |    美国亚利桑那州政府                         |
|    usgoviowa             |    USGov Iowa                            |
|    USGovTexas            |    美国德克萨斯州政府                           |
|    usgovvirginia         |    USGov Virginia                        |
|    westcentralus         |    美国中西部                       |
|    westeurope            |    欧洲西部                           |
|    WestIndia             |    印度西部                               |
|    westus                |    美国西部                               |
|    westus2               |    美国西部 2                             |


## <a name="virtual-machine-usage-faq"></a>虚拟机使用情况常见问题解答
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>部署 VM 时，会对哪些资源收费？    
VM 本身、在 VM 上运行的任何高级软件、与虚拟机相关的存储帐户/托管磁盘，以及从 VM 传输的网络宽带都会产生成本。
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>如何辨别 VM 在使用情况 CSV 中是否使用 Azure 混合权益？
如果使用 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)进行部署，则会收取非 Windows VM 费率，因为你将自己的许可证带到了云中。 在帐单中，可以区分哪些资源管理器 VM 运行 Azure 混合权益，因为这些 VM 的 ImageType 列要么包含“Windows\_Server BYOL”，要么包含“Windows\_Client BYOL”。
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>如何在使用情况 CSV 中区分基本和标准 VM 类型？
基本和标准 A 系列 VM 都有提供。 如果部署基本 VM，那么“测定仪子类别”列中将包含字符串“Basic”。 如果部署标准 A 系列 VM，则该 VM 的大小会显示为“A1 VM”，这是因为默认为标准 VM。 若要详细了解基本和标准 VM 的区别，请参阅[定价页](https://azure.microsoft.com/pricing/details/virtual-machines/)。
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>什么是“特小型”、“小型”、“中型”、“大型”和“特大型”？
特小型 - 特大型是 Standard\_A0 – Standard\_A4 的原有名字。 在经典 VM 使用情况记录中，如果已部署这些大小，则可能会看到使用此约定。
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>测定仪区域和资源位置的区别是什么？
测定仪区域与测定仪相关。 对于所有区域使用一种价格的部分 Azure 服务，测定仪区域字段可能为空白。 但是，由于 VM 针对每个区域有专用的虚拟机价格，所以此字段填充了内容。 同样，虚拟机的资源位置即是部署 VM 的位置。 两个字段中的 Azure 区域都是相同的，尽管它们可能对区域名称具有不同的字符串约定。
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>为什么其他信息字段中的 ImageType 值为空白？
仅对映像的子集填充 ImageType 字段。 如果未部署上述映像之一，ImageType 则为空白。
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>为什么其他信息字段中的 VMName 为空白？
仅规模集 VM 的其他信息字段中才会填充 VMName。 InstanceID 字段包含非规模集 VM 的 VM 名称。
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>ComputeHR 在其他信息的 UsageType 字段中意味着什么？
ComputeHR 代表计算小时数，表示基本基础架构成本的使用情况事件。 如果 UsageType 为 ComputeHR\_SW，则使用情况事件表示 VM 的高级软件费用。
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>如何知道高级软件是否计费？
请务必访问 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute)，寻找最符合自己需求的 VM 映像。 映像具有软件计划费率。 如果看到费率为“免费”，该软件则不会额外收费。 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>在使用的服务中，Microsoft.ClassicCompute 和 Microsoft.Compute 有何区别？
Microsoft.ClassicCompute 表示通过 Azure 服务管理器部署的经典资源。 如果通过资源管理器进行部署，则会在使用的服务中填充 Microsoft.Compute。 详细了解 [Azure 部署模型](../../azure-resource-manager/resource-manager-deployment-model.md)。
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>为什么虚拟机使用情况的 InstanceID 字段为空白？
如果通过经典部署模型进行部署，则 InstanceID 字符串不可用。
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>为什么 VM 的标记没有流向使用情况详细信息？
标记仅流向资源管理器 VM 的使用情况 CSV。 经典资源标记在使用情况详细信息中不可用。
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>一天的使用量如何超过 24 小时？
在经典模型中，资源的计费在云服务级别累积。 如果云服务中有多个 VM 使用同一个计费测定仪，使用情况将会累积到一起。 通过资源管理器部署的 VM 在 VM 级别进行计费，因此不会应用累积。
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>为什么定价页上未提供 DS/FS/GS/LS 大小的定价？
支持高级存储的 VM 与不支持高级存储的 VM 采用相同的费率计费。 仅存储成本不同。 请参阅[存储定价页](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/)了解详细信息。

## <a name="next-steps"></a>后续步骤
若要详细了解使用情况详细信息，请参阅[了解 Microsoft Azure 帐单](../../billing/billing-understand-your-bill.md)

