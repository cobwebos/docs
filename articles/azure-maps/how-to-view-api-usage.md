---
title: 如何查看 Azure Maps API 使用情况 | Microsoft Docs
description: 了解如何在门户中查看 Azure Maps API 调用的指标。
author: dsk-2015
ms.author: dkshir
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: e62d2ff1fdd6bc94244511a2de95c4268a58d6f9
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2018
ms.locfileid: "42142248"
---
# <a name="how-to-view-the-azure-maps-api-usage"></a>如何查看 Azure Maps API 使用情况
本文介绍如何在[门户](https://portal.azure.com)中查看 Azure Maps 帐户的 API 使用情况指标。 指标以便捷的图形形式显示，可自定义时间段。 

## <a name="view-metric-snapshot"></a>查看指标快照 

可在 Maps 帐户的“概述”页中查看部分常见指标。 当前，它可显示所选时间段的“请求总数”、“错误总数”和“可用性”。 

![Azure Maps 指标概述](media/how-to-view-api-usage/portal-overview.png)

如果需要针对特定分析自定义这些图，请继续了解下一部分内容。


## <a name="view-detailed-metrics"></a>查看详细指标

1. 在[门户](https://portal.azure.com)中登录到 Azure 订阅。 

2. 单击左侧的“所有资源”菜单项，然后导航到“Azure Maps 帐户”。

3. 打开 Maps 帐户后，单击左侧的“指标”菜单。

4. 在“指标”窗格中，选择以下一值：

    1. “可用性”，该指标显示一段时间内 API 可用性的平均值；或 
    2. “使用情况”，该指标显示帐户的使用量计算情况。 

    ![Azure Maps 指标窗格](media/how-to-view-api-usage/portal-metrics.png)

5. 选择指标后，可以通过选择默认值“最后 12 小时(自动)”，选择“时间范围”。 此外，还可选择“时间粒度”，并可选择在同一下拉菜单中显示本地或 GMT 时间。 单击“应用”。

    ![Azure Maps 指标时间范围](media/how-to-view-api-usage/time-range.png)
 
6. 添加指标后，可在与该指标相关的属性中“添加筛选器”，并然后选择要查看其图的属性的值。 

    ![Azure Maps 指标筛选器](media/how-to-view-api-usage/filter.png)

7. 此外，还可以根据所选的指标属性，对指标“应用拆分”。 如此，即可将指标图拆分成多个图，每个图显示一个属性值。 例如，在下图中，每个图的颜色对应图底部所示的属性值。

    ![Azure Maps 指标拆分](media/how-to-view-api-usage/splitting.png)
 
8. 此外，还可以在同一图中观察多个指标，只需单击顶部的“添加指标”按钮即可。


## <a name="next-steps"></a>后续步骤

现已了解如何跟踪 Azure Maps 使用情况，可以继续阅读有关正在使用的 API 的更多信息：

* [Azure Maps REST API 文档](https://docs.microsoft.com/rest/api/maps)
