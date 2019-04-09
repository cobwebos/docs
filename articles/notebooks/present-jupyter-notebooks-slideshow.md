---
title: 以幻灯片放映形式在 Azure 上提供 Jupyter 笔记本
description: 如何在 Jupyter 笔记本中为幻灯片放映模式配置单元格，然后使用 RISE 扩展呈现幻灯片放映。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: c372175b-beb5-4b45-b2f8-34cb06990117
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 405fe71676de311ed7e59ea72798ff4fd2db0f62
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280462"
---
# <a name="run-a-notebook-slideshow"></a>运行笔记本幻灯片放映

Azure Notebooks 使用 Jupyter/IPython 幻灯片放映扩展 (RISE) 进行预配置，该扩展使你可以直接以幻灯片放映形式呈现笔记本。 在幻灯片放映中，单元格通常使用适合在大屏幕上呈现的字号一次显示一个，并且你仍可以运行代码而不是切换到单独的演示计算机。

下图显示标准笔记本视图，在其中可以一起查看 Markdown 和代码单元格：

![标准视图中的笔记本](media/slideshow/slideshow-notebook-view.png)

启动幻灯片放映时，第一个单元格会扩大以填充浏览器，其中左上角的 X 可用于退出幻灯片放映，左下角的 ? 可用于显示键盘快捷方式，而右下角的箭头可用于在幻灯片之间导航：

![幻灯片放映模式中的笔记本](media/slideshow/slideshow-slide-view.png)

为幻灯片放映准备笔记本涉及两个主要活动：

1. 因为使用大字体呈现 Markdown 单元格，所以某些内容可能无法在幻灯片放映中显示。 因而通常会限制任何给定单元格中的文本量；具有四到六行的标题通常效果最佳。 如果有更多文本，则将该信息拆分为多个单元格。

2. 使用幻灯片放映单元格工具栏配置幻灯片放映中每个单元格的行为。 单元格类型确定导航按钮的行为。

## <a name="the-anatomy-of-a-slideshow"></a>幻灯片放映剖析

如果采用随机笔记本并将它用于幻灯片放映，则通常会发现所有单元格都混在一起，并且许多内容会在浏览器窗口底部隐藏。 若要创建有效的演示文稿，因而需要使用幻灯片放映单元格工具栏将幻灯片放映类型分配给每个单元格：

1. 在“视图”菜单上，选择“单元格工具栏” > “幻灯片放映”：

    ![打开单元格幻灯片放映工具栏](media/slideshow/slideshow-view-cell-toolbar.png)

1. “幻灯片类型”下拉列表随即出现在 Notebook 中每个单元格的右上角：

    ![单元格幻灯片放映工具栏](media/slideshow/slideshow-cell-toolbar.png)

1. 对于每个单元格，选择五种类型之一：

    ![单元格幻灯片放映类型](media/slideshow/slideshow-cell-slide-types.png)

    | 幻灯片类型 | 行为 |
    | --- | --- |
    | -（未设置） | 单元格会随上一个单元格一起显示，这通常不是幻灯片放映中的所需效果。 |
    | 幻灯片 | 单元格是主幻灯片，使用导航控件的向左和向右箭头进行导航。 |
    | 子幻灯片 | 单元处于主幻灯片“下方”，使用导航控件的向下箭头进行导航。 向上箭头可返回到主幻灯片。 子幻灯片用于可以在演示文稿主路径中跳过，但是在需要时已准备好使用的辅助材料。 |
    | 片段 | 在使用向下导航箭头时，单元格内容出现在上一张幻灯片或子幻灯片的上下文中（在使用向上箭头时，会删除片段）。 可以将片段与代码单元格一起使用，使该代码出现在幻灯片中，也可以使用多个片段使文本项目符号逐个出现（请参阅下一节中的示例）。 因为片段基于当前幻灯片而构建，所有多余碎片在浏览器窗口底部不可见。 |
    | 跳过 | 单元格不显示在幻灯片放映中。 |
    | 说明 | 单元格包含不显示在幻灯片放映中的演讲者备注。 |

1. 在开始时，对每个单元格选择“幻灯片”会很有帮助。 随后可以运行幻灯片放映并进行相应调整。

### <a name="example-fragment-cells-for-bullet-items"></a>示例：项目符号项的片段单元格

若要使幻灯片上的项目符号逐个出现，请将幻灯片标题置于具有“幻灯片”类型的 Markdown 单元格中，然后将每个项目符号置于具有“片段”类型的单独 Markdown 单元格中：

![为项目符号项创建多个 Markdown 单元格的示例](media/slideshow/slideshow-fragments.png)

因为幻灯片放映呈现片段时所使用的垂直间距多于所有项目符号处于相同单元格中时，所以可能无法使用任意多个项目符号项。

## <a name="run-the-slideshow"></a>运行幻灯片放映

1. 如果编辑了任何 Markdown 单元格，请确保运行它们以呈现其 HTML，否则它们会在幻灯片放映中显示为 Markdown。

1. 为每个单元格配置了“幻灯片类型”之后，选择用于开始幻灯片放映的单元格，然后选择主工具栏上的“进入/退出 RISE 幻灯片放映”按钮：

    ![主工具栏上的“进入/退出 RISE 幻灯片放映”按钮](media/slideshow/slideshow-start.png)

1. 若要在幻灯片以及片段之间导航，请使用导航控件中的向左和向右箭头。 该控件中的文本显示表示 slide.sub-slide 的数字。

    ![幻灯片放映导航控件](media/slideshow/slideshow-navigation-control.png)

1. 若要在幻灯片和子幻灯片以及片段之间导航，请使用向上和向下箭头（如果已启用）：

    ![用于子幻灯片的幻灯片放映导航控件](media/slideshow/slideshow-navigation-control-subslide.png)

1. 在代码单元格上，使用播放按钮运行代码；输出会出现在幻灯片上：

    ![用于运行代码单元格的播放按钮](media/slideshow/slideshow-run-code-cell.png)

    ![代码单元格输出出现在幻灯片放映中](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > 单元格输出被视为幻灯片放映中的单元格的一部分。 如果笔记本或幻灯片放映视图中运行单元格，则输出也会出现在其他视图中。 若要清除输出，请使用“单元格” > “当前输出” > “清除”命令（适用于当前单元格）或“单元格” > “所有输出” > “清除”（适用于所有单元格）。

1. 完成幻灯片放映后，使用“X”可返回笔记本视图。

## <a name="next-steps"></a>后续步骤

- [如何：配置和管理项目](configure-manage-azure-notebooks-projects.md)
- [如何：从笔记本内安装包](install-packages-jupyter-notebook.md)
- [如何：处理数据文件](work-with-project-data-files.md)
- [如何：访问数据资源](access-data-resources-jupyter-notebooks.md)
