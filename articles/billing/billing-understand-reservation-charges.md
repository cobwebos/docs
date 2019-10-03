---
title: 了解 Azure SQL 数据库的预留折扣 | Microsoft Docs
description: 了解如何对运行中的 Azure SQL 数据库应用预留折扣。
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2019
ms.author: banders
ms.openlocfilehash: d7a1a451bf40a09b1bd9b9d823e0778d335a690b
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71701786"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-sql-databases"></a>如何对 Azure SQL 数据库应用预留折扣

购买 Azure SQL 数据库预留容量后，预留折扣会自动应用到与预留属性和数量匹配的 SQL 数据库。 预留涵盖 SQL 数据库的计算成本。 将按标准费率收取软件、存储和网络费用。 可以通过 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)来包含 SQL 数据库的许可成本。

对于虚拟机预留实例，请参阅[了解 Azure 虚拟机预留实例折扣](billing-understand-vm-reservation-charges.md)。

## <a name="how-reservation-discount-is-applied"></a>如何应用预留折扣

预留折扣为“使用或丢失”  。 因此，如果你在任何小时内没有匹配资源，那么你将丢失该小时的预留数量。 不能结转未使用的预留小时数。

关闭资源时，预留折扣将自动应用于指定范围内的另一个匹配资源。 如果在指定的范围内找不到匹配的资源，则预留小时数将丢失  。

## <a name="discount-applied-to-sql-databases"></a>应用到 SQL 数据库的折扣

 SQL 数据库预留容量折扣按小时应用到正在运行的 SQL 数据库。 购买的预留容量将与运行中 SQL 数据库产生的计算用量进行匹配。 对于不是整小时运行的 SQL 数据库，预留将自动应用到与预留属性匹配其他 SQL 数据库。 折扣可以应用到同时运行的 SQL 数据库。 如果与预留属性匹配的 SQL 数据库不是整小时运行，则无法获得该小时的完整预留折扣权益。

以下示例演示如何根据购买的核心数目以及 SQL 数据库的运行时间，来应用 SQL 数据库预留容量折扣。

- 方案 1：为 8 核 SQL 数据库购买了 SQL 数据库预留容量。 运行一个与剩余预留属性匹配的 16 核 SQL 数据库。 将按 8 个核心的 SQL 数据库计算用量收取即用即付费用。 将获得 8 核 SQL 数据库计算用量的一小时预留折扣。

剩余的示例假设购买的 SQL 数据库预留容量用于 16 核 SQL 数据库，并且剩余的预留属性与正在运行的 SQL 数据库相匹配。

- 方案 2：运行两个 8 核 SQL 数据库，各运行一小时。 将对 8 核 SQL 数据库的计算用量应用 16 核预留折扣。
- 方案 3：从下午 1 点到下午 1:30 运行一个 16 核 SQL 数据库。 从下午 1:30 到下午 2 点运行另一个 16 核 SQL 数据库。 预留折扣同时涵盖这两个数据库。
- 情景 4：从下午 1 点到下午 1:45 运行一个 16 核 SQL 数据库。 从下午 1:30 到下午 2 点运行另一个 16 核 SQL 数据库。 将收取 15 分钟重叠期的即用即付费用。 预留折扣将应用到剩余时间的计算用量。

若要了解 Azure 预留的应用情况并在计费使用情况报告中查看该信息，请参阅[了解 Azure 预留使用情况](billing-understand-reserved-instance-usage-ea.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [什么是 Azure 预订？](billing-save-compute-costs-reservations.md)
- [通过 Azure 虚拟机预留实例为虚拟机预付费](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [通过 Azure SQL 数据库保留容量预付 SQL 数据库计算资源费用](../sql-database/sql-database-reserved-capacity.md)
- [管理 Azure 预留项](billing-manage-reserved-vm-instance.md)
- [了解即用即付订阅的预留使用情况](billing-understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
- [了解 CSP 订阅的预留使用情况](/partner-center/azure-reservations)
