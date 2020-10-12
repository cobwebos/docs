---
title: Azure Monitor Log Analytics 中的已保存查询
description: 你可以从其着手并根据需要进行修改的查询
ms.subservice: logs
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 06/16/2020
ms.openlocfilehash: 462358ab967d4ba26a265d7b5f401b995223815e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85959836"
---
# <a name="saved-queries-in-azure-monitor-log-analytics"></a>Azure Monitor Log Analytics 中的已保存查询

Log Analytics 提供了示例查询集，你可以运行这些查询本身，或将其用作你自己的查询的起点。 本文介绍了示例查询以及如何使用它们。

如果你不熟悉 Log Analytics 或 KQL 查询语言，则可从示例查询着手。 它们可以提供对资源的即时见解，并提供了一种在你开始学习和使用 KQL 时可以使用的很好的方法，从而缩短你的 Log Analytics 入门时间。 我们收集并特选了 250 多个示例查询（这些查询设计用来提供即时值），这些示例查询的数量还在不断增长。

## <a name="in-context-queries"></a>上下文内查询

新体验在上下文中筛选查询和提供查询建议。 换句话说，系统只会自动显示与所选作用域相关的查询。

- 对于 **单个资源** –按资源类型筛选查询。
- 对于**资源组**，将根据特定资源组中的资源对查询进行筛选。
- 对于 **工作区** –根据工作区中安装的解决方案筛选查询。

对于所有 Log Analytics 作用域，此行为都是一致的。 如果看不到所需资源类型的示例查询，则可能是由于在上下文中进行了筛选。 下文中的一个部分介绍了如何删除上下文中作用域界定，以便你可以查看所有可能的查询。

> [!TIP]
> 你的作用域中的资源越多，门户筛选和显示“示例查询”对话框所需的时间就越长。

## <a name="example-query-user-interface"></a>示例查询用户界面

可以从两个不同的位置访问示例查询。

### <a name="example-query-dialog"></a>“示例查询”对话框

当你首次进入 Log Analytics 体验时，会自动显示“示例查询”对话框。  还可以通过单击屏幕右上角的“示例查询”来访问它。

![边栏](media/saved-queries/sidebar-2.png)

此时会显示“示例查询”对话框，如下所示：  

![“示例查询”屏幕](media/saved-queries/example-query-start.png)

前面的屏幕截图显示了 Azure Key Vault 实例的日志屏幕。 如本文前面所述，将根据上下文显示查询。  因此，屏幕截图只显示了与 Key Vault 相关的示例。 如果选择整个订阅，则会显示针对该订阅中所有资源类型的查询。  

每个示例查询都通过一个卡片来表示。 你可以快速浏览查询来查找所需内容。 你可以直接从对话框中运行查询，也可以选择将其加载到查询编辑器，以便进行更细致的微调和调整。

### <a name="sidebar-query-experience"></a>边栏查询体验

可以从 Log Analytics 左侧边栏中的查询窗格访问与对话框体验完全相同的功能。 可以将鼠标指针悬停在查询名称上来获取查询说明和其他功能。

![边栏](media/saved-queries/sidebar-3.png)

## <a name="finding-and-filtering-queries"></a>查找和筛选查询

本部分中的选项同时存在于对话框和边栏查询体验中，但用户界面略有不同。  

### <a name="use-favorites"></a>使用收藏夹

你可以收藏经常使用的查询，以便能够更快速地进行访问。

> [!TIP]
> 收集查询供以后查看是一种很好的入门方式。 找到所需查询并单击查询旁边的星号可将其添加到收藏夹。 如果以后发现该查询对你没用，则可将其取消收藏。  

### <a name="filtering-and-group-by"></a>筛选和分组依据

虽然查询对话框体验会进行筛选以仅显示具有正确上下文的查询，但你可以选择删除筛选器，这样就可以查看所有查询。

### <a name="group-by"></a>Group by

单击“分组依据”下拉列表，更改查询的分组：

![“示例查询”屏幕中的分组依据](media/saved-queries/example-query-groupby.png)

此对话框支持以下分组依据：

- **资源类型** -Azure 中定义的资源，例如虚拟机。 请参阅 [Azure Monitor 表参考](/azure/azure-monitor/reference/tables/tables-resourcetype)，以了解从 Azure Monitor 日志/Log Analytics 表到资源类型的完整映射。  
- **类别** –一种类型的信息，如 *安全* 或 *审核*。 类别与在“表”边栏窗格中定义的类别完全相同。 有关类别的完整列表，请参阅 [Azure Monitor 表参考](/azure/azure-monitor/reference/tables/tables-category)。  
- **解决方案** -与查询关联的 Azure Monitor 解决方案
- **主题** –示例查询（例如 *活动日志* 或 *应用日志*）的主题。 对示例查询而言，主题属性具有唯一性，可能会因资源类型而异。

分组值还用作活动目录。 单击屏幕左侧的某个值会将查询视图向右滚动到所单击的项。

### <a name="filter"></a>筛选器

你还可以根据前面提到的分组依据值筛选查询。 在“示例查询”对话框中，可以在顶部找到筛选器。

![“示例查询”屏幕上的筛选器](media/saved-queries/example-query-filter.png)

### <a name="combining-group-by-and-filter"></a>组合使用分组依据和筛选器

筛选器和分组依据功能设计为协同工作。 它们在如何排列查询方面提供了灵活性。 例如，如果使用具有多个资源的资源组，你可能希望筛选到特定的资源类型，并按主题排列结果查询。

## <a name="sample-query-dialog-appearance-behavior"></a>“示例查询”对话框外观行为

如果你是 KQL 专业人员，喜欢直接转到查询编辑器，则可以将查询对话框切换到“关闭”状态。 关闭该开关后，在加载 Log Analytics 屏幕时不会加载“示例查询”对话框。

![示例打开-关闭](media/saved-queries/examples-on-off.png)

始终可以从 Log Analytics 顶栏上的“示例查询”按钮访问示例查询弹出窗口体验。

## <a name="query-explorer"></a>查询资源管理器

目前，查询资源管理器中保存和共享用户生成查询的体验保持未变。

## <a name="next-steps"></a>后续步骤

[KQL 查询入门](get-started-queries.md)

