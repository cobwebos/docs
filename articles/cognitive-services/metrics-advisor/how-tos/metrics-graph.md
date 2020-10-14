---
title: 指标顾问指标图
titleSuffix: Azure Cognitive Services
description: 如何配置度量值图并在数据中可视化相关的异常。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbullwin
ms.openlocfilehash: bcedef4a1339dacaff3fe841e97f985c42320819
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043147"
---
# <a name="how-to-build-a-metrics-graph-to-analyze-related-metrics"></a>如何：生成度量值图以分析相关指标

每个指标顾问中的每个指标都由一个模型单独监视，该模型从历史数据中学习，以预测未来趋势。 每个度量值都有单独的模型。 但是，在某些情况下，多个指标可能彼此相关，而异常需要跨多个指标进行分析。 **度量值图**可帮助提供此功能。 

例如，如果在不同的指标中具有不同的遥测数据流，则指标顾问将单独对其进行监视。 如果某个指标中的异常导致其他情况下出现异常，则在对事件进行寻址时，查找这些关系和数据的根本原因可能会很有帮助。 使用 "指标" 图可以创建发现异常的可视拓扑图。 

## <a name="select-a-metric-to-put-the-first-node-to-the-graph"></a>选择一个度量值，将第一个节点置于图形中

单击导航栏中的 " **指标图形** " 选项卡。 构建指标图形的第一步是将节点放在图形上。 选择页面顶部的 "数据馈送" 和 "指标"。 节点将显示在底部面板中。 

:::image type="content" source="../media/graph/metrics-graph-select.png" alt-text="选择指标":::

## <a name="add-a-noderelation-on-existing-node"></a>在现有节点上添加节点/关系

接下来，需要添加另一个节点，并指定与现有节点 () 的关系。 选择现有节点并右键单击该节点。 将出现一个上下文菜单，其中包含多个选项。 

单击 " **添加关系**"，您将能够选择另一个度量值，并指定两个节点之间的关系类型。 您还可以应用特定的维度筛选器。 

:::image type="content" source="../media/graph/metrics-graph-node-action.png" alt-text="选择指标":::

重复上述步骤后，将有一个指标图形，描述所有相关指标之间的关系。
**节点颜色提示**
> [!TIP]
> - 选择度量值和维度筛选器时，关系图中具有相同指标和维度筛选器的所有节点都将着色为 **<font color=blue>蓝色</font>**。
> - 表示关系图中的度量值的未选定节点将为 **<font color=green>绿色</font>**。
> - 如果在当前指标中观察到异常，则该节点将为 **<font color=red>红色</font>**。

## <a name="view-related-metrics-anomaly-status-in-incident-hub"></a>在事件中心内查看相关指标异常状态

构建指标图形时，每当检测到图表中的度量值时，你都可以查看相关异常状态，并获得事件的高级视图。 

单击到关系图中的事件，并向下滚动到 " **跨指标分析**"，并将其置于诊断信息下方。

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="选择指标":::

## <a name="next-steps"></a>后续步骤

- [使用反馈调整异常检测](anomaly-feedback.md)
- [诊断事件](diagnose-incident.md)。
- [配置指标并微调检测配置](configure-metrics.md)
