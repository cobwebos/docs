---
title: 删除 marketplace 产品/服务 |Azure Marketplace
description: 使用云合作伙伴门户删除 Azure 和 AppSource 市场中的套餐
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: pabutler
ms.openlocfilehash: 6a75fbb0c4b9c364342a406a8076128346943101
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826767"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>删除 Azure 市场和 AppSource 套餐或 SKU

出于各种原因，你可能想要从 Microsoft 市场中撤下套餐，此操作采用两种形式：

- “套餐删除”可确保新客户不再能够购买或部署该套餐，但对现有客户不起作用，因为仍然必须根据许可协议和相关法律为现有客户提供支持。 
- “套餐终止”是终止该服务和/或你与现有客户之间许可协议的过程。 

与产品/服务删除和终止相关的指南和策略受到[Microsoft 市场发行者协议](https://go.microsoft.com/fwlink/?LinkID=699560)和[参与策略](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)的管辖（本部分[提供挂起和删除](https://docs.microsoft.com/legal/marketplace/participation-policy#offering-suspension-and-removal)）。 

本文探讨支持的不同删除方案，以及执行每种方案所要采取的步骤。  

> [!NOTE]
> 只需在“编辑器”选项卡的工具栏中选择“删除”按钮，即可删除尚未发布的套餐。


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>从 Azure 市场中删除已发布的 SKU

可使用以下步骤从 Azure 市场中删除已发布的 SKU：

1.  登录[云合作伙伴平台](https://cloudpartner.azure.com/)。
2.  在“所有套餐”页中选择你的套餐。  该套餐应会显示在“编辑器”选项卡中。
3.  在左侧工具栏中选择“SKU”选项卡。 
4.  选择要删除的 SKU，然后单击“删除”按钮。
5.  将产品/服务[重新发布](./cpp-publish-offer.md)到 Azure 市场。

将修改的套餐发布到 Azure 市场后，选定的 SKU 不再显示在 Azure 市场和 Azure 门户中。


## <a name="roll-back-to-a-previous-sku-version"></a>回滚到之前的 SKU 版本

可使用以下步骤从 Azure 市场中删除当前版本的已发布 SKU。 完成该过程后，SKU 将回滚到其前一版本。

1. 登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。
2. 在“所有套餐”页中选择你的套餐。  该套餐应会显示在“编辑器”选项卡中。
3. 在左侧工具栏中选择“SKU”选项卡。 
4. 从磁盘版本列表中删除关联解决方案资产的最新版本。  根据套餐类型，此字段可能是“磁盘版本”、“包版本”或类似资产。 
5. 将产品/服务[重新发布](./cpp-publish-offer.md)到 Azure 市场。

将修改的套餐发布到 Azure 市场后，所列 SKU 的当前版本不再在 Azure 市场和 Azure 门户中列出。  SKU 将回滚到其前一版本。


## <a name="delete-a-live-offer"></a>删除实时产品/服务

删除已上线套餐需要考虑到过程、业务和法律的方方面面。 请遵循以下步骤从支持团队获取有关从 Azure 市场中删除已上线套餐的指导：

1.  使用[创建事件](https://go.microsoft.com/fwlink/?linkid=844975)页，或者单击**云合作伙伴门户**右上角的“支持”，来提出支持票证。[](https://cloudpartner.azure.com/)

2.  在“问题类型”列表中选择具体的套餐类型，然后在“类别”列表中选择“删除已发布的套餐”。

3.  提交请求。

支持团队将指导你完成套餐删除过程。

> [!NOTE]
> 删除套餐（或 SKU）不影响当前已购买该套餐（或 SKU）的客户。 这些客户可以像以前一样继续使用。 但是，以后不可以继续购买已删除的套餐或 SKU。


## <a name="next-steps"></a>后续步骤

熟悉用于管理套餐的基本操作后，可以创建 Microsoft [市场套餐](../cpp-marketplace-offers.md)的实例。
