---
title: Seller Insights 发行说明 | Microsoft Docs
description: 提供有关 Seller Insights 功能更改的信息。
services: Azure, Marketplace, Cloud Partner Portal,
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
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ec3784a91f8aeb7f0fedd13c9ab86a844832a578
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805226"
---
<a name="seller-insights-release-notes"></a>Seller Insights 发行说明 
===============================
（发布日期：2018 年 7 月 28 日）

本文提供有关[云合作伙伴门户](https://cloudpartner.azure.com/#insights)中 Seller Insights 功能更改的信息。


<a name="release-highlights"></a>版本特点
------------------

-   估计价格提供考虑到货币转换影响的客户费用视图。
-   预测的付款金额提供可能付款金额的早期视图。
-  使用情况参考标识符提供客户使用情况与付款订单之间的数据保真度
-   按每日粒度算的使用情况可提供更精细的粒度，更好地洞察客户使用情况。


<a name="changes-to-data-structure-and-taxonomy"></a>数据结构和分类的更改
--------------------------------------

下表列出了此版本已添加或已进行实质性更改的指标。 

| 新建术语                   |    **定义**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| 价格 (CC)                     | 给定 SKU 的一个使用单位的价格（采用客户的货币）。       |
| 估计的延伸费用 (CC) | 针对给定 SKU 的使用单位数量估计的延伸费用（按客户货币计算）。 由于舍入或截断错误，此值可能不精确。   |
| 发布者货币 (PC)        | 发布者首选的付款货币。                               |
| 估计价格 (PC)           | 根据用量计算日期的外币兑换率，针对给定 SKU 的一个使用单位估计的价格（按发布者的货币计算）。 由于舍入或截断错误，此值可能不精确。   |
| 估计的延伸费用 (PC) | 根据用量计算日期的外币兑换率，针对给定 SKU 的使用单位数量估计的延伸费用（按发布者的货币计算）。 由于舍入或截断错误，此值可能不精确。 |
| 估计的付款金额 (PC)          | 根据用量计算日期的外币兑换率，针对给定 SKU 的使用单位数量估计的付款金额（按发布者的货币计算）。 由于舍入或截断错误，此值可能不精确。   |
| 使用情况参考                | 针对与付款报表中某个条目关联的给定 SKU，客户使用的一个或多个日期的标识符。 |
|  |  |
