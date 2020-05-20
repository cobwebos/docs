---
title: 个人订阅的 Azure 预留使用情况
description: 了解如何读取使用情况，以了解如何为采用即用即付费率的个人订阅应用 Azure 预留。
author: bandersmsft
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 5867c4ba7fa1447cbd5d40e15237ae50c24c7168
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199256"
---
# <a name="understand-azure-reservation-usage-for-your-individual-subscription-with-pay-as-you-go-rates-subscription"></a>了解采用即用即付费率的个人订阅的 Azure 预留使用情况

使用[“预留”页](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)中的 ReservationId 和 [Azure 帐户门户](https://account.azure.com)中的使用情况文件评估预留使用情况。

如果你是企业协议的客户，请参阅[了解适用于企业合约的预留使用情况](understand-reserved-instance-usage-ea.md)。

本文假设要将预留应用到单个订阅。 如果将预留应用到多个订阅，则预留权益可能跨多个使用情况 CSV 文件。

## <a name="usage-for-reserved-virtual-machine-instances"></a>虚拟机预留实例的使用情况

以下部分假设在美国东部区域中运行 Standard_DS1_v2 Windows VM，并且虚拟机预留实例信息如下表中所示：

| 字段 | 值 |
|---| :---: |
|预订 ID |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|数量 |1|
|SKU | Standard_DS1_v2|
|区域 | eastus |

涵盖 VM 的硬件部分，因为部署的 VM 与预订属性匹配。 若要查看虚拟机预留实例未涵盖哪些 Windows 软件，请参阅 [Azure 虚拟机预留实例 Windows 软件成本](reserved-instance-windows-software-costs.md)。

### <a name="statement-section-of-csv-file-for-vms"></a>VM CSV 文件的声明部分

CSV 文件的此部分显示预留的总体使用情况。 将筛选器应用到包含“预留-”的“计量子类别”字段。   此时会显示以下屏幕截图中所示的内容：

![已筛选的预留使用情况详细信息和费用的屏幕截图](./media/understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

“预留-基础 VM”行包含预留涵盖的小时总数。  此行是 0.00 美元，因为预留涵盖它。 “预留-Windows Svr (单核)”行涵盖 Windows 软件的成本。 

### <a name="daily-usage-section-of-csv-file"></a>CSV 文件的“每日使用情况”部分

筛选“其他信息”，并键入**预留 ID**。 以下屏幕截图显示了与预订相关的字段。

![每日使用详细信息及收费的屏幕截图](./media/understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. “其他信息”字段中的 **ReservationId** 是应用到 VM 的预留。
2. **ConsumptionMeter** 是 VM 的计量 ID。
3. “预留-基础 VM”**计量子类别**行表示声明部分中的 0 美元成本。  已通过预留支付了运行此 VM 的成本。
4. “计量 ID”是预留的计量 ID。  此计量的成本是 0 美元。 对于任何符合预留折扣条件的 VM，将显示此计量 ID。
5. Standard_DS1_v2 是 1 vCPU VM，此 VM 在不使用 Azure 混合权益的情况下部署。 因此，此计量涉及 Windows 软件的额外费用。 若要查找与 D 系列 1 核心 VM 相对应的计量，请参阅 [Azure 虚拟机预留实例 Windows 软件成本](reserved-instance-windows-software-costs.md)。 如果有 Azure 混合权益，则不会收取此额外费用。

## <a name="usage-for-sql-database--cosmos-db-reservations"></a>SQL 数据库和 Cosmos DB 预留的使用情况

以下部分将以 Azure SQL 数据库为例来描述使用情况报表。 也可以使用相同的步骤来获取 Azure Cosmos DB 的使用情况。

假设在美国东部地区运行 SQL 数据库第 4 代，你的预留信息将类似于下表所示：

| 字段 | 值 |
|---| --- |
|预订 ID |446ec809-423d-467c-8c5c-bbd5d22906b1|
|数量 |2|
|Products| SQL 数据库第 4 代（双核）|
|区域 | eastus |

### <a name="statement-section-of-csv-file"></a>CSV 文件的声明部分

筛选“预留实例使用量”计量名称，并选择所需的“计量类别” - Azure SQL 数据库或 Azure Cosmos DB。   此时会显示以下屏幕截图中所示的内容：

![SQL 数据库预留容量的 CSV 文件](./media/understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-statements.png)

“预留实例使用情况”行包含预留涵盖的核心小时总数。  此行的费率为 0 美元，因为预留涵盖了成本。

### <a name="detail-section-of-csv-file"></a>CSV 文件的详细信息部分

筛选“其他信息”，并键入**预留 ID**。 以下屏幕截图显示了与 SQL 数据库预留容量预订相关的字段。

![SQL 数据库预留容量的 CSV 文件](./media/understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-details.png)

1. “其他信息”字段中的 **ReservationId** 是应用到 SQL 数据库资源的 SQL 数据库预留容量预订。
2. **ConsumptionMeter** 是 SQL 数据库资源的计量 ID。
3. “计量 ID”  是预留计量。 此计量的成本是 0 美元。 任何符合预留折扣条件的 SQL 数据库资源将在 CSV 文件中显示此计量 ID。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [什么是 Azure 预订？](save-compute-costs-reservations.md)
- [通过 Azure 虚拟机预留实例为虚拟机预付费](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [通过 Azure SQL 数据库保留容量预付 SQL 数据库计算资源费用](../../sql-database/sql-database-reserved-capacity.md)
- [管理 Azure 预留项](manage-reserved-vm-instance.md)
- [了解预留折扣的应用方式](../manage/understand-vm-reservation-charges.md)
- [了解企业合约的预留使用情况](understand-reserved-instance-usage-ea.md)
- [预订未包含的 Windows 软件成本](reserved-instance-windows-software-costs.md)
