---
title: Azure 市场的云合作伙伴门户中的虚拟机“SKU”选项卡 | Microsoft Docs
description: 介绍用于在 Azure 市场中创建虚拟机套餐的“SKU”选项卡。
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 4ecc259d40cdcba93a484f27e27191e967f10ff1
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638869"
---
# <a name="virtual-machine-skus-tab"></a>虚拟机“SKU”选项卡

在“新建套餐”页的“SKU”选项卡中，可以创建一个或多个 SKU 并将其关联到新套餐。  不同的 SKU 可按功能集、VM 映像类型、吞吐量或可伸缩性、计费模型或其他某种特征来区分解决方案。

## <a name="create-a-sku"></a>创建 SKU

最初，新套餐没有任何关联的 SKU，因此，需要单击“新建 SKU”来创建一个 SKU。

![虚拟机的“新建套餐”选项卡上的“新建 SKU”按钮](./media/publishvm_005.png)

<br/>

此时会显示“新建 SKU”对话框。  输入新 SKU 的标识符，然后单击“确定”。 （参阅下面的标识符命名约定。）“SKU”选项卡现在会显示可编辑的字段。    名称旁边附有星号 (*) 的字段表示必填字段。

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![虚拟机的“新建套餐”窗体中的“SKU”选项卡](./media/publishvm_006.png)

下表描述了这些字段的用途、内容和格式。

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **字段**       |     **说明**                                                          |
|  ---------       |     ---------------                                                          |
|  *SKU 设置*   |  |
| **SKU ID**       | 此 SKU 的标识符。  此名称最多包含 50 个字符，包括小写字母数字字符或短划线 (-)，但不能以短划线结尾。  套餐一经发布，便不可修改。  |
|  *SKU 详细信息*   |  |
| **标题**        | 套餐在市场中的友好显示名称。 最大长度为 50 个字符。 |
| **摘要**      | 套餐在市场中显示的简短说明。 最大长度为 100 个字符。 |
| **说明**  | 更详细地介绍套餐的说明文本。  <!-- TD: max len/guidance? 3k characters -->  |
| **隐藏此 SKU** | 指示是否应在市场中向客户显示该 SKU。  如果只想通过解决方案模板提供此 SKU，而不允许单独购买，则可以隐藏此 SKU。  对于初始测试、临时性或季节性的套餐，此选项也可以提供方便。 |
| **云可用性** | 确定要在哪个云上提供该 SKU。  默认值为 Azure 公有云。  Microsoft Azure 政府版是一个政府社区云，允许美国联邦、州、地方或部落政府及其认证的合作伙伴进行受控的访问。  有关政府云的详细信息，请参阅[欢迎使用 Azure 政府版](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)。 |
| **是否为专用 SKU?** | 指示该 SKU 是专用还是公共 SKU。 默认值为“否”（即公共 SKU）。  有关详细信息，请参阅[公共和专用 SKU](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)。 |
| **国家/区域可用性** | 确定可在哪些国家或地区购买你的 SKU。 至少选择一个国家/地区。 <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *定价*   |  |
| **许可模型**| 要使用的标准化计费模型。  如果选择“基于用量的每月计费 SKU”，则会打开一个可折叠部分，可在其中指定每个核心的定价详细信息，以及是否要提供免费试用。  在此部分，还可将此定价计划导出和导入到 Excel。 有关详细信息，请参阅 [Azure 市场中的计费选项](../../billing-options-azure-marketplace.md)。 | 
|  *VM 映像*   |  |
| **操作系统系列** | 指示解决方案 VM 是基于 Windows 还是 Linux。 |
| **选择操作系统类型** | 指定的 OS 的特定供应商或版本。 |
| **OS 友好名称** | 向客户显示的操作系统名称。  |
| **建议的 VM 大小** | 从标准化列表中最多选择六个建议的 VM 大小。  尽管向潜在客户突出显示了这些建议的选项，但他们可以指定任何与解决方案映像兼容的 VM 大小。 | 
| **打开端口**| 要打开的端口，以及用于支持 SKU 的协议。  这些配置必须与针对解决方案 VM 网络配置的虚拟网络相匹配。 在 VM 部署期间，这些设置将会生效。 但是，发布 SKU 后，可以修改端口设置。 有关详细信息，请参阅[如何使用 Azure 门户向虚拟机开放端口](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal)。 <br/>以下默认网络映射将添加到所有 VM。 &emsp; Windows：3389 -> 3389 TCP，5986 -> 5986 TCP；&emsp; Linux：22 -> 22，TCP (SSH)。 |
| **磁盘版本**  | 关联的解决方案 VM，按磁盘版本号和磁盘 URL 指定。 磁盘版本必须采用[语义版本](http://semver.org/)格式：`<major>.<minor>.<patch>`。  URL 是为操作系统 VHD 创建的共享访问签名 URI。  尽管最多可为每个 SKU 添加八个磁盘版本，但 Azure 市场中只会显示 SKU 的最高磁盘版本号。 其他版本只能通过 API 显示。  <!--TD: Add more specific link to API --> <br/> 在“新建数据磁盘”可折叠部分，最多可将 15 个数据磁盘附加到 VM。  使用给定的 VM 版本和关联的数据磁盘发布 SKU 后，无法修改此配置。  如果将其他 VM 版本添加到了 SKU，这些版本也必须支持相同数量的数据磁盘。 <br/> 如果尚未创建基于 Azure 的 VM 映像，则以后可以更新此字段。  有关创建关联 VM 资源的信息，请参阅[创建 VM 技术资产](./cpp-create-technical-assets.md)。  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> 单击“保存”以保存进度。 在下一个选项卡中，指定套餐是否支持[体验版](./cpp-test-drive-tab.md)。


## <a name="additional-pricing-considerations"></a>其他定价注意事项

上面对定价模型做了一个基本的说明。  我们正在对此模型进行更改，它的实施可能受当地或区域税法和 Microsoft 定价政策的影响。 

### <a name="simplified-currency-pricing"></a>简化的货币定价

从 2018 年 9 月 1 日开始，门户中将会添加一个名为“简化的货币定价”的新部分。 Microsoft 正在针对全球客户实现更多的可预测定价和托收方案，以简化 Azure 市场业务。 这种简化涉及到减少客户发票所采用的货币数量。  有关详细信息，请参阅[在 Azure 市场中更新现有的 VM 套餐](./cpp-update-existing-offer.md)。


### <a name="additional-information-on-taxes-and-prices"></a>有关税务和价格的其他信息

* Microsoft 将某些国家/地区分类为减免税国家/地区。  在此类国家/地区，Microsoft 会向客户托收税款并向政府支付（汇寄）税款。  在其他国家/地区，通常由合作伙伴负责向其客户托收税款并向政府支付税款。 如果选择在后一类国家/地区销售，必须具备计算和支付当地税款的能力。  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* 价格在推出产品/服务后无法更改。 但是，仍可以添加或删除支持的区域。 
* Microsoft 向客户收取标准的 Azure VM 使用费，以及计划的 SKU 费。
* 根据设置价格时的实时汇率为所有区域设置价格（使用本地货币）。  <!-- TD: Meaning? - Offer created, published, other? -->
* 若要单独设置每个区域的价格，请导出定价电子表格，应用自定义定价，然后导入。 

<!-- TD: The detailed information in the table and supplemental notes should be centralized in another topic, maybe "Billing Options" in AMP tree. Need to include a common section on export/import pricing-->

