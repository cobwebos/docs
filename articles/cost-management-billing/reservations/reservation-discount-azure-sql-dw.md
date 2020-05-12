---
title: 预留折扣如何应用于 Azure Synapse Analytics | Microsoft Docs
description: 了解预留折扣如何应用于 Azure Synapse Analytics 以帮助你节省资金。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: banders
ms.openlocfilehash: 33a0a0a62814579dee10ae046338ff9f78d667cb
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82627069"
---
# <a name="how-reservation-discounts-apply-to-azure-synapse-analytics"></a>预留折扣如何应用于 Azure Synapse Analytics

购买 Azure Synapse Analytics 预留容量后，预留折扣会自动应用于该区域中存在的已预配实例。 预留折扣应用于 Azure Synapse Analytics cDWU 计量器指示的用量。 存储和网络按照即用即付费率进行收费。

## <a name="reservation-discount-application"></a>预留折扣应用

Azure Synapse Analytics 预留容量折扣按小时应用于正在运行的仓库。 如果在一个小时内没有部署仓库，则该小时的保留容量将浪费。 它不会结转。

购买后，所购预留将匹配到在任何时间点正在运行的仓库所指示的 Azure Synapse Analytics 用量。 如果关闭了某些仓库，则预留折扣将自动应用于任何其他匹配仓库。

对于未运行整一小时的仓库，预留将自动应用于该小时内的其他匹配实例。

## <a name="discount-examples"></a>折扣示例

以下示例展示了 Azure Synapse Analytics 预留容量折扣如何根据部署进行应用。

- **示例 1**：你购买了 5 个单位的 100 cDWU 保留容量。 你运行了一个 DW1500c Azure Synapse Analytics 实例一小时。 在这种情况下，将针对 15 个单位的 100 cDWU 使用量发出用量。 预留折扣适用于所使用的 5 个单位。 对于所使用的其余 10 个单位的 100 cDWU 使用量，将按即用即付费率向你收费。 换句话说，多个预留可能只实现了部分涵盖。

- **示例 2**：你购买了 5 个单位的 100 cDWU 保留容量。 你运行了两个 DW100c Azure Synapse Analytics 实例一小时。 在这种情况下，对于 1 个单位的 100 cDWU 使用量将发出两个使用事件。 这两个使用事件都将获得保留容量折扣。 剩余的 3 个单位的 100 cDWU 保留容量将浪费，并且无法结转以供将来使用。 换句话说，单个预留可以匹配到多个 Azure Synapse Analytics 实例。

- **示例 3**：你购买了 1 个单位的 100 cDWU 保留容量。 你运行了两个 DW100c Azure Synapse Analytics 实例。 每个实例运行 30 分钟。 在这种情况下，这两个使用事件都将获得保留容量折扣。 按照即用即付费率不收取使用费。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

- 如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [什么是 Azure 预订？](save-compute-costs-reservations.md)
- [查看预留交易](view-reservations.md)
- [通过 API 获取预留交易和利用率](reservation-apis.md)
- [管理预留](manage-reserved-vm-instance.md)
