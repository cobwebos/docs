---
title: 通过预购优化 Azure Databricks 成本
description: 了解如何预付 Azure Databricks 费用，以便通过预留容量来节省资金。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: b000934bb218c21a454ed04afe4e9a4495e808ab
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200378"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>通过预购优化 Azure Databricks 成本

如果预购一到三年的 Azure Databricks 提交单位 (DBCU)，则可节省 Azure Databricks 单位 (DBU) 成本。 在购买期限内，可以随时使用预购的 DBCU。 与 VM 不同，预购单位不会按小时到期，可以在购买期限内随时使用它们。

任何 Azure Databricks 使用量都会自动从预购的 DBU 中扣除。 若要获取预购折扣，无需重新部署预购计划，也无需将预购计划分配到 Azure Databricks 工作区以使用 DBU。

预购折扣仅适用于 DBU 使用情况。 计算、存储和网络等其他费用单独收费。

## <a name="determine-the-right-size-to-buy"></a>确定要购买的适当大小

Databricks 预购适用于所有 Databricks 工作负荷和层。 可以将预购视为预付费 Databricks 提交单位池。 无论工作负荷或层如何，都会从池中扣除使用量。 使用量将按以下比率扣除：

| **“工作负荷”** | **DBU 应用比率 - 标准层** | **DBU 应用比率 - 高级层** |
| --- | --- | --- |
| 数据分析 | 0.4 | 0.55 |
| 数据工程 | 0.15 | 0.30 |
| 轻量数据工程 | 0.07 | 0.22 |

例如，当使用一定数量的“数据分析 - 标准层”时，预购的 Databricks 提交单位将扣除 0.4 个单位。

在购买之前，请针对不同工作负荷和层级计算消耗的总 DBU 数量。 使用前面的比率针对 DBCU 进行规范化，然后针对下一个一年或三年运行总使用量预测。

## <a name="purchase-databricks-commit-units"></a>购买 Databricks 提交单位

可以在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D)中购买 Databricks 计划。 若要购买预留容量，你必须拥有至少一个企业订阅的“所有者”角色。

- 你必须在至少一个企业协议（套餐编号：MS-AZR-0017P 或 MS-AZR-0148P）或 Microsoft 客户协议或采用即用即付费率的个人订阅中担任所有者角色（套餐编号：MS-AZR-0003P 或 MS-AZR-0023P）。
- 对于 EA 订阅，必须在 EA 门户中启用“添加预留实例”选项。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。
- 对于企业订阅，必须在 [EA 门户](https://ea.azure.com/)中启用“添加预留实例”  。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。

**若要进行购买，请执行以下操作：**

1. 转到 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D)。
1. 选择一个订阅。 使用“订阅”列表，选择用于支付预留容量费用的订阅。  将向订阅的付款方式收取预留容量的前期成本。 从注册的货币承诺余额中扣除费用或作为超额收取费用。
1. 选择一个范围。 使用“范围”列表来选择订阅范围： 
    - **单个资源组范围** - 仅将预留折扣应用到所选资源组中匹配的资源。
    - **单个订阅范围** - 将预留折扣应用到所选订阅中匹配的资源。
    - **共享范围** - 将预留折扣应用到计费上下文中符合条件的订阅中的匹配资源。 对于企业协议客户，计费上下文为注册。
1. 选择要购买多少 Azure Databricks 提交单位，然后完成购买。


![一个示例，显示 Azure 门户中的 Azure Databricks 购买](./media/prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>更改范围和所有权

可以在购买后对预留进行以下类型的更改：

- 更新预留范围
- 基于角色的访问权限

不能拆分或合并 Databricks 提交单位预购。 若要详细了解如何管理预留，请参阅[在购买后管理预留](manage-reserved-vm-instance.md)。

## <a name="cancellations-and-exchanges"></a>取消和更换

Databricks 预购计划不支持取消和交换操作。 所有购买都是最终购买。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
  - [什么是 Azure 预订？](save-compute-costs-reservations.md)
  - [了解如何应用 Azure Databricks 预购 DBCU 折扣](reservation-discount-databricks.md)
  - [了解企业合约的预留使用情况](understand-reserved-instance-usage-ea.md)
