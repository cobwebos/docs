---
title: Azure Monitor 工作簿概述
description: 使用预先构建的自定义参数化工作簿简化复杂的报表
author: mrbullwinkle
manager: carmonm
services: azure-monitor
ms.service: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: cf11ce3a77fb47b7d8586ef56141a72cd0a10c2e
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872853"
---
# <a name="azure-monitor-workbooks"></a>Azure Monitor 工作簿

工作簿提供了一个灵活的画布用于数据分析，并在 Azure 门户中创建了丰富的视觉对象报表。 它们允许你从 Azure 跨多个数据源，并将其合并为统一的交互式体验。 

## <a name="data-sources"></a>数据源

工作簿可以查询 Azure 中多个源的数据。 工作簿的作者可以转换此数据，以便深入了解底层组件的可用性、性能、使用情况和总体运行状况。 例如，分析虚拟机中的性能日志来识别高 CPU 或内存不足实例，并将结果显示为交互式报表中的网格。
  
但工作簿的强大功能是能够将来自不同源的数据组合到一个报表中。 这允许创建复合资源视图或跨资源的联接，从而实现更丰富的数据和更深入的信息，这种情况可能会导致您无法实现。

工作簿当前与以下数据源兼容：

* [日志](workbooks-data-sources.md#logs)
* [指标](workbooks-data-sources.md#metrics)
* [Azure 资源关系图](workbooks-data-sources.md#azure-resource-graph)
* [警报（预览）](workbooks-data-sources.md#alerts-preview)
* [工作负荷运行状况（预览版）](workbooks-data-sources.md#workload-health-preview)
* [Azure 资源运行状况（预览）](workbooks-data-sources.md#azure-resource-health)
* [Azure 数据资源管理器（预览版）](workbooks-data-sources.md#azure-data-explorer-preview)

## <a name="visualizations"></a>可视化效果

工作簿提供了一组丰富的功能来可视化数据。 有关每种可视化类型的详细示例，可以参阅下面的示例链接：

* [Text](workbooks-visualizations.md#text)
* [图表](workbooks-visualizations.md#charts)
* [置](workbooks-visualizations.md#grids)
* [磁贴](workbooks-visualizations.md#tiles)
* [林中](workbooks-visualizations.md#trees)
* [图表](workbooks-visualizations.md#graphs)

![示例工作簿可视化效果](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>入门

若要浏览工作簿体验，请首先导航到 Azure Monitor 服务。 可以通过在 "Azure 门户中的" 搜索 "框中键入**Monitor**来完成此操作。

然后选择 "**工作簿（预览）** "。

![红色框中突出显示的工作簿预览按钮的屏幕截图](./media/workbooks-overview/workbooks-preview.png)

### <a name="gallery"></a>库

这会转到工作簿库：

![Azure Monitor 工作簿库视图的屏幕截图](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>工作簿和工作簿模板

您可以使用绿色显示_工作簿_，并在紫色中看到许多_工作簿模板_。 模板可用作特选的报表，这些报表旨在供多个用户和团队灵活重用。 打开模板将创建一个使用模板内容填充的暂时性工作簿。 

您可以调整基于模板的工作簿的参数和执行分析，而无需担心会破坏未来的同事报告体验。 如果打开模板、进行一些调整，然后选择 "保存" 图标，则会将模板另存为工作簿，该工作簿会以绿色显示，使原始模板保持不变。 

在后台，模板还不同于已保存的工作簿。 保存工作簿会创建关联的 Azure 资源管理器资源，而仅打开模板时创建的临时工作簿没有关联的唯一资源。 若要详细了解如何在工作簿中管理访问控制，请参阅[工作簿访问控制文章](workbooks-access-control.md)。

### <a name="exploring-a-workbook-template"></a>浏览工作簿模板

选择 "**应用程序故障分析**" 以查看默认应用程序工作簿模板之一。

![应用程序故障分析模板的屏幕截图](./media/workbooks-overview/failure-analysis.png)

如前文所述，打开模板会创建一个临时工作簿，以便能够与进行交互。 默认情况下，工作簿将在读取模式中打开，该模式仅显示原始模板作者创建的预期分析体验的信息。

对于此特定工作簿，体验是交互式的。 你可以调整订阅、目标应用以及要显示的数据的时间范围。 做出这些选择后，HTTP 请求网格也是交互式的，因此，选择单个行将更改报表底部的两个图表中呈现的数据。

### <a name="editing-mode"></a>编辑模式

若要了解如何将此工作簿模板放置在一起，需要通过选择 "**编辑**" 来切换到编辑模式。 

![应用程序故障分析模板的屏幕截图](./media/workbooks-overview/edit.png)

切换到编辑模式后，你会注意到，与工作簿的每个单独方位相对应的**编辑**框会显示在右侧。

![编辑按钮的屏幕截图](./media/workbooks-overview/edit-mode.png)

如果在 "请求数据" 网格下直接选择 "编辑" 按钮，我们可以看到工作簿的这一部分包含一个针对 Application Insights 资源的数据的 Kusto 查询。

![底层 Kusto 查询的屏幕截图](./media/workbooks-overview/kusto.png)

单击右侧的其他 "**编辑**" 按钮将显示许多构成工作簿（如基于 markdown 的[文本框](workbooks-visualizations.md#text)、[参数选择](workbooks-parameters.md)UI 元素以及其他[图表/可视化对象类型](workbooks-visualizations.md)）的核心组件。 

在编辑模式下浏览预建模板，然后对其进行修改以满足你的需求，并保存你自己的自定义工作簿是一种非常好的方法，可让你了解 Azure Monitor 工作簿的可能情况。

## <a name="pinning-visualizations"></a>固定可视化效果

工作簿中的 "文本"、"查询" 和 "指标" 步骤可以通过在工作簿处于 pin 模式时使用这些项上的 "固定" 按钮进行固定，或者，如果工作簿作者已为该元素启用了设置以使 pin 图标可见， 

若要访问 pin 模式，请单击 "**编辑**" 进入编辑模式，并选择顶部栏中的蓝色图钉图标。 然后，将在屏幕右侧的每个相应工作簿部件的*编辑*框上方显示单个图钉图标。

![Pin 体验](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> 保存工作簿时，保存工作簿的状态，并且在修改基础工作簿时，仪表板上固定工作簿不会更新。 若要更新固定工作簿部件，你将需要删除并重新固定该部件。

## <a name="dashboard-time-ranges"></a>仪表板时间范围

如果将固定的项配置为使用*时间范围*参数，则固定工作簿查询部分将遵循仪表板的时间范围。 仪表板的时间范围值将用作时间范围参数的值，任何更改的仪表板时间范围将导致固定项更新。 如果某个固定的部分正在使用该仪表板的时间范围，则在时间范围发生变化时，你将看到固定部件更新的副标题以显示仪表板的时间范围。 

此外，使用时间范围参数固定工作簿部件将按仪表板的时间范围确定的费率自动刷新。 上次运行查询的时间将显示在固定部件的副标题中。

如果某个固定步骤具有显式设置的时间范围（不使用时间范围参数），则无论仪表板的设置如何，该时间范围始终将用于仪表板。 固定部件的副标题不会显示仪表板的时间范围，并且该查询不会在仪表板上自动刷新。 该副标题将显示上次执行查询的时间。

> [!NOTE]
> 固定到仪表板时，当前不支持使用*合并*数据源的查询。

## <a name="sharing-workbook-templates"></a>共享工作簿模板

开始创建自己的工作簿模板后，你可能想要将其与更广泛的社区共享。 若要了解详细信息，并浏览不属于默认 Azure Monitor 库视图的其他模板，请访问我们的[GitHub 存储库](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)。 若要浏览现有工作簿，请访问 GitHub 上的[工作簿库](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks)。

## <a name="next-step"></a>后续步骤

* [开始](workbooks-visualizations.md)了解有关工作簿许多丰富可视化效果选项的详细信息。
* [控制](workbooks-access-control.md)和共享对工作簿资源的访问权限。
