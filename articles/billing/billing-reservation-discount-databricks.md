---
title: 如何应用 Azure Databricks 预购买折扣
description: 了解 Azure Databricks 预购买折扣适用于你的使用情况的方式。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: banders
ms.openlocfilehash: 7c1855b587ab1d603e9c6ac24a67b0f50065361f
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827644"
---
# <a name="how-azure-databricks-pre-purchase-discount-is-applied"></a>如何应用 Azure Databricks 预购买折扣

可以在购买期限内随时使用预先购买的 Azure Databricks 提交单位 (DBCU)。 使用情况是任何 Azure Databricks 会自动减去从提前购买 DBCUs。

与不同的 Vm，提前购买的单位按小时永不过期。 您可以在购买的期限内随时使用它们。 若要获取预购买折扣，不需要重新部署或将提前购买的计划分配到 Azure Databricks 工作区以获取使用情况。

预购折扣仅适用于 Azure Databricks 单位 (DBU) 使用情况。 计算、 存储和网络等其他费用将单独收费。

## <a name="pre-purchase-discount-application"></a>折扣预购买应用程序

Databricks 预购买适用于所有 Databricks 工作负荷和层。 您可以将预购作为的预付费 Databricks 提交单元池。 使用情况会从该池，而不考虑工作负荷或层中。 使用情况扣除的以下比率：

| **工作负载** | **DBU 应用程序比-标准层** | **DBU 应用程序比-高级层** |
| --- | --- | --- |
| 数据分析 | 0.4 | 0.55 |
| 数据工程 | 0.15 | 0.30 |
| 数据工程轻量 | 0.07 | 0.22 |

例如时使用的数据分析 – 标准层数量，提前购买的 Databricks 提交单位是由 0.4 单位中扣除。 当数量的数据工程 Light – 使用标准层时，预先购买的 Databricks 提交单位扣除 0.07 单位

## <a name="determine-plan-use"></a>确定计划使用

若要确定 DBCU 计划使用，请转到 Azure 门户 >**预订**单击已购买的 Databricks 计划。 在利用率为-显示日期与任何剩余单位。 有关确定预订详细信息的使用，请参阅[看到预订使用情况](billing-reservation-apis.md#see-reservation-usage)一文。

## <a name="how-discount-application-shows-in-usage-data"></a>折扣应用程序中使用情况数据的显示方式

时预购买折扣适用于你 Databricks 的使用情况，按需费用显示为零的使用情况数据。 有关保留成本和使用情况的详细信息，请参阅[获取企业协议保留成本和用量](billing-understand-reserved-instance-usage-ea.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何管理预留，请参阅[管理 Azure 预留](billing-manage-reserved-vm-instance.md)。
- 若要了解有关预购买 Azure Databricks 以节省资金的详细信息，请参阅[优化 Azure Databricks 成本与预购买](billing-prepay-databricks-reserved-capacity.md)。
- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
  - [什么是 Azure 预订？](billing-save-compute-costs-reservations.md)
  - [管理 Azure 中的预留](billing-manage-reserved-vm-instance.md)
  - [了解即用即付费率使用订阅的预订使用情况](billing-understand-reserved-instance-usage.md)
  - [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
