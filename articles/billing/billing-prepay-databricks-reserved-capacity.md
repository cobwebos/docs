---
title: 优化预购买 Azure Databricks 成本
description: 了解如何可以针对 Azure Databricks 费用预付具有预留容量以节省资金。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: banders
ms.openlocfilehash: 99eb4de86aa227d558bec54d011a0b1548d27cf0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811253"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>优化预购买 Azure Databricks 成本

可以将它们保存在 Azure Databricks 上时预购买 Azure Databricks 单位 (DBU) 成本为一年或三年提交单位 (DBCU)。 可以在购买期限内随时使用预先购买的 DBCUs。 与不同的 Vm，提前购买的单位按小时永不过期，并在购买的期限内随时使用。

任何 Azure Databricks 使用会自动减去从提前购买 dbu 进行计费。 您不必重新部署或将提前购买的计划分配到 Azure Databricks 工作区以获取 DBU 使用情况以获取预购买折扣。

预购折扣仅适用于 DBU 使用情况。 计算、 存储和网络等其他费用将单独收费。

## <a name="determine-the-right-size-to-buy"></a>确定要购买的正确大小

Databricks 预购买适用于所有 Databricks 工作负荷和层。 您可以将预购作为的预付费 Databricks 提交单元池。 使用情况会从该池，而不考虑工作负荷或层中。 使用情况扣除的以下比率：

| **工作负载** | **DBU 应用程序比-标准层** | **DBU 应用程序比-高级层** |
| --- | --- | --- |
| 数据分析 | 0.4 | 0.55 |
| 数据工程 | 0.15 | 0.30 |
| 数据工程轻量 | 0.07 | 0.22 |

例如，当数量的数据分析 – 使用标准层时，提前购买的 Databricks 提交单位扣除 0.4 单位。

在购买之前，计算不同的工作负荷和层所耗用的总 DBU 数量。 使用前面的比率为 DBCU 规范化，然后运行下一次或三年的总使用率投影。

## <a name="purchase-databricks-commit-units"></a>购买 Databricks 提交单位

您可以购买 Databricks 计划[Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D)。 若要购买保留的容量，必须具有至少一个企业订阅的所有者角色。

- 目前，预先购买仅适用于企业协议客户。
- 您必须至少一个企业订阅的所有者角色。
- 对于企业订阅，必须在 [EA 门户](https://ea.azure.com/)中启用“添加预留实例”  。 或者，如果禁用了该设置，你必须是订阅的 EA 管理员。

**若要购买：**

1. 转到 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D)。
1. 选择一个订阅。 使用**订阅**列表来选择用于支付的预留容量的订阅。 订阅的付款方式进行收费的预留容量的预付费用。 从注册的货币承诺余额中扣除费用或作为超额收取费用。
1. 选择作用域。 使用**作用域**列表选择订阅范围内：
    - **单一资源组范围**— 将预订折扣应用于在所选的资源组中匹配的资源。
    - **单一订阅范围内**— 将预订折扣应用于所选订阅中匹配的资源。
    - **共享范围**-预订折扣适用于匹配符合条件的订阅中计费上下文中的资源。 对于企业协议客户，计费上下文是注册。
1. 选择你想要购买并完成购买多少 Azure Databricks 提交单位。


![在 Azure 门户中显示 Azure Databricks 采购示例](./media/billing-prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>更改作用域和所有权

对预订购买后，可以进行以下类型的更改：

- 更新预订范围
- 基于角色的访问

您无法拆分或合并 Databricks 提交单元预购买。 有关管理预订的详细信息，请参阅[购买后管理预订](billing-manage-reserved-vm-instance.md)。

## <a name="cancellations-and-exchanges"></a>取消和更换

Databricks 预购买计划不支持取消和 exchange。 所有购买都不可更改。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
  - [什么是 Azure 预订？](billing-save-compute-costs-reservations.md)
  - [了解如何应用 Azure Databricks 预购买 DBCU 折扣](billing-reservation-discount-databricks.md)
  - [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
