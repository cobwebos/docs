---
title: 了解即用即付订阅的 Azure 预留使用情况 | Microsoft Docs
description: 了解如何读取使用情况，以了解如何应用即用即付订阅的 Azure 预留。
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
ms.openlocfilehash: cf1d7c67fe6033bf41317e75a33349ae07ecf643
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627877"
---
# <a name="understand-azure-reservation-usage-for-your-pay-as-you-go-subscription"></a>了解即用即付订阅的 Azure 预留使用情况

使用[“预留”页](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)中的 ReservationId 和 [Azure 帐户门户](https://account.azure.com)中的使用情况文件评估预留使用情况。

如果你是企业协议的客户，请参阅[了解适用于企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)。

本文假设要将预留应用到单个订阅。 如果将预留应用到多个订阅，则预留权益可能跨多个使用情况 CSV 文件。

## <a name="usage-for-reserved-virtual-machine-instances"></a>虚拟机预留实例的使用情况

以下部分假设在美国东部区域中运行 Standard_DS1_v2 Windows VM，并且虚拟机预留实例信息如下表中所示：

| 字段 | 值 |
|---| :---: |
|预订 ID |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|数量 |1|
|SKU | Standard_DS1_v2|
|区域 | eastus |

涵盖 VM 的硬件部分，因为部署的 VM 与预订属性匹配。 若要查看虚拟机预留实例未涵盖哪些 Windows 软件，请参阅 [Azure 虚拟机预留实例 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)。

### <a name="statement-section-of-csv-file-for-vms"></a>VM CSV 文件的声明部分

CSV 文件的此部分显示预留的总体使用情况。 将筛选器应用到包含“预留-”的“计量子类别”字段。 此时会显示以下屏幕截图中所示的内容：

![已筛选的预留使用情况详细信息和费用的屏幕截图](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

“预留-基础 VM”行包含预留涵盖的小时总数。 此行是 0.00 美元，因为预留涵盖它。 “预留-Windows Svr (单核)”行涵盖 Windows 软件的成本。

### <a name="daily-usage-section-of-csv-file"></a>CSV 文件的“每日使用情况”部分

筛选“其他信息”，并键入**预留 ID**。 以下屏幕截图显示了与预订相关的字段。

![每日使用详细信息及收费的屏幕截图](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. “其他信息”字段中的 **ReservationId** 是应用到 VM 的预留。
2. **ConsumptionMeter** 是 VM 的计量 ID。
3. “预留-基础 VM”**计量子类别**行表示声明部分中的 0 美元成本。 已通过预留支付了运行此 VM 的成本。
4. “计量 ID”是预留的计量 ID。 此计量的成本是 0 美元。 对于任何符合预留折扣条件的 VM，将显示此计量 ID。
5. Standard_DS1_v2 是 1 vCPU VM，此 VM 在不使用 Azure 混合权益的情况下部署。 因此，此计量涉及 Windows 软件的额外费用。 若要查找与 D 系列 1 核心 VM 相对应的计量，请参阅 [Azure 虚拟机预留实例 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)。 如果有 Azure 混合权益，则不会收取此额外费用。

## <a name="usage-for-sql-database-reserved-capacity-reservations"></a>SQL 数据库预留容量预留的使用情况

以下部分假设在美国东部区域运行 SQL 数据库第 4 代，并且预留信息如下表中所示：

| 字段 | 值 |
|---| --- |
|预订 ID |446ec809-423d-467c-8c5c-bbd5d22906b1|
|数量 |2|
|产品| SQL 数据库第 4 代（双核）|
|区域 | eastus |

### <a name="statement-section-of-csv-file"></a>CSV 文件的声明部分

筛选“预留实例使用情况”计量名称。 此时会显示以下屏幕截图中所示的内容：

![SQL 数据库预留容量的 CSV 文件](./media/billing-understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-statements.png)

“预留实例使用情况”行包含预留涵盖的核心小时总数。 此行的费率为 0 美元，因为预留涵盖了成本。

### <a name="detail-section-of-csv-file"></a>CSV 文件的详细信息部分

筛选“其他信息”，并键入**预留 ID**。 以下屏幕截图显示了与 SQL 数据库预留容量预订相关的字段。

![SQL 数据库预留容量的 CSV 文件](./media/billing-understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-details.png)

1. “其他信息”字段中的 **ReservationId** 是应用到 SQL 数据库资源的 SQL 数据库预留容量预订。
2. **ConsumptionMeter** 是 SQL 数据库资源的计量 ID。
3. “计量 ID”是预留计量。 此计量的成本是 0 美元。 任何符合预留折扣条件的 SQL 数据库资源将在 CSV 文件中显示此计量 ID。

## <a name="next-steps"></a>后续步骤

若要了解有关预留的详细信息，请参阅以下文章：

- [什么是 Azure 预留？](billing-save-compute-costs-reservations.md)
- [通过 Azure 虚拟机预留实例为虚拟机预付费](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [通过 Azure SQL 数据库保留容量预付 SQL 数据库计算资源费用](../sql-database/sql-database-reserved-capacity.md)
- [管理 Azure 中的预留](billing-manage-reserved-vm-instance.md)
- [了解预留折扣的应用方式](billing-understand-vm-reservation-charges.md)
- [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
- [预留未包含的 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
