---
title: 如何缩放 Azure 时序见解环境 | Microsoft Docs
description: 本文介绍如何缩放 Azure 时序见解环境。 使用 Azure 门户增加或减少定价 SKU 中的容量。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: f80c3ad28c3944bd365100d1d873231869da46f8
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629712"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>如何缩放时序见解环境

本文介绍如何使用 Azure 门户更改时序见解环境的容量。 容量是应用于入口速率、存储容量以及与所选 SKU 关联的成本的乘数。 

可以使用 Azure 门户来增加或减少给定定价 SKU 中的容量。 

但是，不允许更改定价层 SKU。 例如，不能将定价 SKU 为 S1 的环境转换为 S2，反之亦然。 


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

## <a name="change-the-capacity-of-your-environment"></a>更改环境容量
1. 在 Azure 门户中，找到并选择自己的时序见解环境。 

2. 在时序见解环境的菜单中，选择“配置”。

   ![configure.png](media/scale-your-environment/configure.png)

3. 调整“容量”滑块来选择满足入口速率和存储容量需求的容量。 请注意，**入口速率**、**存储容量**和**估计成本**会动态更新，以显示更改产生的影响。 

   ![滑块](media/scale-your-environment/slider.png)

   或者，可以在滑块右侧的文本框中键入容量乘数的数字。 

4. 选择“保存”以缩放环境。 在提交更改之前，进度指示器会短暂显示。 

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [验证新容量是否足以防止出现限制情况](time-series-insights-diagnose-and-solve-problems.md)。
