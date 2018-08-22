---
title: 了解企业的 Azure 预留使用情况 | Microsoft Docs
description: 了解如何读取使用情况，以了解如何应用适用于企业合约的 Azure 预留。
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: manshuk
ms.openlocfilehash: 5ce0103315f297996ed3f3bd88b5e53558e22e14
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628226"
---
# <a name="understand-azure-reservation-usage-for-your-enterprise-enrollment"></a>了解适用于企业合约的 Azure 预留使用情况

使用[“预留”页](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)中的 **ReservationId** 和 [EA 门户](https://ea.azure.com)中的使用情况文件评估预留使用情况。 还可以在 [EA 门户](https://ea.azure.com)的使用情况摘要部分看到预留使用情况。

如果已在即用即付计费上下文中购买了预留，请参阅[了解即用即付订阅的预留使用情况](billing-understand-reserved-instance-usage.md)。

## <a name="usage-for-reserved-virtual-machines-instances"></a>预留虚拟机实例的使用情况

在以下部分中，假定在美国东部区域运行 Standard_D1_v2 Windows VM，则预留信息如下表所示：

| 字段 | 值 |
|---| --- |
|预订 ID |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|数量 |1|
|SKU | Standard_D1|
|区域 | eastus |

涵盖 VM 的硬件部分，因为部署的 VM 与预订属性匹配。 若要查看预留未涵盖哪些 Windows 软件，请参阅 [Azure 虚拟机预留实例 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)。

### <a name="usage-in-csv-file-for-reserved-vm-instances"></a>虚拟机预留实例的 CSV 文件中的使用情况

可以从企业门户下载企业使用情况 CSV 文件。 在 CSV 文件中，筛选“其他信息”，然后键入 **ReservationID**。 以下屏幕截图显示了与预订相关的字段：

![适用于 Azure 预留的企业协议 (EA) csv](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. “其他信息”字段中的 **ReservationId** 表示适用于 VM 的预留。
2. **ConsumptionMeter** 是 VM 的计量 ID。
3. “计量 ID”是费用为 $0 的预留计量。 运行 VM 的费用按虚拟机预留实例来支付。
4. Standard_D1 是 1 vCPU VM，此 VM 在不使用 Azure 混合权益的情况下部署。 因此，此计量涉及 Windows 软件的额外费用。 若要查找与 D 系列 1 核心 VM 相对应的计量，请参阅 [Azure 虚拟机预留实例 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)。  如果有 Azure 混合权益，则不会收取此额外费用。

## <a name="usage-for-sql-database-reserved-capacity-reservations"></a>SQL 数据库预留容量预留的使用情况

在以下部分，假定在美国东部区域运行 SQL 数据库第 4 代，则预留信息如下表所示：

| 字段 | 值 |
|---| --- |
|预订 ID |8244e673-83e9-45ad-b54b-3f5295d37cae|
|数量 |2|
|产品| SQL 数据库第 4 代（2 核）|
|区域 | eastus |

### <a name="usage-in-csv-file-for-sql-database-reserved-capacity"></a>CSV 文件中 SQL 数据库预留容量的使用情况

筛选“其他信息”，并键入**预留 ID**。 以下屏幕截图显示了与预订相关的字段。

![适用于 SQL 数据库预留容量的企业协议 (EA) csv](./media/billing-understand-reserved-instance-usage-ea/billing-ea-sql-db-reserved-capacity-csv.png)

1. “其他信息”字段中的 **ReservationId** 是适用于 SQL 数据库资源的预留。
2. **ConsumptionMeter** 是 SQL 数据库资源的计量 ID。
3. “计量 ID”是费用为 $0 的预留计量。 任何符合预留资格的 SQL 数据库资源都会在 CSV 文件中显示此计量 ID。

## <a name="usage-summary-page-in-enterprise-portal"></a>企业门户中的使用情况摘要页

Azure 预留使用情况也会出现在企业门户的使用情况摘要部分：![企业协议 (EA) 使用情况摘要](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. 不会收取 VM 的硬件组件费用，因为它已被预留涵盖。 对于 SQL 数据库预留，则会看到“服务名称”为“Azure SQL 数据库预留容量使用情况”的一行。
2. 在此示例中，你没有 Azure 混合权益，因此会对你收取在 VM 中使用的 Windows 软件的费用。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预留的详细信息，请参阅以下文章：

- [什么是 Azure 预留？](billing-save-compute-costs-reservations.md)
- [通过 Azure 虚拟机预留实例为虚拟机预付费](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [通过 Azure SQL 数据库预留容量预付 SQL 数据库计算资源费用](../sql-database/sql-database-reserved-capacity.md) 
- [管理 Azure 预留项](billing-manage-reserved-vm-instance.md)
- [了解如何应用预留折扣](billing-understand-vm-reservation-charges.md)
- [了解即用即付订阅的预留使用情况](billing-understand-reserved-instance-usage.md)
- [预留未包含的 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。