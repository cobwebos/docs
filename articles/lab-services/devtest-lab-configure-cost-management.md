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
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 13535dae82ef2c8896dad7d6221553d15e4e6a95
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38573805"
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>查看 Azure 开发测试实验室的每月估计实验室成本趋势
开发测试实验室的成本管理功能有助于追踪实验室成本。 本文将演示如何使用**月评估成本趋势**图表，查看当前日历月中截止目前的成本以及当前日历月截止月底的预估成本。 本文还演示了如何通过设置支出目标和阈值更好地管理实验室成本，当达到阈值时会触发开发测试实验室向你报告结果。

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>查看月评估成本趋势图表
若要查看月评估成本趋势图表，请按以下步骤操作： 

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 如果需要，请选择“所有服务”，并从列表中选择“开发测试实验室”。 （实验室可能已显示在“所有资源”下的“仪表板”上）。
1. 从实验室列表，选择所需的实验室。  
1. 在实验室的“概览”区域中，选择“配置和策略”。   
1. 在“成本跟踪”左侧，选择“成本趋势”。

   以下屏幕截图为成本图表示例。 
   
    ![成本图表](./media/devtest-lab-configure-cost-management/graph.png)

“评估成本”值为当前日历月中截止目前的成本。 “预计成本”值是当前整个日历月的评估成本，使用前五天的实验室成本计算得出。

成本金额会进位到下一个证书。 例如： 

* 5.01 将计为 6 
* 5.50 将计为 6
* 5.99 将计为 6

如以上图表所述，图表中默认看到的成本是使用[即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)优惠率的估算成本。 还可以通过[管理实验室的成本目标](#managing-cost-targets-for-your-lab)设置在图表中显示的自身支出目标。

此外，以下未计入成本计算：

* 当前 不支持 CSP 和 Dreamspark 订阅，因为 Azure 开发测试实验室使用不支持 CSP 和 Dreamspark 订阅的 [Azure 计费 API](../billing/billing-usage-rate-card-overview.md)。
* 套餐费率。 目前，无法使用你与 Microsoft 或 Microsoft 合作伙伴协商的套餐费率（显示于订阅下）。 仅能使用即用即付费率。
* 税额
* 折扣
* 计费货币。 目前，实验室成本仅以美元显示。

## <a name="managing-cost-targets-for-your-lab"></a>管理实验室的成本目标
开发测试实验室可让你设置支出目标来更好地管理实验室成本，随后可在每月估计成本趋势图表中查看该目标。 开发测试实验室还可在达到指定的目标支出或阈值时向你发送通知。 

1. 在实验室的“概览”窗格中，选择“配置和策略”。
1. 在“成本跟踪”左侧，选择“成本趋势”。

    ![“管理目标”按钮](./media/devtest-lab-configure-cost-management/cost-trend.png)

1. 在“成本趋势”窗格中，选择“管理目标”。

    ![“管理目标”按钮](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)

1. 在“管理目标”窗格中，指定所需支出目标和阈值。 还可以设置是在成本趋势图上还是通过 Webhook 通知报告每个所选阈值。

    ![“管理目标”窗格](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - 选择要跟踪成本目标的时间段。
      - **每月**：每月进行成本目标跟踪。
      - **固定**：在开始日期和结束日期字段中指定的日期范围内跟踪成本目标。 通常情况下，这可能与项目计划运行的时长相对应。
   - 指定目标成本。 例如，可能是计划在定义时间段内用于本实验的费用。
   - 选择启用或禁用任何需报告的阈值 - 增量为 25% - 高达指定目标成本的 125%。
      - **通知**：达到阈值时，可从你指定的 Webhook URL 获得通知。
      - **绘制在图表上**：达到此阈值时，结果会绘制在可以查看的成本趋势图上，如[查看月预估成本趋势图表](#viewing-the-monthly-estimated-cost-trend-chart)所述。
   - 如果选择达到阈值时接收通知，须指定 Webhook URL。 在成本集成区域中，选择“单击此处”可添加集成。

      在“配置通知”窗格中输入 Webhook URL，然后选择“确定”。

       ![“配置通知”窗格](./media/devtest-lab-configure-cost-management/configure-notification.png)

      - 如果指定“通知”，须定义 Webhook URL。
      - 同样，如果定义 Webhook URL，则须在“成本阈值”窗格中将“通知”设置为“开”。
      - 须在将 Webhook 输入此处之前创建 Webhook。  

      有关 Webhook 的详细信息，请参阅[创建 Webhook 或 API Azure 函数](../azure-functions/functions-create-a-web-hook-or-api-function.md)。 
 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>相关的博客文章
* [要追踪开发测试实验室成本须知的另两件事](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [为什么要有成本阈值？](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>后续步骤
以下为后续步骤：

* [定义实验室策略](devtest-lab-set-lab-policy.md) - 了解如何设置各种策略，来管理实验室和其 VM 的使用方式。 
* 
    [创建自定义映像](devtest-lab-create-template.md) - 创建 VM 时，指定一个基本映像，可以是自定义映像或市场映像。 本文演示了如何从 VHD 文件创建自定义映像。
* 
    [配置市场映像](devtest-lab-configure-marketplace-images.md) - 开发测试实验室支持创建基于 Azure 市场映像的 VM。 本文展示了如何指定可用于在实验室中创建 VM 的 Azure 市场映像（如果有）。
* 
    [在实验室中创建 VM](devtest-lab-add-vm.md) - 演示了如何从基本映像（自定义或市场映像）创建 VM，以及如何在 VM 中使用项目。

