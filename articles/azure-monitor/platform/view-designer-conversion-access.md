---
title: Azure Monitor 视图设计器到工作簿的转换摘要和访问
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 7614dd1d60bad5c124269ae2af02d30a5aacfe3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90564026"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>视图设计器到工作簿的转换摘要和访问
[视图设计器](view-designer.md)是 Azure Monitor 的一项功能，它让你能够创建自定义视图，帮助你通过图表、列表和时间线将 Log Analytics 工作区中的数据可视化。 这些元素已逐步淘汰，取而代之的是除这些功能外还可提供其他功能的工作簿。 本文详细介绍如何创建访问工作簿所需的概述摘要和权限。

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>从 Azure 仪表板创建工作区摘要
视图设计器用户可能知道如何使用概述磁贴来表示一组视图。 为了保留可视概述（例如视图设计器工作区摘要），工作簿提供了可以固定到 [Azure 门户仪表板](../../azure-portal/azure-portal-dashboards.md)的固定步骤。 与工作区摘要中的概述磁贴一样，固定的工作簿项会直接链接到工作簿视图。

可以利用 Azure 仪表板随附的高级自定义功能，来执行自动刷新、移动、大小调整，以及对固定的项和可视化效果进行附加的筛选。 

![屏幕截图显示名为 "工作区摘要" 的自定义 Azure 仪表板。](media/view-designer-conversion-access/dashboard.png)

创建新的 Azure 仪表板或选择现有的仪表板，以开始固定工作簿项。

若要固定单个项，需要为特定的步骤启用图钉图标。 为此，请选择步骤对应的“编辑”按钮，然后选择齿轮图标打开“高级设置”。  选中“此步骤始终显示图钉图标”选项，步骤的右上角随即会显示一个图钉图标。 使用此图钉图标可将特定的可视化效果固定到仪表板，就如同固定概述磁贴一样。

![固定步骤](media/view-designer-conversion-access/pin-step.png)


你可能还想要将工作簿中的多个可视化效果或整个工作簿内容固定到仪表板。 若要固定整个工作簿，请在顶部工具栏中选择“编辑”，以切换到“编辑模式”。  此时会显示一个图钉图标，用于固定工作簿中的整个工作簿项或所有各个步骤和可视化对象。

![全部固定](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>共享和查看权限 
工作簿可以是专用文档或共享文档，这是它的一个优势。 默认情况下，保存的工作簿将保存在“我的报表”下，意味着只有创建者才能查看此工作簿。

可以通过在“编辑模式”下从顶部工具栏中选择“共享”图标来共享工作簿。  系统会提示你将工作簿移到“共享报表”，这会生成一个可用于直接访问工作簿的链接。

用户若要查看共享的工作簿，他们必须有权访问该工作簿所保存到的订阅和资源组。

![基于订阅的访问](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>后续步骤

- [常见任务](view-designer-conversion-tasks.md)