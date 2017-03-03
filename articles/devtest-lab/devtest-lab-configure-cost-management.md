---
title: "查看 Azure 开发测试实验室中月评估成本趋势 | Microsoft Docs"
description: "了解 Azure 开发测试实验室中的月评估成本趋势图表。"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2d428e0e3aaf8fd4a2138648411da644ccd308f6
ms.openlocfilehash: b3ad1ead522908d4b41b7cca98d20ac91664998e
ms.lasthandoff: 02/08/2017


---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>查看 Azure 开发测试实验室中月评估成本趋势
开发测试实验室的成本管理功能有助于追踪实验室成本。 本文将演示如何使用**月评估成本趋势**图表，查看当前日历月中截止目前的成本以及当前日历月截止月底的预估成本。 通过本文可以了解如何在 Azure 门户中查看月评估成本趋势图表。

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>查看月评估成本趋势图表
若要查看月评估成本趋势图表，请按以下步骤操作： 

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 选择“更多服务”，然后从列表中选择“开发测试实验室”。
3. 从实验室列表中，选择所需的实验室。   
4. 在实验室的边栏选项卡上，选择“成本设置”。
5. 在本实验的“成本设置”边栏选项卡，选择“实验室成本趋势”。
6. 以下屏幕截图为成本图表示例。 
   
    ![成本图表](./media/devtest-lab-configure-cost-management/graph.png)

“评估成本”值为当前日历月中截止目前的成本。 “预计成本”值是当前整个日历月的评估成本，使用前五天的实验室成本计算得出。

成本金额会进位到下一个证书。 例如： 

* 5.01 将计为 6 
* 5.50 将计为 6
* 5.99 将计为 6

如图表上方所述，图表中出现的成本是使用*即用即付*优惠率的评估[](https://azure.microsoft.com/offers/ms-azr-0003p/)成本。
此外，以下未计入成本计算：

* 当前 不支持 CSP 和 Dreamspark 订阅，因为 Azure 开发测试实验室使用不支持 CSP 和 Dreamspark 订阅的 [Azure 计费 API](../billing/billing-usage-rate-card-overview.md)。
* 优惠费率。 目前，我们无法使用你与 Microsoft 或 Microsoft 合作伙伴协商的优惠费率（显示于订阅下方）。 我们会使用即付即用费率。
* 税额
* 折扣
* 计费货币。 目前，实验室成本仅以美元显示。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>相关的博客文章
* [要追踪开发测试实验室成本须知的另两件事](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [为什么要有成本阈值？](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>后续步骤
以下为后续步骤：

* [定义实验室策略](devtest-lab-set-lab-policy.md) - 了解如何设置各种策略，来管理实验室和其 VM 的使用方式。 
* [创建自定义映像](devtest-lab-create-template.md) - 创建 VM 时，指定一个基本映像，可以是自定义映像或应用商店映像。 本文演示了如何从 VHD 文件创建自定义映像。
* [配置应用商店映像](devtest-lab-configure-marketplace-images.md) - 开发测试实验室支持创建基于 Azure 应用商店映像的 VM。 本文展示了如何指定可用于在实验室中创建 VM 的 Azure 应用商店映像（如果有）。
* [在实验室中创建 VM](devtest-lab-add-vm-with-artifacts.md) - 演示了如何从基本映像（自定义或应用商店映像）创建 VM，以及如何使用在 VM 中使用项目。


