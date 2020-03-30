---
title: Azure 监视器视图设计器到工作簿转换摘要和访问
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: fb53a966b0dc4959253ac0786ef09ef3c497e809
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658840"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>查看设计器以工作簿转换摘要和访问
[视图设计器](view-designer.md)是 Azure 监视器的一项功能，允许您创建自定义视图，以帮助您在日志分析工作区中可视化数据，包括图表、列表和时间线。 它们正在逐步淘汰，代之以工作簿，从而提供了额外的功能。 本文详细介绍了如何创建访问工作簿所需的概述摘要和权限。

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>从 Azure 仪表板创建工作区摘要
视图设计器用户可能熟悉使用概览磁贴来表示一组视图。 要维护可视化概览（如视图设计器工作区摘要），工作簿提供固定步骤，这些步骤可以固定到 Azure[门户仪表板](../../azure-portal/azure-portal-dashboards.md)。 与工作区摘要中的概述磁贴一样，固定工作簿项将直接链接到工作簿视图。

您可以利用 Azure 仪表板提供的高级别自定义功能，这些功能允许对固定项和可视化进行自动刷新、移动、调整和附加筛选。 

![仪表板](media/view-designer-conversion-access/dashboard.png)

创建新的 Azure 仪表板或选择现有仪表板以开始固定工作簿项。

要固定单个项目，您需要为特定步骤启用引脚图标。 为此，请为您的步骤选择相应的 **"编辑"** 按钮，然后选择齿轮图标以打开 **"高级设置**"。 选中"**始终显示此步骤上的引脚图标**"选项，步骤右上角将显示一个引脚图标。 此引脚使您能够将特定可视化效果固定到仪表板，如概览磁贴。

![固定步骤](media/view-designer-conversion-access/pin-step.png)


您可能还希望将工作簿或整个工作簿内容中的多个可视化效果固定到仪表板。 要固定整个工作簿，请在顶部工具栏中选择 **"编辑**"以切换 **"编辑模式**"。 将显示一个固定图标，允许您固定整个工作簿项或工作簿中的所有单个步骤和可视化效果。

![固定所有](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>共享和查看权限 
工作簿的好处可以是私有文档，也可以是共享文档。 默认情况下，保存的工作簿将保存在 **"我的报告"** 下，这意味着只有创建者才能查看此工作簿。

您可以在 **"编辑模式**"中从顶部工具栏中选择 **"共享**"图标来共享工作簿。 系统将提示您将工作簿移动到**共享报表**，这将生成一个链接，提供对工作簿的直接访问权限。

为了使用户查看共享工作簿，他们必须同时有权访问工作簿保存下的订阅和资源组。

![基于订阅的访问](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>后续步骤

- [常见任务](view-designer-conversion-tasks.md)