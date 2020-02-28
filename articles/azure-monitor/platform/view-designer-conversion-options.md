---
title: Azure Monitor 查看设计器到工作簿的转换选项
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 7bfa831332451718c0c9c05023b90104d2b8b02b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658704"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Azure Monitor 查看设计器到工作簿的转换选项
[视图设计器](view-designer.md)是 Azure Monitor 的一项功能，它允许您创建自定义视图来帮助您通过图表、列表和时间线将 Log Analytics 工作区中的数据可视化。 它们将被分段并替换为提供其他功能的工作簿。 本文比较了两个选项之间的基本概念，以及用于将视图转换为工作簿的选项。

## <a name="basic-workbook-designs"></a>基本工作簿设计

视图设计器具有固定的静态表示形式，而工作簿可以自由地包含和修改数据的表示方式。 下图描绘了两个示例，说明如何在转换视图时排列工作簿。

垂直
![垂直[工作簿](view-designer-conversion-examples.md#vertical)](media/view-designer-conversion-options/view-designer-vertical.png)

选项卡[式工作簿](view-designer-conversion-examples.md#tabbed)
![数据类型分布 "选项卡](media/view-designer-conversion-options/distribution-tab.png)
" 一段时间 "选项卡 ![数据类型](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>磁贴转换
视图设计器使用概述磁贴功能来表示和汇总总体状态。 它们以七个磁贴表示，范围从数字到图表。 在工作簿中，用户可以创建类似的可视化效果，并将其固定为类似于 "概述" 磁贴的原始样式。 

![库](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>查看仪表板转换
视图设计器磁贴通常由两部分组成：可视化对象和与可视化对象中的数据相匹配的列表（例如，**环形 & 列表**磁贴）。

![环形](media/view-designer-conversion-options/donut-example.png)

在工作簿中，允许用户选择查询视图的一个或两个部分。 在工作簿中构建查询是一个简单的两步过程。 首先，数据是通过查询生成的，第二个数据呈现为可视化。  如何在工作簿中重新创建此视图的示例如下所示：

![转换](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>后续步骤
- [& 权限访问工作簿](view-designer-conversion-access.md)