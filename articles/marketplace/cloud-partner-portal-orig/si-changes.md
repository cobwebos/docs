---
title: 卖方 Insights 发行说明
description: 提供有关 Seller Insights 功能更改的信息。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: pabutler
ms.openlocfilehash: 4f7e461241793b27d838c04311509f43932df291
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808959"
---
# <a name="seller-insights-release-notes"></a>Seller Insights 发行说明 

（发布日期：2019年3月1日）

本文提供有关[云合作伙伴门户](https://cloudpartner.azure.com/#insights)中 Seller Insights 功能更改的信息。

## <a name="release-highlights-for-march-1-2019"></a>2019年3月1日发布亮点

* 添加到摘要的*客户趋势*
* 摘要上*排名前五的客户*反映了客户拥有的所有 Azure 订阅
* *标准化的使用趋势 & 活动订单*按*月订单*下的摘要移动趋势
* 已更新*支出对帐报表*
* *排名前五*位的客户反映了客户拥有的所有 Azure 订阅
* 使用客户 ID 更新了*使用情况报表*
* *客户账龄*订单 & 使用量反映了客户拥有的所有 Azure 订阅


（发布日期：2018 年 7 月 28 日）

## <a name="release-highlights-for-july-28-2018"></a>2018年7月28日发布亮点


-   估计价格提供考虑到货币转换影响的客户费用视图。
-   预测的付款金额提供可能付款金额的早期视图。
-  使用情况参考标识符提供客户使用情况与付款订单之间的数据保真度
-   按每日粒度算的使用情况可提供更精细的粒度，更好地洞察客户使用情况。


### <a name="changes-to-data-structure-and-taxonomy"></a>数据结构和分类的更改

下表列出了此版本已添加或已进行实质性更改的指标。 

| 新建术语                   |    **定义**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| 价格 (CC)                     | 给定 SKU 的一个使用单位的价格（采用客户的货币）。       |
| 估算的延伸费用 (CC) | 给定 SKU 的用量（以客户的货币表示）的用量的估计延伸费用。 由于舍入或截断错误，此值可能不精确。   |
| 发布者货币 (PC)        | 发布者首选的付款货币。                               |
| 估计价格 (PC)           | 根据日期使用情况的外币转换计算给定 SKU 的使用单位的预计价格（以出版商的币种表示）。 由于舍入或截断错误，此值可能不精确。   |
| 估计的延伸费用 (PC) | 计算基于在日期使用情况上基于外汇转换的给定 SKU 的用量的用量（以出版商的货币表示）。 由于舍入或截断错误，此值可能不精确。 |
| 估算的付款金额 (PC)          | 计算使用情况的日期（以出版商的货币表示），针对给定 SKU 的使用量的使用情况的估计支付。 由于舍入或截断错误，此值可能不精确。   |
| 使用情况参考                | 针对与付款报表中某个条目关联的给定 SKU，客户使用的一个或多个日期的标识符。 |
|  |  |
