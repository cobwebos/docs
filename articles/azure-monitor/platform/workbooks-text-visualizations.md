---
title: Azure Monitor 工作簿文本可视化
description: 了解所有 Azure Monitor 工作簿文本可视化。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: e8f2d9495484b781b26962c2946b5bada6c38b4c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89663789"
---
# <a name="text-visualizations"></a>文本可视化

工作簿允许作者在其工作簿中包含文本块。 文本可以是遥测数据的人工分析、用于帮助用户解释数据的信息、部分标题，等等。

[![Apdex 文本表的屏幕截图](./media/workbooks-text-visualizations/apdex.png)](./media/workbooks-text-visualizations/apdex.png#lightbox)

文本是通过 Markdown 控件添加的，该控件提供完全的格式控制。 其中包括不同的标题和字体样式、超链接、表等。

编辑模式：

![编辑模式下的文本步骤的屏幕截图。](./media/workbooks-text-visualizations/text-edit-mode.png)

预览模式：

!["预览" 选项卡上的 "编辑" 模式下的文本步骤的屏幕截图。](./media/workbooks-text-visualizations/text-edit-mode-preview.png)

## <a name="add-a-text-control"></a>添加文本控件

1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 使用“添加文本”链接将一个文本控件添加到工作簿。
3. 在编辑器字段中添加 Markdown。
4. 使用 " *文本样式* " 选项可在使用 Azure 门户的标准信息/警告/成功/错误样式的纯 markdown 和 markdown 进行切换。
5. 使用 " **预览** " 选项卡可以查看内容的外观。 编辑时，预览将显示滚动条区域内的内容以限制其大小;但是，在运行时，markdown 内容将展开以填充所需的所有空间，无滚动条。
6. 选择 " **完成编辑** " 按钮，完成编辑步骤。

> [!TIP]
> 使用此 [Markdown 速查表](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)了解不同的格式设置选项。

## <a name="text-styles"></a>文本样式

以下文本样式可用于文本步骤：

| Style     | 说明                                                                               |
|-----------|-------------------------------------------------------------------------------------------|
| `plain`   | 不应用其他格式设置。                                                      |
| `info`    | 门户的 "info" 样式，具有  `ℹ` 或类似图标，一般为蓝色背景。      |
| `error`   | 门户的 "错误" 样式，具有 `❌` 或类似图标，一般为红色背景。     |
| `success` | 门户的 "成功" 样式，具有 `✔` 或类似图标和一般绿色背景。  |
| `upsell`  | 门户的 "追加" 样式，具有 `🚀` 或类似图标，一般为紫色。 |
| `warning` | 门户的 "警告" 样式，具有 `⚠` 或类似图标，一般为蓝色背景。   |

您还可以选择文本参数作为样式的源，而不是选择特定样式。 参数值必须是上述文本值之一。 缺少值或不能识别的任何值都将被视为 `plain` 样式。

信息样式示例：

![信息样式的外观的屏幕截图。](./media/workbooks-text-visualizations/text-preview-info-style.png)

警告样式示例：

![警告样式的外观的屏幕截图。](./media/workbooks-text-visualizations/text-warning-style.png)

## <a name="next-steps"></a>后续步骤

* 了解如何 [在工作簿中创建图表](workbooks-chart-visualizations.md)。
* 了解如何 [在工作簿中创建网格](workbooks-grid-visualizations.md)。
