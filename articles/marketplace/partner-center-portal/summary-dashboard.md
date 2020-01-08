---
title: 商业应用商店中合作伙伴中心分析的摘要仪表板
description: 了解如何从合作伙伴中心的 "摘要" 仪表板访问用于汇总 marketplace 活动的图表、趋势和值。
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: e20f3d156df139cfaf1935bae7fc7babd3e35fc5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480354"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>商业 Marketplace 分析中的摘要仪表板

本文提供了有关合作伙伴中心中的 "摘要" 仪表板的信息。 此仪表板显示汇总数据的图形、趋势和值，汇总了产品/服务的 marketplace 活动。

若要访问摘要仪表板，请在 "商业应用商店" 下打开 " **[分析" 仪表板](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** 。

>[!NOTE]
> 有关分析术语的详细定义，请参阅[商业 Marketplace 分析的常见问题和术语](./faq-terminology.md)。

## <a name="summary-dashboard"></a>“摘要”仪表板

"**摘要**" 仪表板提供基于每个产品/服务类型的概述。 **见解**一目了然地显示关键信息，并提供产品/服务销售活动的广泛视图。 您可以使用 "**摘要**" 仪表板可视化这些报表。 本文详细介绍了以下每个元素：

- [日期范围](#date-range)
- [摘要部分](#summary-section)
- [按地理位置划分的客户](#customers-by-geography)
- [增长趋势图表](#growth-trend-charts)
- [客户排行榜](#customer-leaderboard)
- [座位数趋势](#seat-count-trend)
- [免费试用 SaaS 订单趋势](#free-trials-saas-orders-trend)

## <a name="elements-of-the-summary-dashboard"></a>"摘要" 仪表板的元素

以下各节介绍如何使用 "摘要" 仪表板以及如何读取数据。

### <a name="date-range"></a>日期范围

可以在每个页面的右上角找到日期范围选择。 通过选择基于过去3、6或12个月的日期范围，或选择最长持续时间为12个月的自定义日期范围，自定义**摘要**页面图形的输出。 默认日期范围为6个月。

![合作伙伴中心分析仪表板](./media/analyze-dashboard.png)

### <a name="summary-section"></a>“摘要”部分

"摘要" 部分显示在所选日期范围内创建的所有订单、获得的客户和使用情况的计数。 部分当前月将从这些指标的计算中排除。 例如：如果你选择了6分钟时间范围，则将计算当月前六个月的使用时间。 如果选择自定义日期范围，将从计算中排除部分金额。

!["摘要" 仪表板中的增长趋势](./media/summary-summary-section.png)

#### <a name="reading-the-summary-section"></a>读取摘要部分

- **订单**：到目前为止发布的产品/服务的所有采购订单（未取消订单）的计数。
- **客户**：购买产品/服务的所有客户的计数，并且至少有一个未取消的订单。
- **规范化使用时间**：定义为用于确定虚拟机内核数的使用时间（[vm 内核数] x [原始使用时间]）。 指定为 "SHAREDCORE" 的 Vm 使用1/6 （或0.1666）作为 [VM 内核数] 乘数。
- **原始使用时间**： vm 的运行时间（以小时为单位）。 "**总订单**"、"**客户总数**"、"**标准化使用小时数**"、"**原始使用时间**"、"**页面访问**数" 和 "**操作操作**" 旁边的百分比值表示所选日期范围（[上个月使用情况–第一个月使用率]）/第月使用情况下的使用量增长量。 如上所述，将从此指标中排除当月的部分量。
- **增长趋势**：如果将鼠标悬停在图表的列上，条形图将显示每个月的值。
- **向上的绿色三角形**：指示正增长趋势。
- **向下的红色三角形**：指示相对于上个月的负增长趋势。

### <a name="customers-by-geography"></a>按地理位置划分的客户

"**按地理位置**划分的客户热度地图" 显示世界地图上的客户计数。

!["摘要" 仪表板中的 "地域" 客户](./media/summary-customers-by-geography.png)

- 可以移动地图来查看确切的位置。
- 可以缩放到特定位置。
- 热度地图有一个补充网格，用于查看特定位置中客户计数、订单计数、规范化使用时间的详细信息。
- 您可以在网格中搜索和选择国家/地区，以便缩放到地图中的位置。 按图中的 "**主页**" 按钮，还原到原始视图。
- **新**客户在所选日期范围内的第一个月内第一次购买了你的产品/服务。

### <a name="growth-trend-charts"></a>增长趋势图表

根据所选的日期范围，你可以根据所**购买订单**的增长量（包括已取消的订单）、**获取的客户**（包括丢失的客户）和**使用情况**报告的趋势来查看趋势。 您可以通过选择图表下方的链接来进一步分析这些趋势，导航到相应的**订单**、**使用情况**、**客户**或**Marketplace 见解**页面。

应用商店在两个选项卡上显示 Azure Marketplace 和 AppSource 的**页面访问和行动**趋势图表。

![页面访问和在 "摘要" 仪表板中调用操作趋势图](./media/summary-page-visits-and-cta.png)

"**按产品提供订单**" 图表根据产品/服务名称来组织订单。

"**按销售渠道的订单**" 饼图按销售渠道在所选日期范围内组织订单（包括客户已取消的订单）。 销售渠道是客户用来购买 Azure （云解决方案提供商（CSP）、企业版、企业版、企业版、企业版、转售版和即付即用的许可协议类型。

**销售渠道**饼图的**产品/服务**和使用情况的使用情况分别显示了主要产品/服务的使用情况和销售渠道。 内部饼图表示原始使用量，而外部饼图表示规范化的使用情况。

按**marketplace**许可证类型和**使用情况按 marketplace 许可证类型**饼图显示订单和使用情况按各自的许可证类型的细分。 许可证类型包括：

- **通过 Azure 计费**：你选择使用此许可证类型出售你的产品/服务时，会代表你向 microsoft 计费客户。 支付类型包括即用即付-通过信用卡或企业开具发票。
- **自带许可**： Microsoft 不会向客户收取此类 marketplace 产品/服务的使用费用。 此使用情况列为 marketplace **（免费）** 。
- **免费**：对于使用此类 marketplace 产品/服务的客户，Microsoft 不会对其进行计费。 此使用情况在 marketplace 中列为**免费试用版**。
- **Microsoft as 经销商**：代表 microsoft 经销商作为**云解决方案提供商（CSP）计划**的一部分销售的产品/服务。

### <a name="customer-leaderboard"></a>客户排行榜

具有最高订单数的排名靠前的50客户显示在*领导板*上，按最高订单计数和订单百分比排序。

- 选择客户以查看其配置文件详细信息、按产品/服务组织的订单或按 Azure 许可证类型和定价通道组织的订单。
- "**按订单**列出的产品/服务" 圆环图显示了前四个产品/服务（按订单计数），其余的产品/服务以 "全部 Rest" 的形式分组。
- "**产品/服务" 的规范化使用**方式按使用情况提供前5个产品/服务。

> [!NOTE]
> 仅当客户提供同意时才会显示客户个人信息。 如果已使用**所有者**角色权限级别登录，则可以查看此信息。 具有**参与者**角色的用户将无法查看此信息。 [了解有关用户角色和权限的详细信息](./manage-account.md#define-user-roles-and-permissions)。

### <a name="seat-count-trend"></a>座位数趋势

按**每个客户/站点**列出的订单图表显示根据定价模型购买的所有订单的明细。 **座位数量趋势**图显示了针对所有每个客户软件即服务（SaaS）产品/服务购买的座位。

### <a name="free-trials-saas-orders-trend"></a>免费试用 SaaS 订单趋势

**免费试用 saas 订单趋势**图显示了免费试用版 saas 产品/服务的订单趋势，期限为30天。

## <a name="next-steps"></a>后续步骤

- 有关合作伙伴中心商用 Marketplace 中可用的分析报表的概述，请参阅[合作伙伴中心的专业市场分析](./analytics.md)。
- 有关以图形和可下载格式显示的订单的信息，请参阅[商业 Marketplace 分析中的 "订单" 仪表板](./orders-dashboard.md)。
- 对于虚拟机（VM）提供使用情况和计量计费指标，请参阅[商业 Marketplace 分析中的使用情况仪表板](./usage-dashboard.md)。
- 有关客户的详细信息，包括增长趋势，请参阅[商业 Marketplace 分析中的客户仪表板](./customer-dashboard.md)。
- 有关过去30天内下载请求的列表，请参阅[商业 Marketplace 分析中的下载仪表板](./downloads-dashboard.md)。
- 若要查看有关 Azure Marketplace 和 AppSource 上的产品/服务的客户反馈的合并视图，请参阅[商业 marketplace 分析中的评级和评论仪表板](./ratings-reviews.md)。
- 有关商业 Marketplace 分析的常见问题和全面的数据术语字典，请参阅[商业市场分析的常见问题和术语](./faq-terminology.md)。
