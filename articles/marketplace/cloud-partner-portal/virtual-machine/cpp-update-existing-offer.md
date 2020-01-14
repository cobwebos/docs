---
title: 在 Azure Marketplace 中更新现有 VM 产品/服务
description: 介绍如何更新 Azure 市场中的现有 VM 套餐。
services: Azure, Marketplace, Cloud Partner Portal,
author: MaggiePucciEvans
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/27/2018
ms.author: evansma
ms.openlocfilehash: 1ba2abb3fbeb1d08ed780669fb94a2ef83cbfb1b
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934248"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>更新 Azure 市场中的现有 VM 套餐

本文从不同的方面指导你在[云合作伙伴门户](https://cloudpartner.azure.com/)中更新虚拟机 (VM) 套餐，然后重新发布该套餐。 

你可能出于多种原因更新套餐，常见的原因包括：

-  将新的 VM 映像版本添加到现有 SKU
-  更改 SKU 的可用区域
-  添加新 SKU
-  更新套餐或单个 SKU 的市场元数据
-  更新即用即付套餐的定价

为了帮助你进行这些修改，门户中提供了“比较”和“历史记录”功能。  

>[!Note]
>现在提供了云解决方案提供商（CSP）合作伙伴渠道选择。  请参阅[云解决方案提供商](../../cloud-solution-providers.md)，了解有关通过 Microsoft CSP 合作伙伴渠道营销产品/服务的详细信息。

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>不允许对 VM 套餐或 SKU 进行的更改

套餐在 Azure 市场中上线后，无法修改 VM 套餐或 SKU 的某些属性，主要包括：

-  套餐的“套餐 ID”和“发布者 ID”
-  现有 SKU 的“SKU ID”
-  现有 SKU 的数据磁盘计数
-  对现有 SKU 的计费/许可证模型更改
-  提高已发布 SKU 的价格


## <a name="common-update-operations"></a>常见更新操作

可对 VM 套餐的各种特征进行更改，下面是一些常见的操作。

### <a name="update-the-vm-image-version-for-a-sku"></a>更新 SKU 的 VM 映像版本

我们经常会使用安全修补程序、其他功能等定期更新 VM 映像。  在这种情况下，可能需要使用以下步骤来更新 SKU 引用的 VM 映像：

1.  登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。

2.  在“所有套餐”下，找到要更新的套餐。

3.  在“SKU”选项卡中，单击与要更新的 VM 映像关联的 SKU。

4.  在“磁盘版本”下，单击“+新建磁盘版本”以添加新的 VM 映像。

5.  提供新的 VM 映像**磁盘版本**。 此磁盘版本需遵循[语义版本](https://semver.org/)格式。 版本格式应为 X.Y.Z，X、Y 和 Z 是整数。 确认提供的新版本高于以往的所有版本；否则，重新发布后，新版本将不会在门户或 Azure 市场中显示。

6.  对于“OS VHD URL”，请输入为操作系统 VHD 创建的[共享访问签名 (SAS) URI](./cpp-get-sas-uri.md)。 

    > [!WARNING] 
    > 无法在不同的 SKU 版本之间更改数据磁盘计数。 如果先前版本配置了数据磁盘，则此新版本也必须有相同数量的数据磁盘。

7.  单击“发布”，启动将新 VM 版本发布到 Azure 市场的工作流。


### <a name="change-region-availability-of-a-sku"></a>更改 SKU 的可用区域

以后可能需要在更多区域提供产品/SKU。  或者，可能需要在特定区域停止支持产品/SKU。
若要修改可用性，请使用以下步骤：

1.  登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。

2.  在“所有套餐”下，找到要更新的套餐。

3.  在“SKU”选项卡中，单击要修改其可用性的 SKU。

4.  单击“国家/地区可用性”字段下的“选择国家/地区”按钮。

5.  在可用区域弹出窗口中，添加或删除此 SKU 的可用区域。

6.  单击“发布”，启动更新 SKU 可用区域的发布工作流。

如果要在新区域中提供 SKU，可通过“导出定价数据”功能对此特定区域指定定价。 如果加回以前的可用区域，则无法更新其定价，因为不允许更改定价。


### <a name="add-a-new-sku"></a>添加新 SKU

使用以下步骤使新 SKU 可用于现有套餐： 

1.  登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。

2.  在“所有套餐”下，找到要更新的套餐。

3.  在“SKU”选项卡下，单击“添加新 SKU”并在弹出窗口中提供一个 **SKU ID**。

4.  根据[将虚拟机发布到 Azure 市场](./cpp-publish-offer.md)中的详述重新发布 VM。

5.  单击“发布”，启动发布新 SKU 的工作流。


### <a name="update-offer-marketplace-metadata"></a>更新套餐市场元数据

使用以下步骤更新与套餐关联的市场元数据 — 公司名称、徽标等： 

1.  登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。

2.  在“所有套餐”下，找到要更新的套餐。

3.  转到“市场”选项卡，然后遵照[将虚拟机发布到 Azure 市场](./cpp-publish-offer.md)一文中的说明更改元数据。

4.  单击“发布”，启动发布更改的工作流。


### <a name="update-pricing-on-published-offers"></a>更新已发布套餐的定价

发布即用即付套餐后，无法直接提高 SKU 的定价。  （但是，你可以在同一个产品/服务下创建新的 SKU，删除旧的 SKU，然后为新客户重新发布产品/服务。） 与此相反，你可以使用以下步骤降低已发布产品/服务的价格：

1.  登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。

2.  在“所有套餐”下，找到要更新的套餐。

3.  单击要降低其定价的 SKU。

4.  如果在 1x1 GUI 中设置了定价，可以直接在该 UI 中更改价格。 如果通过导入/导出电子表格设置了定价，则只能通过导入/导出功能降低价格。

3.  单击“ **保存**”。

4.  单击“发布”，启动发布更改的工作流。

套餐在网站中上线后，新客户会看到已降低的新价格。  此新价格会在以下方面影响客户：

- 将按此新费率对新客户计费。 
- 在降价生效期间，计费周期开始时，将以回溯的方式向现有客户反映降价情况。
如果在降价时所处的周期内已对他们计费，则在下一个计费周期，他们会收到一笔退款，以冲抵降价。


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>简化的货币定价

从 2018 年 9 月 1 日开始，门户中将会添加一个名为“简化的货币定价”的新部分。 Microsoft 正在针对全球客户实现更多的可预测定价和托收方案，以简化 Azure 市场业务。 这种简化涉及到减少客户发票所采用的货币数量。

新部分将以这些新货币定价。 所有客户都迁移到这些新的结算币种后，原始定价部分将停用，并且只有简化的货币定价部分会保留。

必须在 2018 年 11 月 1 之前，为要改变结算货币的区域设置新价格。 无法为结算货币不会改变的区域提高价格。

> [!NOTE] 
> 如果使用 API 发布套餐，可在“套餐 JSON”中看到一个新部分。 根据套餐的类型，此部分带有 `virtualMachinePricingV2` 或 `monthlyPricingV2` 批注。 

如果对此项更改有任何疑问，请联系 [Azure 市场支持人员](../../support-azure-marketplace.md)。


## <a name="compare-feature"></a>“比较”功能

对已发布的套餐进行更改时，可以利用“比较”功能来审核所做的更改。 若要使用此功能：

1.  在编辑过程中的任意时间点，单击套餐旁边的“比较”按钮。

    ![“比较”功能按钮](./media/publishvm_037.png)


2.  并列查看营销资产和元数据的版本。


## <a name="history-of-publishing-actions"></a>发布操作的历史记录

若要查看任何历史发布活动，请单击云合作伙伴门户左侧导航菜单栏中的“历史记录”项。 在此处可以查看 Azure 市场套餐生存期内执行的带时间戳的操作。  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

