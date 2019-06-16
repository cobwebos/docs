---
title: 查看 Azure 开发测试实验室的每月估计实验室成本趋势 | Microsoft Docs
description: 了解 Azure 开发测试实验室的每月估计成本趋势图表。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: spelluru
ms.openlocfilehash: f761af3a5a3f08e4da89d8869aea5d666ecd69d0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60868202"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>跟踪与 Azure 开发测试实验室中的实验室相关的成本
本文介绍如何跟踪你的实验室的成本。 它演示如何查看估计费用为 trent 实验室在当前日历月。 本文还介绍了如何在实验室中查看每个资源的月-日成本。

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>查看每月估计的实验室成本趋势 
在本部分中，您将学习如何使用**每月估计成本趋势**图表以查看当前日历月的当前日历月的估计的成本至今和预测的月的成本。 你还了解如何通过设置支出目标和阈值，达到触发开发测试实验室向你报告结果时管理实验室成本。

若要查看月评估成本趋势图表，请按以下步骤操作： 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务”  ，并从列表中选择“开发测试实验室”  。
3. 在实验室列表中，选择实验室。  
4. 选择**配置和策略**在左侧菜单中。  
4. 选择**成本趋势**中**成本跟踪**在左侧菜单中的部分。 以下屏幕截图显示了为成本图表示例。 
   
    ![成本图表](./media/devtest-lab-configure-cost-management/graph.png)

    “评估成本”  值为当前日历月中截止目前的成本。 “预计成本”  值是当前整个日历月的评估成本，使用前五天的实验室成本计算得出。

    成本金额会进位到下一个证书。 例如： 

   * 5.01 将计为 6 
   * 5.50 将计为 6
   * 5.99 将计为 6

     如以上图表所述，图表中默认看到的成本是使用[即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)优惠率的估算成本。  还可以通过[管理实验室的成本目标](#managing-cost-targets-for-your-lab)设置在图表中显示的自身支出目标。

     以下成本*不*成本计算中包括：

   * 当前 不支持 CSP 和 Dreamspark 订阅，因为 Azure 开发测试实验室使用不支持 CSP 和 Dreamspark 订阅的 [Azure 计费 API](../billing/billing-usage-rate-card-overview.md)。
   * 套餐费率。 目前，无法使用你与 Microsoft 或 Microsoft 合作伙伴协商的套餐费率（显示于订阅下）。 仅能使用即用即付费率。
   * 税额
   * 折扣
   * 计费货币。 目前，实验室成本仅以美元显示。

### <a name="managing-cost-targets-for-your-lab"></a>管理实验室的成本目标
开发测试实验室可让你设置支出目标来更好地管理实验室成本，随后可在每月估计成本趋势图表中查看该目标。 开发测试实验室还可在达到指定的目标支出或阈值时向你发送通知。 

1. 上**成本趋势**页上，选择**管理目标**。

    ![“管理目标”按钮](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. 上**管理目标**页上，指定支出目标和阈值。 还可以设置是在成本趋势图上还是通过 Webhook 通知报告每个所选阈值。

    ![“管理目标”窗格](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - 选择要跟踪成本目标的时间段。
      - **每月**：每月进行成本目标跟踪。
      - **固定**： 指定在开始和结束日期的日期范围内进行成本目标跟踪。 通常情况下，这些值表示你的项目计划多长时间运行。
   - 指定目标成本。  例如，多少您计划在此实验室上花费您定义的时间段中。
   - 选择启用或禁用任何需报告的阈值 - 增量为 25% - 高达指定目标成本的 125%。 
      - **通知**:达到此阈值时，你会收到通知你指定的 webhook url。
      - **显示在图表上**:达到此阈值时，结果将绘制在可以查看的成本趋势图上，查看月评估成本趋势图表中所述。
   - 如果选择达到阈值时接收通知，须指定 Webhook URL。  在成本集成区域中，选择“单击此处”可添加集成。  输入**Webhook URL**中配置通知窗格，然后选择**确定**。

       ![“配置通知”窗格](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - 如果指定“通知”，须定义 Webhook URL。 
     - 同样，如果定义 Webhook URL，则须在“成本阈值”窗格中将“通知”设置为“开”。  
     - 须在将 Webhook 输入此处之前创建 Webhook。  

       有关 Webhook 的详细信息，请参阅[创建 Webhook 或 API Azure 函数](../azure-functions/functions-create-a-web-hook-or-api-function.md)。 

## <a name="view-cost-by-resource"></a>按资源查看成本 
在实验室中的每月成本趋势功能，可查看你有多少花费在当前日历月中。 它还显示花费数月，根据在过去七天内支出结束前的投影。 若要帮助你了解为何花费在实验室中达到阈值在早期，您可以使用**的资源费用**功能，它会显示的月份日期成本**每个资源**表中。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务”  ，并从列表中选择“开发测试实验室”  。
3. 从实验室列表，选择所需的实验室。  
4. 选择**配置和策略**在左侧菜单中。
5. 选择**的资源费用**中**成本跟踪**在左侧菜单中的部分。 请参阅与实验室关联的每个资源相关的成本。 

    ![按资源划分的成本](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

此功能可帮助你轻松识别的资源的成本最多，以便您可以采取的措施来减少实验室支出。 例如，VM 的成本取决于 VM 的大小。 更大的更多的 VM 大小是成本。 以便可以与要了解为什么需要这种 VM 大小的 VM 所有者可以轻松地找到的 VM 和所有者、 大小以及是否有机会来减小的大小。

[自动关闭策略](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown)可帮助你通过实验室 Vm 关闭在一天中的特定的时间来降低成本。 但是，实验室用户可以选择禁用关闭策略，这会增加运行 VM 的成本。 在此表以了解如果它具有已选择退出自动关闭策略中，可以选择 VM。 如果是这种情况，可以与 VM 所有者来查找为什么 VM 具有已选择扩展的策略。
 
## <a name="next-steps"></a>后续步骤
以下为后续步骤：

* [定义实验室策略](devtest-lab-set-lab-policy.md) - 了解如何设置各种策略，来管理实验室和其 VM 的使用方式。 
* [创建自定义映像](devtest-lab-create-template.md) - 创建 VM 时，指定一个基本映像，可以是自定义映像或市场映像。 本文演示了如何从 VHD 文件创建自定义映像。
* [配置市场映像](devtest-lab-configure-marketplace-images.md) - 开发测试实验室支持创建基于 Azure 市场映像的 VM。 本文展示了如何指定可用于在实验室中创建 VM 的 Azure 市场映像（如果有）。
* [在实验室中创建 VM](devtest-lab-add-vm.md) - 演示了如何从基本映像（自定义或市场映像）创建 VM，以及如何在 VM 中使用项目。

