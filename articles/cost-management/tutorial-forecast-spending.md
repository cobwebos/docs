---
title: 教程 - 使用 Azure 成本管理预测支出 | Microsoft Docs
description: 本教程介绍如何根据历史使用情况和支出数据预测支出。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: ''
manager: dougeby
ms.openlocfilehash: 411b4797510b26dec43ea7f2232457199808c857
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32162188"
---
# <a name="tutorial-forecast-future-spending"></a>教程：预测将来的支出

可以使用 Azure 成本管理，根据历史使用情况和支出数据预测将来的支出。 可以使用 Cloudyn 报表查看所有成本预测数据。 本教程中的示例将引导你完成使用报表查看成本预测的过程。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 预测将来的支出

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

- 必须具有 Azure 帐户。
- 必须有 Azure 成本管理的试用注册版或付费订阅版。

## <a name="forecast-future-spending"></a>预测将来的支出

Cloudyn 包括了成本预测报表，可帮助你根据在一段时间内的使用情况来预测支出。 它们的主要用途是帮助你确保成本趋势不会超出组织的预期。 你使用的报表是“本月预测成本”和“年度预测成本”。 如果使用情况与过去 30 天的使用情况保持相对一致，则这两个报表都显示预测的将来支出。

“本月预测成本”报表显示你的服务的成本。 它使用月初和上月的成本来显示预测的成本。 在门户顶部的报表菜单中，单击“成本” > “预测和预算” > “本月预测成本”。 下图显示了一个示例。

![本月预测成本](./media/tutorial-forecast-spending/project-month01.png)

在示例中，可以查看哪些服务的支出最高。 Azure 成本低于 AWS 成本。 如果希望查看 Azure VM 的成本预测详细信息，请在“筛选器”列表中选择“Azure/VM”。

![Azure VM 本月预测成本](./media/tutorial-forecast-spending/project-month02.png)

对于你关注的其他服务，按照与前面相同的基本步骤来查看每月成本预测。

“年度预测成本”报表显示你的服务在接下来 12 月的推测成本。

在门户顶部的报表菜单中，单击“成本” > “预测和预算” > “年度预测成本”。 下图显示了一个示例。

![年度预测成本报表](./media/tutorial-forecast-spending/project-annual01.png)

在示例中，可以查看哪些服务的支出最高。 与每月示例一样，Azure 成本低于 AWS 成本。 如果希望查看 Azure VM 的成本预测详细信息，请在“筛选器”列表中选择“Azure/VM”。

![VM 的年度预测成本](./media/tutorial-forecast-spending/project-annual02.png)

在上图中，Azure VM 的年度预测成本为 $28,374。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 预测将来的支出


进入下一个教程，了解如何利用成本分配和 showback 报表来管理成本。

> [!div class="nextstepaction"]
> [利用成本分配和报销报表管理成本](tutorial-manage-costs.md)
