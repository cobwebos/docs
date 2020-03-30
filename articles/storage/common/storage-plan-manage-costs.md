---
title: 计划和管理 Azure 存储的成本
description: 了解如何在 Azure 门户中使用成本分析来规划和管理 Azure 存储的成本。
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: aa0b789b31f50c8b1ccf5450700874a02ad4664c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304519"
---
# <a name="plan-and-manage-costs-for-azure-storage"></a>计划和管理 Azure 存储的成本

本文介绍如何规划和管理 Azure 存储的成本。 首先，在添加任何资源之前，使用 Azure 定价计算器帮助规划存储成本。 开始使用 Azure 存储资源后，使用成本管理功能来设置预算并监视成本。 您还可以查看预测的成本并监控支出趋势，以确定您可能想要采取行动的领域。

请记住，Azure 存储的成本只是 Azure 帐单中每月成本的一部分。 尽管本文介绍了如何规划和管理 Azure 存储的成本，但会为用于 Azure 订阅的所有 Azure 服务和资源（包括第三方服务）收费。 熟悉 Azure 存储的管理成本后，可以应用类似的方法来管理订阅中使用的所有 Azure 服务的成本。

## <a name="prerequisites"></a>先决条件

成本分析支持各种 Azure 帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](../../cost-management-billing/costs/understand-cost-mgt-data.md)。 若要查看成本数据，你至少需要对 Azure 帐户具有读取访问权限。 若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](../../cost-management-billing/costs/assign-access-acm-data.md)。

## <a name="estimate-costs-before-creating-an-azure-storage-account"></a>在创建 Azure 存储帐户之前估计成本

在创建数据并开始将数据传输到 Azure 存储帐户之前，使用[Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)估计成本。

1. 在[Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)页上，选择 **"存储帐户"** 磁贴。

2. 向下滚动页面并找到估算的 **"存储帐户"** 部分。

3. 从下拉列表中选择选项。 

   当您修改这些下拉列表的值时，成本估计将发生变化。 该估计值出现在上角和估计的底部。 
    
   ![使用成本分析窗格监控成本](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   当您更改 **"类型**"下拉列表的值时，此工作表上显示的其他选项也会更改。 使用 **"更多信息**"部分中的链接详细了解每个选项的含义以及这些选项如何影响与存储相关的操作的价格。 

4. 修改其余选项以查看它们对估计值的影响。

## <a name="use-budgets-and-cost-alerts"></a>使用预算和成本警报

可以创建[预算](../../cost-management-billing/costs/tutorial-acm-create-budgets.md)来管理成本，并创建警报以自动通知利益干系人支出异常和超支风险。 警报基于与预算和成本阈值相比的支出。 为 Azure 订阅和资源组创建预算和警报，因此它们作为总体成本监视策略的一部分非常有用。 但是，它们可能具有管理单个 Azure 服务成本（如 Azure 存储成本）的有限功能，因为它们旨在跟踪更高级别的成本。

## <a name="monitor-costs"></a>监控成本

将 Azure 资源与 Azure 存储一起使用时，会产生成本。 资源使用单位成本因时间间隔（秒、分钟、小时和天）或单位使用情况（字节、兆字节等）而异。一旦开始使用 Azure 存储，就会产生成本。 您可以在 Azure 门户中[的成本分析](../../cost-management-billing/costs/quick-acm-cost-analysis.md)窗格中查看成本。

使用成本分析时，可以在不同时间间隔的图形和表中查看 Azure 存储成本。 一些示例按天、当前和上月以及年份进行。 您还可以根据预算和预测成本查看成本。 随时间转换到较长的视图可以帮助您确定支出趋势，并了解支出可能发生在哪里。 如果您已创建预算，您还可以轻松查看超出预算的超出位置。

要查看成本分析中的 Azure 存储成本：

1. 登录到[Azure 门户](https://portal.azure.com)。

2. 打开**成本管理 + 计费**窗口，从菜单中选择**成本管理**，然后选择**成本分析**。 然后，可以从 **"范围"** 下拉列表中更改特定订阅的范围。

   ![使用成本分析窗格监控成本](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. 要仅查看 Azure 存储的成本，请选择 **"添加筛选器**"，然后选择 **"服务名称**"。 然后，从列表中选择**存储**。 

   下面是一个示例，显示仅 Azure 存储的成本：

   ![使用成本分析窗格监控存储成本](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

在前面的示例中，您可以看到服务的当前成本。 还会显示按 Azure 区域（位置）和资源组显示的成本。  

## <a name="next-steps"></a>后续步骤

详细了解通过[成本分析管理成本](../../cost-management-billing/costs/quick-acm-cost-analysis.md)。

请参阅以下文章，了解有关定价如何与 Azure 存储配合使用的文章：

- [Azure 存储概述定价](https://azure.microsoft.com/pricing/details/storage/)
- [借助预留容量优化 Blob 存储的成本](../blobs/storage-blob-reserved-capacity.md)
