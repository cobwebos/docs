---
title: "如何缩放 Azure 时序见解环境 | Microsoft Docs"
description: "本教程介绍如何缩放 Azure 时序见解环境"
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 8f6c66ea2173c98179ec899d6626c2ab6f7ec4b6
ms.contentlocale: zh-cn
ms.lasthandoff: 05/01/2017

---
# <a name="how-to-scale-your-time-series-insights-environment"></a>如何缩放时序见解环境

本教程介绍如何缩放时序见解环境。

> [!NOTE]
> 不允许跨 SKU 类型向上扩展。 不能将 S1 SKU 的环境转换为 S2 环境。

## <a name="s1-sku-ingress-rates-and-capacities"></a>S1 SKU 入口速率和容量

| S1 SKU 容量 | 入口速率 | 最大存储容量
| --- | --- | --- |
| 1 | 1 GB（1 百万个事件） | 每月 30 GB（3 千万个事件） |
| 10 | 10 GB（1 千万个事件） | 每月 300 GB（3 亿个事件） |

## <a name="s2-sku-ingress-rates-and-capacities"></a>S2 SKU 入口速率和容量

| S2 SKU 容量 | 入口速率 | 最大存储容量
| --- | --- | --- |
| 1 | 10 GB（1 千万个事件） | 每月 300 GB（3 亿个事件） |
| 10 | 100 GB（1 亿个事件） | 每月 3 TB（30 亿个事件） |

容量呈线性增长，因此容量为 2 的 S1 SKU 每日入口速率支持 2 GB（2 百万）的事件，每月支持 60 GB（6 千万）的事件。

## <a name="changing-the-capacity-of-your-environment"></a>更改环境容量

1. 在 Azure 门户中，选择想要更改其容量的环境。
1. 在“设置”下，单击“配置”。
1. 使用“容量”滑块来选择满足入口速率和存储容量需求的容量。

## <a name="next-steps"></a>后续步骤

* 验证新容量是否足以防止出现限制情况。 有关详细信息，请在[此处](time-series-insights-diagnose-and-solve-problems.md)参阅“你的环境可能受到了限制”一节。
