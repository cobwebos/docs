---
title: 从 Azure 市场删除产品/服务/SKU
description: 从 Azure 市场删除产品/服务/SKU
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 02b4ad8aea3904ba0e99a5ea4d4d035f970e171a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805812"
---
<a name="delete-an-offersku-from-azure-marketplace"></a>从 Azure 市场删除产品/服务/SKU 
==========================================

可能由于各种原因决定从市场中删除产品/服务。 新客户可能不再购买或部署你的产品/服务，但现有客户不受影响。
产品/服务终止是终止该服务和/或你与现有客户之间许可协议的过程。 与产品/服务删除和终止相关的指南和政策受 [Microsoft 市场发布者协议](http://go.microsoft.com/fwlink/?LinkID=699560)（请参阅第 7 节）
7) 和[参与策略](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)（请参阅第 6.2 节）的约束。 本文探讨了多种受支持的删除方案以及可以采取的步骤。

<a name="delete-a-live-sku-from-azure-marketplace"></a>从 Azure 市场删除实时 SKU 
----------------------------------------

按照以下步骤操作，可从 Azure 市场中删除实时 SKU：

1.  登录[云合作伙伴平台](http://cloudpartner.azure.com)。
2.  从“所有产品/服务”选项卡中选择产品/服务。
3.  在屏幕左侧窗格中，选择“SKU”选项卡。
4.  选择要删除的 SKU，并单击该 SKU 对应的“删除”按钮。
5.  将产品/服务重新发布到 Azure 市场。

产品/服务实时显示在 Azure 市场中后，SKU 将从 Azure 市场和 Azure 门户中删除。

<a name="roll-back-to-a-previous-sku-version"></a>回滚到之前的 SKU 版本 
----------------------------------

按照如下步骤操作，可从 Azure 市场中删除实时 SKU 的当前版本。 完成该过程后，SKU 将回滚到其之前的版本。

1.  登录[云合作伙伴平台](http://cloudpartner.azure.com)。
2.  从“所有产品/服务”选项卡中选择产品/服务。
3.  在屏幕左侧窗格中，选择“SKU”选项卡。
4.  从已发布程序包的列表中删除最新的程序包版本。
5.  将产品/服务重新发布到 Azure 市场。

产品/服务实时显示在 Azure 市场中后，已列出的 SKU 的当前版本将从 Azure 市场和 Azure 门户中删除。
SKU 将回滚到其以前的版本。

<a name="delete-a-live-offer"></a>删除实时产品/服务 
-------------------

请按照以下步骤从支持团队获取有关从 Azure 市场中删除实时产品/服务的指南：

1.  使用此[链接](https://go.microsoft.com/fwlink/?linkid=844975)生成支持票证
2.  在“问题类型”列表中选择“管理产品/服务”，并在“类别”列表中选择“修改已在生产中的产品/服务和/或 SKU”。
3.  提交请求。

支持团队指导完成产品/服务删除过程。

SKU/产品/服务的删除不会影响当前已购买的 SKU/产品/服务。 它们会像以前一样继续工作，但不会用于任何新的购买。
