---
title: 卖家洞察发行说明
description: 提供有关 Seller Insights 功能更改的信息。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: dsindona
ms.openlocfilehash: 1b88a8353c1cb9f97f18d720171f0827d7d85254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285395"
---
# <a name="seller-insights-release-notes"></a>Seller Insights 发行说明 

（发布日期：2019 年 3 月 1 日）

本文提供有关[云合作伙伴门户](https://cloudpartner.azure.com/#insights)中 Seller Insights 功能更改的信息。

## <a name="release-highlights-for-march-1-2019"></a>2019 年 3 月 1 日发布的亮点

* *添加到摘要的客户趋势*
* 摘要上*的前五个客户*反映客户拥有的所有 Azure 订阅
* *标准化使用趋势&活动订单趋势*汇总移动下*每月订单概览*
* *付款对账报告*更新
* 付款*方面排名前五的客户*反映客户拥有的所有 Azure 订阅
* 使用客户 ID 更新*的使用报告*
* *客户订单期限*&使用情况反映客户拥有的所有 Azure 订阅


（发布日期：2018 年 7 月 28 日）

## <a name="release-highlights-for-july-28-2018"></a>2018 年 7 月 28 日发布亮点


-   估计价格** 提供考虑到货币转换影响的客户费用视图。
-   预测的付款金额** 提供可能付款金额的早期视图。
-  使用情况参考标识符** 提供客户使用情况与付款订单之间的数据保真度
-   按每日粒度算的使用情况** 可提供更精细的粒度，更好地洞察客户使用情况。


### <a name="changes-to-data-structure-and-taxonomy"></a>数据结构和分类的更改

下表列出了此版本已添加或已进行实质性更改的指标。 

| 新建术语****                   |    **定义**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| 价格 (CC)                     | 给定 SKU 的一个使用单位的价格（采用客户的货币）。       |
| 估计的延伸费用 (CC) | 给定 SKU（以客户货币表示）的使用单位数量的估计扩展费用。 由于舍入或截断错误，此值可能不精确。   |
| 发布者货币 (PC)        | 发布者首选的付款货币。                               |
| 估计价格 (PC)           | 根据计算日期使用情况的外汇兑换（以发布者的货币）计算给定 SKU 的使用量单位的估计价格。 由于舍入或截断错误，此值可能不精确。   |
| 估计的延伸费用 (PC) | 根据计算日期使用日期的外汇兑换（以发布者的货币）计算给定 SKU 的使用单位数量的估计扩展费用。 由于舍入或截断错误，此值可能不精确。 |
| 估计的付款金额 (PC)          | 根据计算使用日期（以发布者的货币）的外汇兑换情况，估计给定 SKU 的使用单位数量。 由于舍入或截断错误，此值可能不精确。   |
| 使用情况参考                | 针对与付款报表中某个条目关联的给定 SKU，客户使用的一个或多个日期的标识符。 |
|  |  |
