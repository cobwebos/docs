---
title: Azure 监视器视图设计器到工作簿转换选项
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 7bfa831332451718c0c9c05023b90104d2b8b02b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658704"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Azure 监视器视图设计器到工作簿转换选项
[视图设计器](view-designer.md)是 Azure 监视器的一项功能，允许您创建自定义视图，以帮助您在日志分析工作区中可视化数据，包括图表、列表和时间线。 它们正在逐步淘汰，代之以提供额外功能的工作簿。 本文比较了两者之间的基本概念和将视图转换为工作簿的选项。

## <a name="basic-workbook-designs"></a>基本工作簿设计

视图设计器具有固定的静态表示风格，而工作簿允许自由地包括和修改数据的表示方式。 下图描述了在转换视图时如何排列工作簿的两个示例。

[垂直工作簿](view-designer-conversion-examples.md#vertical)
![垂直](media/view-designer-conversion-options/view-designer-vertical.png)

[选项卡式工作簿](view-designer-conversion-examples.md#tabbed)
![数据类型分发](media/view-designer-conversion-options/distribution-tab.png)
![选项卡 随时间的数据类型选项卡](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>磁贴转换
视图设计器使用概览磁贴功能表示和汇总总体状态。 这些表示在七个磁贴中，从数字到图表不等。 在工作簿中，用户可以创建类似的可视化效果，并将其固定为类似于概述磁贴的原始样式。 

![库](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>查看仪表板转换
查看设计器磁贴通常由两个部分组成：可视化效果和与可视化数据匹配的列表，例如 **"圆环&列表"** 磁贴。

![圆环图](media/view-designer-conversion-options/donut-example.png)

使用工作簿，我们允许用户选择查询视图的一个或两个部分。 在工作簿中编写查询是一个简单的两步过程。 首先，从查询生成数据，其次，数据呈现为可视化效果。  如何在工作簿中重新创建此视图的示例如下：

![转换](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>后续步骤
- [访问工作簿&权限](view-designer-conversion-access.md)