---
title: 更新市场套餐 - Azure 市场 | Microsoft Docs
description: 使用云合作伙伴门户更新 Azure 和 AppSource 市场中的套餐
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pbutlerm
ms.openlocfilehash: 76b607502324c3ca25b3536d5197a97dbb80399d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102139"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>更新 Azure 市场和 AppSource 套餐

发布套餐后，可对其应用多种类型的更新。  [云合作伙伴门户](https://cloudpartner.azure.com/)可帮助你适当修改套餐的属性，包括：

-  将新虚拟机 (VM) 映像或包版本添加到现有 SKU
-  更改 SKU 的可用区域
-  添加新的 SKU
-  更新套餐或 SKU 的市场元数据 
-  更新即用即付套餐的定价

该门户还提供可帮助你管理更改的功能，例如，比较套餐的功能，以及查看套餐功能的历史记录。  修改套餐或 SKU 后，必须先将其重新发布，然后更改才会生效。  本文将提供市场套餐更新方方面面的分步指导。

## <a name="unpermitted-changes-to-an-offersku"></a>对套餐/SKU 进行未经许可的更改

套餐或 SKU 在市场中发布后，无法修改其某些属性。  相应字段会在门户的“编辑器”选项卡中禁用，例如：  

- 套餐 ID 和发布者 ID
- SKU ID 
- 现有 SKU 的数据磁盘计数
- 对现有 SKU 的计费/许可证模型更改
- 版本标记，例如 `1.0.1`


## <a name="common-update-operations"></a>常见更新操作

以下部分介绍如何执行一些最常见的更新操作。  并非所有套餐类型都适用这些操作。  必须登录到云合作伙伴门户才能启动其中的任何操作。


### <a name="update-offer-contacts"></a>更新产品/服务联系人

使用以下步骤更新产品/服务的支持联系人。
1. 在“所有套餐”页中选择套餐。
2. 选择“联系人”选项卡。更新你的联系人。
3. 选择“保存”按钮。
4. 选择“发布”启动发布过程。


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>更改套餐或 SKU 的适用区域

以后可能需要在更多区域提供产品/SKU。
或者，可能需要在特定区域停止支持产品/SKU。
若要实施这些更改，请遵循以下步骤。

1. 在“所有套餐”页中，找到要更新的套餐。

对于 Azure 市场套餐：

1. 选择“SKU”选项卡。选择要修改的 SKU。
1. 单击“适用国家/地区”字段下的“选择国家/地区”按钮。
1. 在“适用区域”对话框中，添加或删除此 SKU 的适用区域。

对于 AppSource 套餐：

1. 选择“店面详细信息”选项卡。
1. 在“支持的国家/地区”标签旁边，单击“支持的国家/地区”。 
1. 在“支持的国家/地区”对话框中，添加或删除此套餐的适用区域。

对于任一市场：

1. 单击“发布”启动发布过程。 

如果要在新区域中提供 SKU，可通过“导出定价数据”功能对此特定区域指定定价。 如果加回以前适用的区域，则无法更新其定价，因为不允许更改定价。


### <a name="add-a-new-sku"></a>添加新 SKU 

若要使新 SKU 可用于现有套餐，请使用以下步骤：

1. 在“所有套餐”页中找到该套餐。
3. 在“SKU”窗体下，单击“添加新 SKU”并在弹出窗口中提供一个 **SKU ID**。
4. 遵循[发布虚拟机套餐](../virtual-machine/cpp-publish-offer.md)中详述的剩余步骤。
5. 单击“发布”启动发布过程。


### <a name="update-offer-marketplace-assets"></a>更新套餐市场资产

在某些情况下，你可能需要更新市场中基于文本的资产和图像资产，例如公司徽标、套餐说明，等等。请使用以下步骤更新这些资产。

1. 在“所有套餐”页中找到你的套餐。 
2. 选择“市场”选项卡，然后遵照套餐的“市场”选项卡主题中的说明操作。
3. 单击“发布”启动发布过程。


### <a name="update-pricing-on-published-offers"></a>更新已发布套餐的定价

发布即用即付套餐后，无法提高现有 SKU 的价格。  应在同一套餐下创建一个 SKU、删除旧 SKU，然后重新发布套餐。 可以降低以前发布的套餐的价格。 降低套餐价格：

1. 选择要降低其定价的 SKU。
2. 必须通过最初所用的同一机制来降低价格：直接在门户 UI 设置价格，或者使用导入/导出电子表格设置价格。
3. 单击“ **保存**”。
4. 单击“发布”启动发布过程。

套餐在市场中上线后，新客户会看到该定价，而所有新客户将按已降低的新价格付费。  在降价生效期间，计费周期开始时，将以回溯的方式向现有客户反映降价情况。  如果在降价时所处的周期内已对他们计费，则在下一个计费周期，他们会收到一笔退款，以冲抵降价。


## <a name="compare-feature"></a>“比较”功能

对发布的套餐进行更改时，可以使用“比较”功能来审核更改。 若要利用此功能：

1. 在编辑过程中的任意时间点，可以单击套餐“编辑器”选项卡中的“比较”按钮。
2. 比较窗口会并排显示此套餐的已保存更改版本与市场套餐版本的比较。 

![编辑器选项卡中的比较套餐按钮](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>发布操作的历史记录

若要查看历史发布活动，请选择云合作伙伴门户左侧纵向菜单栏中的“历史记录”选项卡。  在“历史记录”页中，可按多个特征灵活筛选，并且可将列排序。  每个发布事件带有时间戳。  有关详细信息，请参阅[审核历史记录页](../portal-tour/cpp-history-page.md)。


## <a name="next-steps"></a>后续步骤

还可以使用云合作伙伴门户[删除已发布的 SKU 或套餐](./cpp-delete-offer.md)。
