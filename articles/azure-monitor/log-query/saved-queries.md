---
title: Azure Monitor 中保存的查询 Log Analytics
description: 可以从启动查询，并根据需要进行修改
ms.subservice: logs
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 06/16/2020
ms.openlocfilehash: 462358ab967d4ba26a265d7b5f401b995223815e
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959836"
---
# <a name="saved-queries-in-azure-monitor-log-analytics"></a>Azure Monitor 中保存的查询 Log Analytics

Log Analytics 提供了一组示例查询，你可以自行运行这些查询，或将其用作你自己的查询的起点。 本文介绍了示例查询以及如何使用它们。

如果你不熟悉 Log Analytics 或 KQL 查询语言，则可以使用示例查询。 它们可以提供对资源的即时深入了解，并提供一种更好的方法来开始学习和使用 KQL，从而缩短开始使用 Log Analytics 所用的时间。 我们收集并250特选了多个示例查询，这些查询旨在提供即时值，并且该数量的示例查询不断增长。

## <a name="in-context-queries"></a>上下文内查询

新体验筛选并建议在上下文中进行查询。 换句话说，系统只会自动显示与所选作用域相关的查询。

- 对于**单个资源**–按资源类型筛选查询。
- 对于**资源组**，将根据特定资源组中的资源对查询进行筛选。
- 对于**工作区**–根据工作区中安装的解决方案筛选查询。

此行为对于所有 Log Analytics 作用域都是一致的。 如果看不到所需资源类型的示例查询，则可能是由于筛选器导致了上下文中的筛选器。 后面的部分介绍如何删除上下文内范围以便查看所有可能的查询。

> [!TIP]
> 作用域中的资源越多，门户筛选和显示 "示例查询" 对话框所需的时间就越长。

## <a name="example-query-user-interface"></a>查询用户界面示例

可以从两个不同的位置获取示例查询。

### <a name="example-query-dialog"></a>示例查询对话框

当你首次输入 Log Analytics 体验时，将自动显示 "*示例查询" 对话框*。  还可以通过在**示例查询**屏幕的右上角单击来访问它。

![边栏](media/saved-queries/sidebar-2.png)

此时将显示 "示例查询" 对话框，如下所示：  

![示例查询屏幕](media/saved-queries/example-query-start.png)

前面的屏幕截图显示了 Azure Key Vault 实例的日志屏幕。 如本文前面所述，查询显示在上下文中。  因此，屏幕截图只显示 Key Vault 相关的示例。 如果选择整个订阅，则会显示该订阅中所有资源类型的查询。  

每个示例查询都由一个卡表示。 您可以快速浏览查询以查找您需要的内容。 您可以直接从对话框中运行查询，也可以选择将其加载到查询编辑器，以便进行更细致的微调和调整。

### <a name="sidebar-query-experience"></a>边栏查询体验

可以从 Log Analytics 左侧边栏中的 "查询" 窗格中访问所有相同的对话体验功能。 您可以将鼠标悬停在查询名称上以获取查询说明和其他功能。

![边栏](media/saved-queries/sidebar-3.png)

## <a name="finding-and-filtering-queries"></a>查找和筛选查询

此部分中的选项可用于对话和边栏查询体验，但用户界面略有不同。  

### <a name="use-favorites"></a>使用收藏夹

你可以将经常使用的查询收藏在一起，使你能够更快地进行访问。

> [!TIP]
> 稍后收集和查看查询是一种开始使用的好方法。 找到所需的查询，然后单击查询旁的星号将其添加到收藏夹。 如果以后发现查询对您来说并不有用，则可以取消对它的兴趣。  

### <a name="filtering-and-group-by"></a>筛选和分组依据

查询对话框体验文件系统只显示具有正确上下文的查询，您可以选择删除筛选器并查看所有查询。

### <a name="group-by"></a>Group by

单击 "*分组依据*" 下拉列表，更改查询的分组：

![示例查询屏幕 groupby](media/saved-queries/example-query-groupby.png)

此对话框支持分组依据：

- **资源类型**-Azure 中定义的资源，例如虚拟机。 请参阅[Azure Monitor 表参考](/azure/azure-monitor/reference/tables/tables-resourcetype)，了解 Azure Monitor 日志/Log Analytics 表到资源类型的完整映射。  
- **类别**–一种类型的信息，如*安全*或*审核*。 类别与 "表" 窗格中定义的类别完全相同。 有关类别的完整列表，请参阅[Azure Monitor 表引用](/azure/azure-monitor/reference/tables/tables-category)。  
- **解决方案**-与查询关联的 Azure Monitor 解决方案
- **主题**–示例查询（例如*活动日志*或*应用日志*）的主题。 主题属性是示例查询独有的，可能会因特定的资源类型而有所不同。

分组值还用作活动目录。 单击屏幕左侧的某个值会将查询视图向右滚动到单击的项。

### <a name="filter"></a>筛选器

你还可以根据前面提到的 groupby 值筛选查询。 在 "示例查询" 对话框中，在顶部找到筛选器。

![示例查询屏幕筛选器](media/saved-queries/example-query-filter.png)

### <a name="combining-group-by-and-filter"></a>组合组和筛选器

筛选器和分组依据功能旨在一起工作。 它们在排列查询方面提供了灵活性。 例如，如果使用具有多个资源的资源组，则可能需要筛选到特定的资源类型，并按主题排列结果查询。

## <a name="sample-query-dialog-appearance-behavior"></a>示例查询对话外观行为

如果你是 KQL pro，并愿意直接转到查询编辑器，则可以将 "关闭" 查询对话框。 关闭该开关时，"示例查询" 对话框不会在加载 Log Analytics 屏幕时加载。

![启用示例](media/saved-queries/examples-on-off.png)

您始终可以从 Log Analytics 顶部栏中的 "*示例查询*" 按钮访问示例查询弹出窗口。

## <a name="query-explorer"></a>查询资源管理器

用于保存和共享用户生成的查询的查询资源管理器体验在时间上保持不变。

## <a name="next-steps"></a>后续步骤

[KQL 查询入门](get-started-queries.md)

