---
title: Azure Monitor 工作簿概述
description: 使用预先构建的自定义参数化工作簿简化复杂的报表
author: mrbullwinkle
manager: carmonm
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 29e675f3ae35df9211f58d45ad8450566d67a588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658228"
---
# <a name="azure-monitor-workbooks"></a>Azure Monitor 工作簿

工作簿为数据分析和在 Azure 门户中创建丰富的可视化报表提供了灵活的画布。 它们允许您从 Azure 中挖掘多个数据源，并将它们合并到统一的交互式体验中。 

## <a name="data-sources"></a>数据源

工作簿可以查询 Azure 中的多个源的数据。 工作簿的作者可以转换此数据，以便深入了解底层组件的可用性、性能、使用情况和总体运行状况。 例如，分析来自虚拟机的性能日志，以识别高 CPU 或低内存实例，并在交互式报表中将结果显示为网格。
  
但工作簿的真正力量是能够将来自不同来源的数据合并到单个报表中。 这允许创建复合资源视图或跨资源联接，从而实现更丰富的数据和见解，否则将是不可能的。

工作簿当前与以下数据源兼容：

* [日志](workbooks-data-sources.md#logs)
* [指标](workbooks-data-sources.md#metrics)
* [Azure 资源图](workbooks-data-sources.md#azure-resource-graph)
* [警报（预览版）](workbooks-data-sources.md#alerts-preview)
* [工作负载运行状况（预览）](workbooks-data-sources.md#workload-health-preview)
* [Azure 资源运行状况（预览）](workbooks-data-sources.md#azure-resource-health)
* [Azure 数据资源管理器（预览）](workbooks-data-sources.md#azure-data-explorer-preview)

## <a name="visualizations"></a>可视化效果

工作簿提供了一组丰富的功能，用于可视化数据。 有关每种可视化类型的详细示例，请参阅以下示例链接：

* [Text](workbooks-visualizations.md#text)
* [图表](workbooks-visualizations.md#charts)
* [网 格](workbooks-visualizations.md#grids)
* [平铺](workbooks-visualizations.md#tiles)
* [Trees](workbooks-visualizations.md#trees)
* [关系图](workbooks-visualizations.md#graphs)

![工作簿可视化示例](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>入门

要浏览工作簿体验，首先导航到 Azure 监视器服务。 这可以通过在 Azure 门户中的搜索框中键入**监视器**来实现。

然后选择**工作簿（预览）。**

![工作簿预览按钮的屏幕截图突出显示在红色框中](./media/workbooks-overview/workbooks-preview.png)

### <a name="gallery"></a>库

这将带您到工作簿库：

![Azure 监视器工作簿库视图的屏幕截图](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>工作簿与工作簿模板

您可以看到绿色_工作簿_和紫色工作_簿_模板。 模板用作精心策划的报表，专为多个用户和团队灵活重用而设计。 打开模板将创建一个包含模板内容的瞬态工作簿。 

您可以调整基于模板的工作簿的参数并执行分析，而不必担心破坏同事的未来报告体验。 如果打开模板，请进行一些调整，然后选择将模板保存为工作簿的保存图标，然后以绿色显示，使原始模板保持不变。 

在引擎盖下，模板也不同于保存的工作簿。 保存工作簿将创建关联的 Azure 资源管理器资源，而刚刚打开模板时创建的瞬态工作簿没有与其关联的唯一资源。 要了解有关如何在工作簿中管理访问控制的更多详细信息，请参阅[工作簿访问控制文章](workbooks-access-control.md)。

### <a name="exploring-a-workbook-template"></a>探索工作簿模板

选择**应用程序故障分析**以查看默认应用程序工作簿模板之一。

![应用程序故障分析模板的屏幕截图](./media/workbooks-overview/failure-analysis.png)

如前所述，打开模板将创建一个临时工作簿，以便您能够与之交互。 默认情况下，工作簿在读取模式下打开，该模式下仅显示原始模板作者创建的预期分析体验的信息。

就此特定工作簿而言，体验是交互式的。 您可以调整要显示的数据的订阅、目标应用和时间范围。 进行这些选择后，HTTP 请求网格也是交互式的，根据该网格，选择单个行将更改报表底部两个图表中呈现的数据。

### <a name="editing-mode"></a>编辑模式

要了解如何将此工作簿模板放在一起，您需要通过选择 **"编辑**"来交换到编辑模式。 

![应用程序故障分析模板的屏幕截图](./media/workbooks-overview/edit.png)

切换到编辑模式后，您会注意到许多**编辑**框显示在与工作簿的每个方面对应的正确。

![编辑按钮的屏幕截图](./media/workbooks-overview/edit-mode.png)

如果我们立即在请求数据网格下选择编辑按钮，我们可以看到，工作簿的这一部分包含针对来自应用程序见解资源的数据的 Kusto 查询。

![基础 Kusto 查询的屏幕截图](./media/workbooks-overview/kusto.png)

单击右侧的其他 **"编辑"** 按钮将显示一些构成工作簿的核心组件，如基于标记[的文本框](workbooks-visualizations.md#text)、[参数选择](workbooks-parameters.md)UI 元素和其他[图表/可视化类型](workbooks-visualizations.md)。 

在编辑模式下浏览预构建的模板，然后修改这些模板以满足您的需要并保存自己的自定义工作簿，是开始了解 Azure 监视器工作簿可能实现的内容的绝佳方式。

## <a name="pinning-visualizations"></a>固定可视化效果

在工作簿处于固定模式时，或者工作簿作者启用了该元素的设置以使引脚图标可见，则可以在工作簿上使用这些项目上的固定按钮来固定工作簿中的文本、查询和指标步骤。 

要访问引脚模式，请单击 **"编辑"** 以进入编辑模式，然后选择顶部栏中的蓝色引脚图标。 然后，屏幕右侧每个相应工作簿部件*的"编辑"* 框上方将显示一个单独的引脚图标。

![引脚体验](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> 工作簿的状态在 pin 时保存，如果修改了基础工作簿，仪表板上的固定工作簿将不会更新。 为了更新固定工作簿部件，您需要删除并重新固定该部件。

## <a name="dashboard-time-ranges"></a>仪表板时间范围

如果固定项配置为使用*时间范围*参数，则固定工作簿查询部件将尊重仪表板的时间范围。 仪表板的时间范围值将用作时间范围参数的值，仪表板时间范围的任何更改都将导致固定项更新。 如果固定零件使用仪表板的时间范围，您将看到固定零件更新的副标题，以便每当时间范围更改时显示仪表板的时间范围。 

此外，使用时间范围参数固定工作簿部件将以仪表板的时间范围确定的速率自动刷新。 上次运行查询的时间将显示在固定部分的副标题中。

如果固定步骤具有显式设置的时间范围（不使用时间范围参数），则无论仪表板的设置如何，该时间范围始终用于仪表板。 固定部件的副标题不会显示仪表板的时间范围，并且查询不会在仪表板上自动刷新。 字幕将显示上次执行查询的时间。

> [!NOTE]
> 固定到仪表板时，当前不支持使用*合并*数据源的查询。

## <a name="sharing-workbook-templates"></a>共享工作簿模板

开始创建自己的工作簿模板后，您可能需要与更广泛的社区共享它。 要了解更多信息，并浏览不属于默认 Azure 监视器库视图的其他模板，请访问我们的[GitHub 存储库](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)。 要浏览现有工作簿，请访问 GitHub 上的[工作簿库](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks)。

## <a name="next-step"></a>后续步骤

* [开始](workbooks-visualizations.md)了解有关工作簿的许多丰富可视化选项的详细信息。
* [控制和](workbooks-access-control.md)共享对工作簿资源的访问权限。
