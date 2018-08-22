---
title: 了解 Azure 预留折扣 | Microsoft Docs
description: 了解如何对运行中的 SQL 数据库应用预留折扣。
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: yashar
ms.openlocfilehash: 3a5339200309a50eace5b48dfbd9127b7eb1dae6
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39631999"
---
# <a name="understand-how-an-azure-reservation-discount-is-applied-to-sql-databases"></a>了解如何对 SQL 数据库应用 Azure 预留折扣

购买 Azure SQL 数据库预留容量后，预留折扣会自动应用到与预留属性和数量匹配的 SQL 数据库。 预留涵盖 SQL 数据库的计算成本。 将按标准费率收取软件、存储和网络费用。 可以通过 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)来包含 SQL 数据库的许可成本。

对于虚拟机预留实例，请参阅[了解 Azure 虚拟机预留实例折扣](billing-understand-vm-reservation-charges.md)。

## <a name="reservation-discount-applied-to-sql-databases"></a>应用到 SQL 数据库的预留折扣

 SQL 数据库预留容量折扣按小时应用到正在运行的 SQL 数据库。 购买的预留容量将与运行中 SQL 数据库产生的计算用量进行匹配。 对于不是整小时运行的 SQL 数据库，预留将自动应用到与预留属性匹配其他 SQL 数据库。 折扣可以应用到同时运行的 SQL 数据库。 如果与预留属性匹配的 SQL 数据库不是整小时运行，则无法获得该小时的完整预留折扣权益。

以下示例演示如何根据购买的核心数目以及 SQL 数据库的运行时间，来应用 SQL 数据库预留容量折扣。

- 场景 1：为 8 核 SQL 数据库购买了 SQL 数据库预留容量。 运行一个与剩余预留属性匹配的 16 核 SQL 数据库。 将按 8 个核心的 SQL 数据库计算用量收取即用即付费用。 将获得 8 核 SQL 数据库计算用量的一小时预留折扣。

剩余的示例假设购买的 SQL 数据库预留容量用于 16 核 SQL 数据库，并且剩余的预留属性与正在运行的 SQL 数据库相匹配。

- 场景 2：运行两个 8 核 SQL 数据库，各运行一小时。 将对 8 核 SQL 数据库的计算用量应用 16 核预留折扣。
- 场景 3：从下午 1 点到下午 1:30 运行一个 16 核 SQL 数据库。 从下午 1:30 到下午 2 点运行另一个 16 核 SQL 数据库。 预留折扣同时涵盖这两个数据库。
- 场景 4：从下午 1 点到下午 1:45 运行一个 16 核 SQL 数据库。 从下午 1:30 到下午 2 点运行另一个 16 核 SQL 数据库。 将收取 15 分钟重叠期的即用即付费用。 预留折扣将应用到剩余时间的计算用量。

若要了解 Azure 预留的应用情况并在计费使用情况报告中查看该信息，请参阅[了解 Azure 预留使用情况](https://go.microsoft.com/fwlink/?linkid=862757)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预留的详细信息，请参阅以下文章：

- [什么是 Azure 预留？](billing-save-compute-costs-reservations.md)
- [通过 Azure 虚拟机预留实例为虚拟机预付费](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [通过 Azure SQL 数据库保留容量预付 SQL 数据库计算资源费用](../sql-database/sql-database-reserved-capacity.md)
- [管理 Azure 预留项](billing-manage-reserved-vm-instance.md)
- [了解即用即付订阅的预留使用情况](billing-understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
- [了解 CSP 订阅的预留使用情况](https://docs.microsoft.com/partner-center/azure-reservations)


## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
