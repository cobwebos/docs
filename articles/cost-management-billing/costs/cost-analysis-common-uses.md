---
title: 常见成本分析在 Azure 成本管理中的使用
description: 本文介绍如何在 Azure 成本管理中获取常见成本分析任务的结果。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: ''
ms.openlocfilehash: 756e2f275a92a31a99604aaf7ef880bfdce8149b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "75988508"
---
# <a name="common-cost-analysis-uses"></a>常见成本分析的使用

Azure 成本管理用户通常需要那些对许多其他人提出的问题的解答。 本文详细介绍如何在成本管理中获取常见成本分析任务的结果。

## <a name="view-cost-breakdown-by-azure-service"></a>查看按 Azure 服务进行的成本细分

通过 Azure 服务查看成本，可以更好地了解基础结构中的最大开销部分。 例如，VM 计算成本可能较小。 不过，由于从 VM 发出的信息量大，你可能会产生大量的网络成本。 了解 Azure 服务的主要成本驱动因素至关重要，这样你就可以根据需要调整服务的使用。

1. 在 Azure 门户中，导航到与范围相对应的成本分析。 例如：“成本管理 + 计费” > “成本管理” > “成本分析”。   
1. 选择“按服务划分的成本”  ，然后按“服务层”分组  。
1. 将视图更改为“表”。 

![按 Azure 服务进行的成本细分](./media/cost-analysis-common-uses/breakdown-by-service.png)

## <a name="view-cost-breakdown-by-azure-resource"></a>查看按 Azure 资源进行的成本细分

服务是用 Azure 资源生成的。 查看基于资源的成本有助于快速确定主要的成本贡献者。 如果服务的资源太昂贵，可以考虑进行更改以降低成本。

1. 在 Azure 门户中，导航到与范围相对应的成本分析。 例如：“成本管理 + 计费” > “成本管理” > “成本分析”。   
1. 选择“按资源划分的成本”。 
1. 将视图更改为“表”。 

![查看按 Azure 资源进行的成本细分](./media/cost-analysis-common-uses/cost-by-resource.png)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>查看按所选维度进行的成本细分

有了维度，就可以基于费用中显示的各种元数据值来组织成本。 例如，可以按位置对成本分组。

1. 在 Azure 门户中，导航到与范围相对应的成本分析。 例如：“成本管理 + 计费” > “成本管理” > “成本分析”。   
1. 选择“分组依据”筛选器。   
    ![选择一个“分组依据”项](./media/cost-analysis-common-uses/group-by.png)
1. （可选）保存该视图供以后使用。
1. 单击图形下方的饼图，查看更详细的数据。  
    ![查看按所选维度进行的成本细分](./media/cost-analysis-common-uses/drill-down.png)

## <a name="view-costs-per-day-or-by-month"></a>查看每日或每月成本

查看每日和每月成本可以更好地了解在一周或一年中，是否某个时间的成本较高。 如果节假日期间有更多的客户流量，是否会导致 Azure 成本的相应增加？ 星期五是否比星期一成本更高？

1. 在 Azure 门户中，导航到与范围相对应的成本分析。 例如：“成本管理 + 计费” > “成本管理” > “成本分析”。   
1. 将“粒度”设置为“每月”或“每日”。   

![查看每日成本](./media/cost-analysis-common-uses/daily-granularity.png)

## <a name="view-costs-for-a-specific-tag"></a>查看特定标记的成本

许多 Azure 用户会将标记应用到其资源，如成本中心或开发环境（生产和测试），以便更好地进行费用分类。 标记在成本分析中显示为维度。 可以使用该维度来了解自定义标记分类。

对标记的支持适用于在将标记应用于资源后报告的使用情况  。 标记不会逆向应用于成本汇总。

1. 在 Azure 门户中，导航到与范围相对应的成本分析。 例如：“成本管理 + 计费” > “成本管理” > “成本分析”。   
1. 针对标记选择“分组依据”  。

![查看特定标记的成本](./media/cost-analysis-common-uses/tag.png)

## <a name="download-your-usage-details"></a>下载使用情况详细信息

使用情况详细信息报表文件（CSV 格式）提供了发票的所有应计费用的明细。 可以使用报表与发票进行比较，更好地了解发票。 发票上的每项计费对应于使用情况报表中的细分费用。

1. 在 Azure 门户中，导航到计费帐户或订阅的“使用情况和费用”  选项卡。 例如：“成本管理 + 计费”   > “计费”   >   “使用情况 + 费用”。
1. 选择要从其下载内容的行项目，然后单击下载符号。  
    ![下载使用情况和费用](./media/cost-analysis-common-uses/download1.png)
1.  选择要下载的使用情况文件。  
    ![选择要下载的使用情况文件](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>查看每月 EA 成本细分

EA 注册累积整个组织的成本。 了解成本如何累积以及如何按时间开票，这样有助于你与相应的利益干系人合作，确保对成本进行负责的管理。

1. 在 Azure 门户中，导航到“成本管理 + 计费”   >   “概览”。
1. 单击当月的“细目”  ，查看货币承诺尚未完成的部分。  
    ![EA 成本概述 - 细目汇总](./media/cost-analysis-common-uses/breakdown1.png)
1.  单击“使用情况和费用”  选项卡，查看所选时间范围内的上个月的细目。  
    ![“使用情况和费用”选项卡](./media/cost-analysis-common-uses/breakdown2.png)

## <a name="view-enrollment-monthly-cost-by-term"></a>按期限查看每月注册成本

使用注册的每月成本的图形视图，了解给定期间的成本趋势和开票金额。

1. 在 Azure 门户中，导航到与范围相对应的成本分析。 例如：“成本管理 + 计费” > “成本管理” > “成本分析”。   
1. 选择注册并设置注册期限。
1. 将粒度设置为“每月”，然后将视图设置为“列     (堆叠)”。 

可以对数据进行分组和筛选，以便进行更详细的分析。

![按期限划分的每月注册成本](./media/cost-analysis-common-uses/enrollment-term1.png)

## <a name="view-ea-enrollment-accumulated-costs"></a>查看 EA 注册累计成本

查看一段时间内的净累计费用，了解组织在给定时间段内的总支出。

1. 在 Azure 门户中，导航到与范围相对应的成本分析。 例如：“成本管理 + 计费” > “成本管理” > “成本分析”。   
1. 选择注册，然后查看当前的累计成本。

![注册累计成本](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)

## <a name="next-steps"></a>后续步骤
- 如果尚未完成有关成本管理的第一个快速入门，请阅读[开始分析成本](quick-acm-cost-analysis.md)。
- 阅读[成本管理文档](../index.yml)。
