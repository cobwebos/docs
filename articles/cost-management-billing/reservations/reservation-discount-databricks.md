---
title: 如何应用 Azure Databricks 预购折扣
description: 了解 Azure Databricks 预购折扣如何适用于你的使用情况。
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c148351a4475bfdbee474a5e0951cc3b5717404e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995723"
---
# <a name="how-azure-databricks-pre-purchase-discount-is-applied"></a>如何应用 Azure Databricks 预购折扣

可以在购买期限内随时使用预购的 Azure Databricks 提交单位 (DBCU)。 任何 Azure Databricks 使用量都会自动从预购的 DBCU 中扣除。

与 VM 不同，预购单位不会按小时到期。 在购买期限内随时都可以使用它们。 若要获取预购折扣，无需重新部署预购计划，也无法将预购计划分配到 Azure Databricks 工作区以供使用。

预购折扣仅适用于 Azure Databricks 单位 (DBU) 使用情况。 计算、存储和网络等其他费用单独收费。

## <a name="pre-purchase-discount-application"></a>预购折扣应用

Databricks 预购适用于所有 Databricks 工作负荷和层。 可以将预购视为预付费 Databricks 提交单位池。 无论工作负荷或层如何，都会从池中扣除使用量。 使用量将按以下比率扣除：

| **“工作负荷”** | **DBU 应用比率 - 标准层** | **DBU 应用比率 - 高级层** |
| --- | --- | --- |
| 数据分析 | 0.4 | 0.55 |
| 数据工程 | 0.15 | 0.30 |
| 数据工程轻量 | 0.07 | 0.22 |

例如，当使用一定数量的“数据分析 - 标准层”时，预购的 Databricks 提交单位将扣除 0.4 个单位。 例如，当使用一定数量的“轻量数据工程 - 标准层”时，预购的 Databricks 提交单位将扣除 0.07 个单位

## <a name="determine-plan-use"></a>确定计划使用情况

若要确定 DBCU 计划的使用情况，请转到 Azure 门户>“预留”并单击已购买的 Databricks 计划。 到目前为止的利用率将与任何剩余的单位一起显示。 有关确定预留使用情况的详细信息，请参阅[查看预留使用情况](reservation-apis.md#see-reservation-usage)一文。

## <a name="how-discount-application-shows-in-usage-data"></a>折扣应用在使用情况数据中的显示方式

当预购折扣应用于 Databricks 使用量时，按需费用在使用情况数据中显示为零。 有关预留成本和使用情况的详细信息，请参阅[获取企业协议预留成本和使用情况](understand-reserved-instance-usage-ea.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何管理预留，请参阅[管理 Azure 预留](manage-reserved-vm-instance.md)。
- 若要详细了解预购 Azure Databricks 如何节省资金，请参阅[通过预购优化 Azure Databricks 成本](prepay-databricks-reserved-capacity.md)。
- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
  - [什么是 Azure 预订？](save-compute-costs-reservations.md)
  - [管理 Azure 中的预留](manage-reserved-vm-instance.md)
  - [了解采用即用即付费率的订阅的预留使用情况](understand-reserved-instance-usage.md)
  - [了解企业合约的预留使用情况](understand-reserved-instance-usage-ea.md)
