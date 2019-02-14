---
title: 更新现有 Power BI 应用套餐 - Azure 市场 | Microsoft Docs
description: 在 Microsoft AppSource 市场上发布 Power BI 应用套餐后对其进行更新。
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
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
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 635e2e71bb952aaee761df6a1d5d87c46db531f6
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665698"
---
# <a name="update-an-existing-power-bi-app-offer"></a>更新现有 Power BI 应用套餐

本文从不同的方面指导你在[云合作伙伴门户](https://cloudpartner.azure.com/)中更新 Power BI 应用套餐，然后重新发布该套餐。  你可能出于多种原因更新套餐，常见的原因包括：

- 更新 Power BI 中应用的内容，并从新打包的应用中获取新的安装 URL
- 更新套餐的市场元数据：销售、市场营销或支持信息和资产
 
为了帮助你进行这些修改，门户中提供了“比较”和“历史记录”功能。


## <a name="unpermitted-changes-to-offer"></a>不允许对套餐进行的更改

Power BI 应用套餐在 AppSource 中上线后，无法修改该套餐的某些属性，主要包括“套餐 ID”和“发布者 ID”。


## <a name="common-update-operations"></a>常见更新操作

可对 Power BI 应用套餐的各种特征进行更改，下面是一些常见的操作。


### <a name="update-app-content-in-power-bi"></a>更新 Power BI 中的应用内容

我们经常会使用新内容、安全修补程序、其他功能等定期更新 Power BI 中的应用。 在这种情况下，需要按照以下步骤来更新新应用内容安装的 URL：

1.  登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。
2.  在“所有套餐”下，找到要更新的套餐。
3.  在“技术信息”选项卡中，输入新的安装程序 URL。
4.  单击“发布”，启动将新应用的版本发布到 AppSource 的工作流。


### <a name="update-offer-marketplace-metadata"></a>更新套餐市场元数据

使用以下步骤更新与套餐关联的市场元数据 — 公司名称、徽标等：

1.  登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。
2.  在“所有套餐”下，找到要更新的套餐。
3.  转到“店面详细信息”选项卡，然后按照 [Power BI 应用“店面详细信息”选项卡](./cpp-storefront-details-tab.md)中的说明进行元数据更改。
4.  单击“发布”，启动发布更改的工作流。


## <a name="compare-feature"></a>“比较”功能

对已发布的套餐进行更改时，可以使用“比较”功能来审核所做的更改。 若要使用此功能：

1.  在编辑过程中的任意时间点，单击套餐旁边的“比较”按钮。

    ![“比较”功能按钮](./media/compare-feature-button.png)

2.  并列查看营销资产和元数据的版本。


## <a name="history-of-publishing-actions"></a>发布操作的历史记录

若要查看任何历史发布活动，请单击云合作伙伴门户左侧导航菜单栏中的“历史记录”选项卡。 在此处可以查看 AppSource 套餐生存期内执行的带时间戳的操作。


## <a name="next-steps"></a>后续步骤

应定期使用[云合作伙伴门户](https://cloudpartner.azure.com/#insights)的 [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) 功能来提供有关市场客户和使用情况的见解。  
