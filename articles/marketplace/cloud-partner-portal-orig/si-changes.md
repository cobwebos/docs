---
title: 卖方 Insights 发行说明
description: 提供有关 Seller Insights 功能更改的信息。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: pabutler
ms.openlocfilehash: c6e9e4fe672c7e171ed4b1cd60655f9e71a562e6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943129"
---
# <a name="seller-insights-release-notes"></a>Seller Insights 发行说明 

(发布日期：2019 年 3 月 1 日)

本文提供有关[云合作伙伴门户](https://cloudpartner.azure.com/#insights)中 Seller Insights 功能更改的信息。

## <a name="release-highlights-for-march-1-2019"></a>2019 年 3 月 1 日的版本主要变化

* *客户趋势*添加到摘要
* *排名靠前的 5 位客户*摘要上反映客户拥有的所有 Azure 订阅
* *规范化使用趋势和活动订单趋势*上移动下摘要*一目了然的每月订单*
* *付款对帐报表*更新
* *排名靠前的 5 位客户*付款上反映客户拥有的所有 Azure 订阅
* *使用情况报告*使用客户 ID 来更新
* *客户任期*在订单和使用情况上反映了一位客户拥有的所有 Azure 订阅


(发布日期：2018 年 7 月 28 日)

## <a name="release-highlights-for-july-28-2018"></a>2018 年 7 月 28 日的版本主要变化


-   估计价格  提供考虑到货币转换影响的客户费用视图。
-   预测的付款金额  提供可能付款金额的早期视图。
-  使用情况参考标识符  提供客户使用情况与付款订单之间的数据保真度
-   按每日粒度算的使用情况  可提供更精细的粒度，更好地洞察客户使用情况。


### <a name="changes-to-data-structure-and-taxonomy"></a>数据结构和分类的更改

下表列出了此版本已添加或已进行实质性更改的指标。 

| 新建术语                    |    **定义**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| 价格 (CC)                     | 给定 SKU 的一个使用单位的价格（采用客户的货币）。       |
| 估计的延伸费用 (CC) | 针对给定 SKU 的使用单位数量估计的延伸费用（按客户货币计算）。 由于舍入或截断错误，此值可能不精确。   |
| 发布者货币 (PC)        | 发布者首选的付款货币。                               |
| 估计价格 (PC)           | 根据用量计算日期的外币兑换率，针对给定 SKU 的一个使用单位估计的价格（按发布者的货币计算）。 由于舍入或截断错误，此值可能不精确。   |
| 估计的延伸费用 (PC) | 根据用量计算日期的外币兑换率，针对给定 SKU 的使用单位数量估计的延伸费用（按发布者的货币计算）。 由于舍入或截断错误，此值可能不精确。 |
| 估计的付款金额 (PC)          | 根据用量计算日期的外币兑换率，针对给定 SKU 的使用单位数量估计的付款金额（按发布者的货币计算）。 由于舍入或截断错误，此值可能不精确。   |
| 使用情况参考                | 针对与付款报表中某个条目关联的给定 SKU，客户使用的一个或多个日期的标识符。 |
|  |  |
