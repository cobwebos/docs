---
title: Azure 成本管理中的分组和筛选选项
description: 本文介绍如何使用 Azure 成本管理中的分组和筛选选项。
author: bandersmsft
ms.author: banders
ms.date: 06/08/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: f67cc019e8e7d4da4b0db1c0bbfdb6ee1cd495d7
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561863"
---
# <a name="group-and-filter-options-in-cost-analysis"></a>成本分析中的分组和筛选选项

成本分析有很多分组和筛选选项。 本文有助于了解何时使用它们。

若要观看有关分组和筛选选项的视频，请观看 [Cost Management reporting by dimensions and tags](https://www.youtube.com/watch?v=2Vx7V17zbmk)（按维度和标记生成的成本管理报表）视频。 若要观看其他视频，请访问[成本管理 YouTube 频道](https://www.youtube.com/c/AzureCostManagement)。

>[!VIDEO https://www.youtube.com/embed/2Vx7V17zbmk]

## <a name="group-and-filter-properties"></a>分组和筛选属性

下表列出了一些成本分析中可用的最常用分组和筛选选项以及使用它们的时机。

| properties | 何时使用 | 说明 |
| --- | --- | --- |
| **可用性区域** | 按可用性区域细分 AWS 成本。 | 仅适用于 AWS 范围和管理组。 Azure 数据不包含可用性区域，会显示为“不适用”。 |
| **计费周期** | 将 PAYG 成本按已开票或将开票的月份细分。 | 使用“计费周期”可以准确表示已开票的 PAYG 费用。 如果筛选到自定义的日期范围，请在计费周期之前和之后额外包括 2 天。 仅限确切的计费周期日期会与发票不匹配。 会显示计费周期内所有发票的成本。 使用**发票 ID** 筛选特定发票。 仅适用于 PAYG 订阅，因为 EA 和 MCA 按日历月计费。 EA/MCA 帐户可以使用日期选取器中的日历月份或每月粒度来完成同一目标。 |
| **费用类型** | 细分使用量、购买、退款和未使用预留成本。 | 只有在使用实际成本而不使用摊销成本时，预留购买和退款才适用。 只有在查看摊销成本时，未使用预留成本才适用。 |
| **部门** | 按 EA 部门细分成本。 | 仅适用于 EA 和管理组。 PAYG 订阅没有部门，会显示为“不适用”或“未分配”。 |
| **注册帐户** | 按 EA 帐户所有者细分成本。 | 仅适用于 EA 计费帐户、部门和管理组。 PAYG 订阅没有 EA 注册帐户，会显示为“不适用”或“未分配”。 |
| **频率** | 细分基于使用情况的一次性定期成本。 | |
| **发票 ID** | 按计费发票细分成本。 | 未开票费用尚无发票 ID，并且 EA 成本不包含发票详细信息，会显示为“不适用”。  |
| **计量** | 按使用计量细分成本。 | 购买和市场使用情况会显示为“不适用”。 请参阅“收费类型”以确定购买情况，参阅“发布者类型”以确定市场费用。 |
| **操作** | 按运营细分 AWS 成本。 | 仅适用于 AWS 范围和管理组。 Azure 数据不包含运营，会显示为“不适用” - 改用“计量”。 |
| **定价模型** | 按需求、预留或点使用细分成本。 | 购买项显示为 **OnDemand**。 如果看到“不适用”，请按“预留”分组来确定使用是预留还是按需使用，并按“费用类型”分组来确定购买情况。
| **提供程序** | 按 AWS 和 Azure 细分成本。 | 仅适用于管理组。 |
| **发布者类型** | 细分 AWS、Azure 和 Azure 市场成本。 |  |
| **预留** | 按预留细分成本。 | 与预留无关的任何使用或购买都会显示为“不适用”。 按“发布者类型”分组，以确定其他 Azure、AWS 或市场购买。 |
| **资源** | 按资源细分成本。 | 购买显示为“不适用”，因为它们应用于 EA/PAYG 计费帐户或 MCA 计费配置文件级别，不与特定资源关联。 按“发布者类型”分组，以确定其他 Azure、AWS 或市场购买。 |
| **资源组** | 按资源组细分成本。 | 购买、不与订阅关联的租户资源、未部署到资源组的订阅资源以及经典资源都没有资源组，会显示为“其他”、“经典服务”、“$system”或“不适用”。 |
| **资源类型** | 按资源类型细分成本。 | 购买和经典服务没有 Azure 资源管理器资源类型，会显示为“其他”、“经典服务”或“不适用”。 |
| **资源位置** | 按位置或区域细分成本。 | 购买和市场使用情况可能会显示为“未分配”、“未知”、“未映射”或“不适用”。 |
| **服务名称**或**计量类别** | 按 Azure 服务细分成本。 | 购买和市场使用情况会显示为“不适用”或“未分配”。 |
| **服务层级**或**计量子类别** | 按 Azure 使用情况计量子分类细分成本。 | 购买和市场使用情况会显示为“不适用”或“未分配”。 |
| **订阅** | 按 Azure 订阅和 AWS 关联帐户细分成本。 | 购买和租户资源可能会显示为“不适用”。 |
| **标记** | 按特定标记键的标记值细分成本。 | 标记不适用于：购买、不与订阅关联的租户资源、未部署到资源组的订阅资源、经典资源。 某些服务不在使用情况数据中包含标记。 详细了解[每种资源类型的标记支持](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support)。 |

有关术语的详细信息，请参阅[了解在 Azure 使用情况和费用文件中使用的术语](../understand/understand-usage.md)。

## <a name="next-steps"></a>后续步骤

- [开始分析成本](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis)。
