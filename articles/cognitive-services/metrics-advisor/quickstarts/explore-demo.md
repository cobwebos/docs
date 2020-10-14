---
title: 开始浏览指标顾问演示
titleSuffix: Azure Cognitive Services
description: 使用我们提供的演示了解指标顾问 Web 界面
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 39748bacf784af0b3f162f960cd7d87cbd03a2eb
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047584"
---
# <a name="quickstart-explore-the-metrics-advisor-demo-with-example-data"></a>快速入门：浏览带有示例数据的指标顾问演示

> [!Note]
> 指标顾问演示为只读，无法加入你的数据。 若要对数据使用该服务，请先[创建指标顾问资源](web-portal.md)。

使用本文快速开始浏览指标顾问中的主要功能。 我们提供了一个演示网站，其中包含示例数据和预设配置，以便你熟悉功能完备的 Web 门户。

单击[此链接](https://aka.ms/MetricsAdvisor/Demo)转到演示网站。

## <a name="view-the-available-sample-data"></a>查看可用的示例数据

登录到演示网站后，“数据馈送”页将会显示。 数据馈送是时序数据的逻辑组，来自数据源查询。 有关指标顾问中使用的术语和概念的详细信息，请参阅[术语表](../glossary.md)。 

此处列出了多个数据馈送，它们从不同类型的数据源（例如 Azure SQL 数据库或 Azure 表）引入。 每个数据馈送都使用略微不同的配置设置来连接到关联的数据存储。

:::image type="content" source="../media/sample-datafeeds.png" alt-text="示例数据列表" lightbox="../media/sample-datafeeds.png":::

## <a name="explore-the-data-feed-configurations"></a>浏览数据馈送配置

单击“Sample - Cost/Revenue - City/Category”数据馈送。 你将看到馈送的多个细节部分：

* 数据馈送名称和引入状态。
* 从数据源查询的指标列表。 例如“cost”和“revenue” 。 
* 数据馈送变为不可用时的警报历史记录。 
* 数据馈送更新时间的日志。   
* 数据馈送信息和设置。

:::image type="content" source="../media/data-feed-view.png" alt-text="示例数据列表" lightbox="../media/data-feed-view.png":::


## <a name="view-time-series-visualizations-and-configurations"></a>查看时序可视化效果和配置

单击进入“Sample - Cost/Revenue - City/Category”数据馈送中的“cost”指标 。 你将看到根据维度切片的关联时序，以及根据历史指标数据显示的可视化效果。 指标数据周围的蓝带表示指标顾问的机器学习模型中的预期值范围。 位于此带之外的点将以红点标记，表示检测到异常。 

:::image type="content" source="../media/series-visualization.png" alt-text="示例数据列表" lightbox="../media/series-visualization.png":::

可以通过在指标详细信息页的左侧优化“检测配置”来配置异常情况检测。 有多种异常情况检测方法可用，你可以将这些方法加以组合。 还可以尝试不同的敏感度、检测方向和其他配置。 通过“检测配置”底部的”高级配置”链接，可以创建更复杂的自定义检测设置，这些设置可用于组或单个序列 。 

还可以通过向检测算法提供反馈来优化异常情况检测。 单击进入异常，并使用“添加反馈”面板配置其异常状态、季节性和更改点状态。 此反馈将融入将来点的检测中。  

面板中的底部有一个“前往事件中心”链接，该链接会将你定向到事件分析页并分析事件的根本原因 。  

:::image type="content" source="../media/incident-link.png" alt-text="示例数据列表" lightbox="../media/incident-link.png":::

## <a name="explore-anomaly-detection-results-and-perform-root-cause-analysis"></a>浏览异常情况检测结果并执行根本原因分析

从异常中单击“前往事件中心”链接时，你将看到一个事件分析页，其中包含有关事件的诊断见解，如严重性、涉及的异常数量以及开始/结束时间。 “根本原因”部分通过分析事件树自动显示建议，并考虑到偏差、分布和对父异常的贡献，而这些可能是事件的根本原因。

“诊断”部分显示事件树以及用于诊断事件的多个选项卡。

:::image type="content" source="../media/incident-diagnostic.png" alt-text="示例数据列表" lightbox="../media/incident-diagnostic.png":::

通过查明事件的根本原因，你可以在情况恶化之前采取措施并缓解问题。 还可以单击提供的其他诊断功能来浏览更多见解。 

## <a name="next-steps"></a>后续步骤

- [使用 Web 门户](web-portal.md)
- [使用 REST API](rest-api.md)
- [加入数据馈送](../how-tos/onboard-your-data.md)
    - [管理数据馈送](../how-tos/manage-data-feeds.md)
    - [不同数据源的配置](../data-feeds-from-different-sources.md)
