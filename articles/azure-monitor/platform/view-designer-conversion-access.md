---
title: Azure Monitor 查看设计器到工作簿转换摘要和访问
description: ''
author: austonli
ms.author: aul
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: e49b4c0220b6c00fe3a7a6e1cab219364a2a89b7
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171236"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>视图设计器到工作簿转换摘要和访问
[视图设计器](view-designer.md)是 Azure Monitor 的一项功能，它允许您创建自定义视图来帮助您通过图表、列表和时间线将 Log Analytics 工作区中的数据可视化。 它们将被分段并替换为工作簿，这些工作簿提供附加功能。 本文详细介绍了如何创建访问工作簿所需的概述摘要和权限。

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>从 Azure 仪表板创建工作区摘要
视图设计器用户可能熟悉如何使用概述磁贴来表示一组视图。 为了保持视觉对象概述（如视图设计器工作区摘要），工作簿提供了固定的步骤，可以将这些步骤固定到[Azure 门户仪表板](../../azure-portal/azure-portal-dashboards.md)。 与 "工作区摘要" 中的 "概述" 磁贴一样，固定的工作簿项会直接链接到工作簿视图。

你可以利用 Azure 仪表板提供的高级自定义功能，这些功能可自动刷新、移动、调整大小和附加筛选固定项和可视化效果。 

![仪表板](media/view-designer-conversion-access/dashboard.png)

创建新的 Azure 仪表板，或选择现有仪表板以开始固定工作簿项目。

若要固定单个项，需要启用特定步骤的固定图标。 为此，请为你的步骤选择对应的 "**编辑**" 按钮，然后选择齿轮图标以打开 "**高级设置**"。 选中 "**在此步骤中始终显示固定图标**" 选项，并在步骤的右上角显示一个 pin 图标。 此 pin 使你可以将特定的可视化效果固定到仪表板，如概述磁贴。

![固定步骤](media/view-designer-conversion-access/pin-step.png)


你可能还希望将工作簿中的多个可视化效果固定到仪表板，或将整个工作簿内容固定到仪表板。 若要固定整个工作簿，请选择顶部工具栏中的 "**编辑**" 以切换**编辑模式**。 将显示一个 pin 图标，允许您固定工作簿中的整个工作簿项或所有单个步骤和可视化对象。

![全部固定](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>共享和查看权限 
工作簿的优点是专用文档或共享文档。 默认情况下，保存的工作簿将保存在**我的报表**下，这意味着只有创建者才能查看此工作簿。

在**编辑模式下**，可以通过从顶部工具栏中选择**共享**图标来共享工作簿。 系统将提示您将工作簿移至**共享报表**，这将生成一个链接，该链接提供对工作簿的直接访问。

为了使用户能够查看共享工作簿，他们必须能够访问保存该工作簿的订阅和资源组。

![基于订阅的访问](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>后续步骤

- [常见任务](view-designer-conversion-tasks.md)