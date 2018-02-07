---
title: "了解 Azure 成本管理中的成本报表 | Microsoft 文档"
description: "本文可帮助你了解 Cloudyn 报表的基本结构和功能。"
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 38c1313f42a58403e158cad9c2930b6541da5adc
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="understanding-cost-reports"></a>了解成本报表

本文可帮助你了解 Cloudyn 报表的基本结构和功能。 大多数 Cloudyn 报表都较为直观，具有统一的外观。 在阅读本文后，即可使用所有的报表。 许多标准功能在各种报表中都可用，使你能够轻松导航报表。 报表是可自定义的，你可以从多个选项中进行选择以计算和显示结果。

## <a name="report-fields-and-options"></a>报表字段和选项

下面我们来看看时段成本报表示例。 大多数 Cloudyn 报表的布局都比较相似。

![示例报表](./media/understanding-cost-reports/sample-report.png)

上图中每个带有相应编号的区域在以下信息中进行了详细说明：

1. **日期范围**

    使用“日期范围”列表，以便使用预设或自定义来定义报表时间间隔。
2. **保存的筛选器**

    使用“保存的筛选器”列表保存应用于报表的当前组和筛选器。 “保存的筛选器”可用于各种成本和性能报表，包括：

      - 成本分析
      - 分配
      - 资产管理
      - 优化

  键入筛选器名称，然后单击“保存”。

3. **标记**

    使用“标记”区域以按标记类别分组。 菜单中列出的标记是 Azure 部门或成本中心标记，或者它们是 Cloudyn 的成本实体和订阅标记。 选择标记来筛选结果。 你也可以键入要筛选结果的标记名称（关键字）。

    ![选择选项](./media/understanding-cost-reports/select-options.png)

    单击“添加”以添加新的筛选器。

    ![添加筛选器](./media/understanding-cost-reports/add-filter.png)

    标记分组或筛选与 Azure 资源或资源组标记无关。

    成本分摊标记分组和筛选可以在“组”菜单选项中找到。

4. **报表中的组**

    使用“成本分析”报表中的组可显示报表中帐单数据的标准明细类别。  但是，成本分摊报表中的组可以显示基于标记的视图类别。 基于标记的类别是在成本分摊模型中定义的，属于帐单数据中的标准明细类别。

    ![组标记](./media/understanding-cost-reports/groups-tags01.png)

    ![组标记](./media/understanding-cost-reports/groups-tags02.png)

    在成本分摊报表中，基于标记的组类别中的组可能包括：
      - 标记
      - 资源组标记
      - Cloudyn 成本实体标记
      - 用于成本分摊目的的订阅标记类别

  示例可能包括：
     - 成本中心
     - 系
     - Application
     - 环境
     - 成本代码

5. **筛选器**

    使用单选或多选筛选器将范围设置为选定的值。 若要设置筛选器，请单击“添加”，然后选择筛选器类别和值。

6. **成本模型**

    使用成本模型选择以前使用成本分摊 360 创建的成本模型。 你可能有多个 Cloudyn 成本模型，具体要取决于你的成本分摊要求。 某些组织团队的成本分摊要求可能与其他团队的不同。 每个团队可以有自己专用的成本模型。

    有关创建成本分摊模型定义的信息，请参阅[使用自定义标记分摊成本](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs)。

7. **分期付款**

    使用成本分摊报表中的分期付款，可查看非基于用量的服务费用或者一次性应付成本，并已根据服务整个生存期的不同时间段将成本均匀分摊。 一次性费用的示例可能包括：
    - 年度支持费用
    - 年度安全组件费用
    - 保留实例购买费用
    - 某些 Azure Marketplace 项目。

  在“分期付款”下，选择“摊销成本”或“实际成本”。

8. **解决方法**

    “解析度”用于选择所选日期范围内的时间解析度。 时间解析度将确定单位如何在报表中显示，并可以包括：
    - 每日
    - 每周
    - 每月
    - 每季度
    - 每年

9. **分摊规则**

    使用分摊规则来应用或禁用成本分摊成本重新计算。 你可以启用或禁用帐单数据的成本分摊重新计算。 重新计算适用于在报表中所选的类别。 它允许你针对原始帐单数据评估成本分摊重新计算的影响。

10. **未分类**

    使用“未分类”来包含或排除报表中的未分类成本。

11. **显示/隐藏字段**

    “显示/隐藏”选项在报表中没有任何影响。

12.   **显示格式**

    使用“显示格式”可选择各种图或表格视图。

    ![显示格式](./media/understanding-cost-reports/display-formats.png)

13. **多色**

    使用多色可在报表中设置图表的颜色。

14. **操作**

    使用操作可保存、导出或计划报表。

## <a name="next-steps"></a>后续步骤

- 如果尚未完成有关成本管理的第一本教程，请阅读[查看使用情况和成本](tutorial-review-usage.md)。
