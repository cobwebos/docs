---
title: "从 Azure 应用商店删除产品/服务 | Microsoft Docs"
description: "本文提供有关从 Azure 应用商店删除产品/服务的详细信息"
services: cloud-partner-portal
documentationcenter: 
author: anuragdalmia
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2017
ms.author: andalmia
translationtype: Human Translation
ms.sourcegitcommit: 233d62b176303ca384aae9453c2e125c0c70fad2
ms.openlocfilehash: f09be39b75bd0f39094a2ec08609c0c7ff03bc8e
ms.lasthandoff: 04/12/2017


---
# <a name="delete-an-offersku-from-azure-marketplace"></a>从 Azure 应用商店删除产品/服务/SKU

可能由于各种原因决定从应用商店中删除产品/服务。  产品/服务删除可确保新客户无法再购买或部署该产品/服务，但对现有客户不起作用。 产品/服务终止是终止该服务和/或你与你的现有客户之间许可协议的过程。  有关产品/服务删除和终止的指南和相关策略均由 [Microsoft Marketplace Publisher Agreement](http://go.microsoft.com/fwlink/?LinkID=699560)（Microsoft 应用商店发布者协议）（请参阅第 7 节）和[参与策略](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)（请参阅 6.2 节）。   本文探讨不同的删除方案和可为这些方案执行的步骤。

## <a name="delete-a-live-sku-from-azure-marketplace"></a>从 Azure 应用商店删除实时 SKU

按照以下步骤操作，可从 Azure 应用商店中删除实时 SKU：

1.  登录[云合作伙伴平台](https://cloudpartner.azure.com/)。

2.  从“所有产品/服务”选项卡选择你的产品/服务。

3.  在屏幕左侧窗格中，选择“SKU”选项卡。

4.  选择要删除的 SKU，然后针对该 SKU 单击“删除”按钮。

5.  将产品/服务[重新发布](./Cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md)到 Azure 应用商店。

产品/服务实时显示在 Azure 应用商店中后，SKU 将从 Azure 应用商店和 Azure 门户中删除。

## <a name="rollback-to-a-previous-sku-version"></a>回滚到之前的 SKU 版本

按照如下步骤操作，可从 Azure 应用商店中删除实时 SKU 的当前版本。 完成该过程后，SKU 将回滚到其之前的版本。

1.  登录[云合作伙伴平台](https://cloudpartner.azure.com/)。

2.  从“所有产品/服务”选项卡选择你的产品/服务。

3.  在屏幕左侧窗格中，选择“SKU”选项卡。

4.  从已发布磁盘版本的列表中删除最新的**磁盘版本**。

5.  将产品/服务[重新发布](./Cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md)到 Azure 应用商店。

产品/服务实时显示在 Azure 应用商店中后，已列出的 SKU 的当前版本将从 Azure 应用商店和 Azure 门户中删除。 SKU 将回滚到其以前的版本。

## <a name="delete-a-live-offer"></a>删除实时产品/服务

请求删除实时产品/服务时，需要注意以下方面。 请按照以下步骤从支持团队获取有关从 Azure 应用商店中删除实时产品/服务的指南：

1.  使用此[链接](https://go.microsoft.com/fwlink/?linkid=844975)生成支持票证

2.  在“问题类型”列表中选择“管理产品/服务”，并在“类别”列表中选择“修改已在生产中的产品/服务和/或 SKU”。

3.  提交请求

支持团队将指导你完成产品/服务删除过程。

请注意，SKU/产品/服务的删除不会影响当前已购买的 SKU/产品/服务。 这些将继续按以前的方式工作。 未来进行任何新的购买后，它们将不可用。  
