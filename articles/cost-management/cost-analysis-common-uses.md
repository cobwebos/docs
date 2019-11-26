---
title: 常见成本分析在 Azure 成本管理中使用
description: 本文介绍如何在 Azure 成本管理中获取常见成本分析任务的结果。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/16/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: ''
ms.openlocfilehash: 01087f3e40e278490abb87d0ab3b7b6ab5052b6b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219489"
---
# <a name="common-cost-analysis-uses"></a>常见成本分析使用

Azure 成本管理用户通常需要对许多其他人提出的问题的解答。 本文逐步讲解如何在成本管理中获取常见成本分析任务的结果。

## <a name="view-cost-breakdown-by-azure-service"></a>查看 Azure 服务的成本细分

通过 Azure 服务查看成本，可帮助你更好地了解你的基础结构中最大开销的部分。 例如，VM 计算成本可能较小。 不过，由于从 Vm 发出的信息量，您可能会产生大量的网络成本。 了解 Azure 服务的主要成本驱动因素至关重要，这样您就可以根据需要调整服务使用。

1. 在 Azure 门户中，导航到你的作用域的成本分析。 例如：**成本管理 + 计费** > **成本管理** > **成本分析**。
1. 选择 "**按服务成本**"，然后按**服务层**分组。
1. 将视图更改为 "**表**"。

![Azure 服务的成本细分](./media/cost-analysis-common-uses/breakdown-by-service.png)

## <a name="view-cost-breakdown-by-azure-resource"></a>查看 Azure 资源的成本细分

服务是用 Azure 资源生成的。 查看基于资源的成本可帮助您快速确定主要的成本贡献者。 如果服务的资源太昂贵，可以考虑进行更改以降低成本。

1. 在 Azure 门户中，导航到你的作用域的成本分析。 例如：**成本管理 + 计费** > **成本管理** > **成本分析**。
1. 选择 "**按资源成本**"。
1. 将视图更改为 "**表**"。

![查看 Azure 资源的成本细分](./media/cost-analysis-common-uses/cost-by-resource.png)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>查看所选维度的成本细分

维度使你可以基于费用中显示的各种元数据值来组织成本。 例如，可以按位置对成本进行分组。

1. 在 Azure 门户中，导航到你的作用域的成本分析。 例如：**成本管理 + 计费** > **成本管理** > **成本分析**。
1. 选择 "**分组依据**" 筛选器。  
    ![按项选择组](./media/cost-analysis-common-uses/group-by.png)
1. （可选）保存该视图供以后使用。
1. 单击图形下方的饼图以查看更详细的数据。  
    ![查看所选维度的成本细分](./media/cost-analysis-common-uses/drill-down.png)

## <a name="view-costs-per-day-or-by-month"></a>查看每日或每月的成本

查看每日和每月成本可帮助您更好地了解您的成本是在一周还是一年中。 如果你在节假日期间有更多的客户流量，这会导致 Azure 成本的相应增加？ 星期五是否比 Monday 更昂贵？

1. 在 Azure 门户中，导航到你的作用域的成本分析。 例如：**成本管理 + 计费** > **成本管理** > **成本分析**。
1. 将**粒度**设置为**每月**或**每日**。

![查看每日成本](./media/cost-analysis-common-uses/daily-granularity.png)

## <a name="view-costs-for-a-specific-tag"></a>查看特定标记的成本

许多 Azure 用户会将标记应用到其资源，如成本中心或开发环境（生产和测试），以更好地分类费用。 标记在成本分析中显示为维度。 您可以使用该维度深入了解自定义标记分类。

1. 在 Azure 门户中，导航到你的作用域的成本分析。 例如：**成本管理 + 计费** > **成本管理** > **成本分析**。
1. 为标记选择 "**分组依据**"。

![查看特定标记的成本](./media/cost-analysis-common-uses/tag.png)

## <a name="download-your-usage-details"></a>下载使用情况详细信息

"使用情况详细信息" 报告文件（CSV 格式）提供了向发票计费的所有费用的明细。 您可以使用报表将它与您的发票进行比较和更好地了解。 发票上的每个计费费用对应于使用情况报表中的细分费。

1. 在 Azure 门户中，导航到计费帐户或订阅的 "**使用情况和费用**" 选项卡。 例如：**成本管理 + 计费** > **计费** > **用量和费用**。
1. 选择要从其下载的行项目，然后单击下载符号。  
    ![下载用量和费用](./media/cost-analysis-common-uses/download1.png)
1.  选择要下载的使用情况文件。  
    ![选择要下载的使用情况文件](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>查看每月 EA 成本细分

你的 EA 注册为你的整个组织累算费用。 了解成本如何随时间推移而开票，有助于您与相应的利益干系人合作，确保成本是管理的。

1. 在 Azure 门户中，导航到 "**成本管理 + 计费** > **概述**"。
1. 单击 "当前月**细目**" 并查看你的货币承诺刻录。  
    ![EA 成本概述-细目汇总](./media/cost-analysis-common-uses/breakdown1.png)
1.  单击 "**使用情况和费用**" 选项卡，然后查看所选 timespan 中的上个月细目。  
    ![使用情况和费用 "选项卡](./media/cost-analysis-common-uses/breakdown2.png)

## <a name="view-enrollment-monthly-cost-by-term"></a>按字词查看每月注册成本

使用注册的每月费用的图形视图来了解给定期间的成本趋势和开票金额。

1. 在 Azure 门户中，导航到你的作用域的成本分析。 例如：**成本管理 + 计费** > **成本管理** > **成本分析**。
1. 选择注册并设置注册术语。
1. 将粒度设置为 "每月"，然后将 "视图" 设置为 "**列（堆积）** "。

您可以对数据进行分组和筛选，以便进行更详细的分析。

![每月注册成本（按条款）](./media/cost-analysis-common-uses/enrollment-term1.png)

## <a name="view-ea-enrollment-accumulated-costs"></a>查看 EA 注册累计成本

查看一段时间内的净累计费用，以了解你的组织在给定时间段内的总支出。

1. 在 Azure 门户中，导航到你的作用域的成本分析。 例如：**成本管理 + 计费** > **成本管理** > **成本分析**。
1. 选择注册，然后查看当前累计成本。

![注册累计成本](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)

## <a name="next-steps"></a>后续步骤
- 如果尚未完成有关成本管理的第一个快速入门，请阅读[开始分析成本](quick-acm-cost-analysis.md)。
- 阅读[成本管理文档](index.yml)。
