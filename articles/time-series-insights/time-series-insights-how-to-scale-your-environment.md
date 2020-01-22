---
title: 如何缩放环境-Azure 时序见解 |Microsoft Docs
description: 了解如何使用 Azure 门户缩放 Azure 时序见解环境。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 23efda2793ef5d323089ee5b72fb1ea873de6b20
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310979"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>如何缩放时序见解环境

本文介绍如何使用[Azure 门户](https://portal.azure.com)更改时序见解环境的容量。 容量是应用于入口速率、存储容量以及与所选 SKU 关联的成本的乘数。

可以使用 Azure 门户来增加或减少给定定价 SKU 中的容量。

但是，不允许更改定价层 SKU。 例如，不能将定价 SKU 为 S1 的环境转换为 S2，反之亦然。

## <a name="ga-limits"></a>GA 限制

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>更改环境容量

1. 在 Azure 门户中，找到并选择自己的时序见解环境。

1. 在时序见解环境的菜单中，选择 "**存储配置**"。

   [![配置时序见解容量](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. 调整“容量”滑块来选择满足入口速率和存储容量需求的容量。 请注意，**入口速率**、**存储容量**和**估计成本**会动态更新，以显示更改产生的影响。

   [使用容量滑块 ![配置环境](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   或者，可以在滑块右侧的文本框中键入容量乘数的数字。

1. 选择“保存”以缩放环境。 在提交更改之前，进度指示器会短暂显示。

1. 验证新容量是否[足以防止](time-series-insights-diagnose-and-solve-problems.md)阻止。

## <a name="next-steps"></a>后续步骤

- 有关详细信息，请参阅[了解时序见解中的保留期](time-series-insights-concepts-retention.md)。

- 了解如何[在 Azure 时序见解中配置数据保留期](time-series-insights-how-to-configure-retention.md)。

- 了解如何[规划您的环境](time-series-insights-environment-planning.md)。