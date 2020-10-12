---
title: 使用 Azure Sentinel 中的 Azure Monitor 工作簿可视化数据 |Microsoft Docs
description: 本教程介绍如何使用 Azure Sentinel 中的工作簿可视化数据。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2020
ms.author: yelevin
ms.openlocfilehash: 63a9a6dc9f052c01a7440f616f0baeaab083ad73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843067"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>教程：可视化和监视数据



[将数据源连接](quickstart-onboard.md) 到 Azure Sentinel 后，可以使用采用 Azure Sentinel 的 Azure Monitor 工作簿来可视化和监视数据，这在创建自定义仪表板方面提供了多样性。 虽然工作簿在 Azure Sentinel 中的显示方式有所不同，但有助于你了解如何[使用 Azure Monitor 工作簿创建交互式报表](../azure-monitor/platform/workbooks-overview.md)。 Azure Sentinel 可让你跨数据创建自定义工作簿，并且还附带了内置的工作簿模板，使你可以在连接数据源后快速获得对数据的见解。


本教程可帮助你在 Azure Sentinel 中可视化数据。
> [!div class="checklist"]
> * 使用内置工作簿
> * 创建新工作簿

## <a name="prerequisites"></a>先决条件

- 对于 Azure Sentinel 工作区的资源组，你必须至少具有工作簿读取者或工作簿参与者权限。

> [!NOTE]
> 可在 Azure Sentinel 中查看的工作簿保存在 Azure Sentinel 工作区的资源组中，并由创建它们的工作区进行标记。

## <a name="use-built-in-workbooks"></a>使用内置工作簿

1. 转到“工作簿”，然后选择“模板”以查看 Azure Sentinel 内置工作簿的完整列表。 若要查看与你连接的数据类型相关的数据类型，每个工作簿中“必需的数据类型”字段将在绿色复选标记旁边列出数据类型（如果已将相关数据流式传输到 Azure Sentinel）。
  ![转到工作簿](./media/tutorial-monitor-data/access-workbooks.png)
1. 单击 " **查看模板** "，查看用你的数据填充的模板。
  
1. 若要编辑工作簿，请选择 " **保存**"，然后选择要为模板保存 JSON 文件的位置。 

   > [!NOTE]
   > 这会基于相关模板创建 Azure 资源，并保存工作簿的 JSON 文件，而不是数据。


1. 选择 " **查看保存的工作簿**"。 然后单击顶部的“编辑”按钮。 你现在可以编辑工作簿，并根据需要对其进行自定义。 有关如何自定义工作簿的详细信息，请参阅如何[使用 Azure Monitor 工作簿创建交互式报表](../azure-monitor/platform/workbooks-overview.md)。
![查看工作簿](./media/tutorial-monitor-data/workbook-graph.png)
1. 进行更改后，可以保存工作簿。 

1. 还可以克隆工作簿：选择“编辑”，然后选择“另存为”，确保在同一订阅和资源组下将其另存为其他名称。 这些克隆的工作簿显示在 " **我的工作簿** " 选项卡下。


## <a name="create-new-workbook"></a>创建新的工作簿

1. 若要从头开始创建新工作簿，请选择“工作簿”，然后选择“添加工作簿”。 
  ![显示新工作簿屏幕的屏幕截图。](./media/tutorial-monitor-data/create-workbook.png)

1. 若要编辑工作簿，请选择“编辑”，然后根据需要添加文本、查询和参数。 有关如何自定义工作簿的详细信息，请参阅如何[使用 Azure Monitor 工作簿创建交互式报表](../azure-monitor/platform/workbooks-overview.md)。 

1. 生成查询时，请确保将 " **数据源** " 设置为 " **日志** "，将 " **资源类型** " 设置为 " **Log Analytics**"，然后选择 "相关工作区" (s) 。 

1. 创建工作簿后，保存工作簿，并确保将其保存在 Azure Sentinel 工作区的订阅和资源组下。

1. 如果要让组织中的其他人使用该工作簿，请在“保存到”下选择“共享报表”。 如果希望此工作簿仅供你使用，请选择“我的报表”。

1. 若要在工作区中的工作簿之间切换，可以选择 " **打开**" ![ 图标以打开工作簿。 ](./media/tutorial-monitor-data/switch.png)在任意工作簿的顶部窗格中。 在右侧打开的窗口中，在工作簿之间切换。

   ![切换工作簿](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="how-to-delete-workbooks"></a>如何删除工作簿

若要删除保存的工作簿 (保存的模板或自定义工作簿) ，请在 "工作簿" 页中选择要删除的已保存工作簿，然后选择 " **删除**"。 这将删除已保存的工作簿。

> [!NOTE]
> 这会删除工作簿资源以及对模板所做的任何更改。 原始模板仍可用。

## <a name="next-steps"></a>后续步骤

在本教程中，已学习如何使用 Azure 工作簿在 Azure Sentinel 中直观显示数据。

若要了解如何自动完成对威胁的响应，请参阅[在 Azure Sentinel 中设置自动威胁响应](tutorial-respond-threats-playbook.md)。
