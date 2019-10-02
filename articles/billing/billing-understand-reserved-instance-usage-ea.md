---
title: 了解企业协议的 Azure 预留项使用情况
description: 了解如何读取使用情况，以了解如何应用适用于企业合约的 Azure 预留。
author: bandersmsft
manager: yashar
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: f2f5b2ecf096d7dc8babb79a38d00158a2120688
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71218078"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>获取企业协议预留成本和使用情况

企业协议客户可以在 Azure 门户和 REST API 中查看预留成本和使用情况数据。 本文将会帮助你：

- 获取预留项购买数据
- 了解哪个订阅、资源组或资源使用了预留项
- 预留项的使用费分摊
- 计算预留节省额
- 获取预留未充分利用的数据
- 摊销预留成本

市场费用将整合成使用情况数据。 可以从单个数据源查看第一方使用情况、市场使用情况和购买项目。

## <a name="reservation-charges-in-azure-usage-data"></a>Azure 使用情况数据中的预留费用

数据划分为两个不同的数据集：“实际成本”和“摊销成本”。   这两个数据集的差别如下：

**实际成本** - 提供要与每月帐单对帐的数据。 此数据包括预留项购买成本和预留项应用详细信息。 使用此数据可以知道哪个订阅、资源组或资源在特定的日期收到了预留折扣。 收到预留折扣的用途的 EffectivePrice 为零。

**摊销成本** - 此数据集与“实际成本”数据集类似，差别在于：获取预留折扣的用途的 EffectivePrice 是预留项的按比例分配成本（而不是零）。 这可以帮助你了解订阅、资源组或资源消耗的预留项的货币价值，并可帮助你在内部分摊预留项的使用费。 该数据集还包括预留项的未使用小时数。 该数据集不包括预留项购买记录。 

两个数据集的比较：

| 数据 | “实际成本”数据集 | “摊销成本”数据集 |
| --- | --- | --- |
| 预留项购买 | 在此视图中适用。<br><br>  若要获取此数据，请使用筛选器 ChargeType = &quot;Purchase&quot;。 <br><br> 参考 ReservationID 或 ReservationName 来了解费用适用于哪个预留项。  | 不适用于此视图。 <br><br> 摊销数据中不提供购买成本。 |
| EffectivePrice | 对于获取预留折扣的用途，值为零。 | 对于提供预留折扣的用途，值为预留项的按小时按比例分配成本。 |
| 未使用的预留项（提供预留项在一天中未使用的小时数，以及浪费的货币价值） | 在此视图中不适用。 | 在此视图中适用。<br><br> 若要获取此数据，请使用筛选器 ChargeType = &quot;UnusedReservation&quot;。<br><br>  参考 ReservationID 或 ReservationName 来了解哪个预留项未充分利用。 这反映了该预留项在当天的浪费程度。  |
| UnitPrice（价目表中资源的价格） | 可用 | 可用 |

Azure 使用情况数据中提供的其他信息已更改：

- 产品和计量信息 - Azure 不会像以前那样将最初消耗的计量替换为 ReservationId 和 ReservationName。
- ReservationId 和 ReservationName - 它们在数据中具有自身的字段。 以前，它们往往只在 AdditionalInfo 下提供。
- ProductOrderId - 预留订单 ID，作为自身的字段添加。
- ProductOrderName - 已购买的预留项的产品名称。
- Term - 12 个月或 36 个月。
- RINormalizationRatio - 在 AdditionalInfo 下提供。 这是预留项应用到使用记录的比率。 如果为预留项启用了实例大小灵活性，则它可应用到其他大小。 该值显示预留项应用到使用记录的比率。

[请参阅字段定义](https://docs.microsoft.com/rest/api/consumption/usagedetails/list#definitions)

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>使用 API 获取 Azure 消耗量和预留项使用情况数据

可以使用 API 获取数据，也可以从 Azure 门户下载数据。

调用[使用情况详细信息 API](/rest/api/consumption/usagedetails/list) 以获取新数据。 有关术语的详细信息，请参阅[使用情况中的术语](billing-understand-your-usage.md)。 调用方应是企业协议的企业管理员，并使用 [EA 门户](https://ea.azure.com)。 只读企业管理员也可以获取数据。

该数据不会在[面向企业客户的报告 API - 使用情况详细信息](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail)中提供。

下面是调用 API 的示例：

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

有关 {enrollmentId} 和 {billingPeriodId} 的详细信息，请参阅[使用情况详细信息 - 列出](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) API 文章。

下表中有关指标和筛选器的信息可帮助解决常见的预留项问题。

| **API 数据的类型** | API 调用操作 |
| --- | --- |
| **所有费用(使用情况和购买项目)** | 请将 {metric} 替换为 ActualCost |
| **获得预留折扣的用途** | 请将 {metric} 替换为 ActualCost<br><br>请将 {filter} 替换为 properties/reservationId%20ne%20 |
| **未获得预留折扣的用途** | 请将 {metric} 替换为 ActualCost<br><br>请将 {filter} 替换为 properties/reservationId%20eq%20 |
| **摊销费用(使用情况和购买项目)** | 请将 {metric} 替换为 AmortizedCost |
| **未使用的预留项报告** | 请将 {metric} 替换为 AmortizedCost<br><br>请将 {filter} 替换为 properties/ChargeType%20eq%20'UnusedReservation' |
| **预留项购买** | 请将 {metric} 替换为 ActualCost<br><br>请将 {filter} 替换为 properties/ChargeType%20eq%20'Purchase'  |
| **退款** | 请将 {metric} 替换为 ActualCost<br><br>请将 {filter} 替换为 properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>下载包含新数据的使用情况 CSV 文件

如果你是 EA 管理员，可以从 Azure 门户下载包含新使用情况数据的 CSV 文件。 在 [EA 门户](https://ea.azure.com)中无法获取此数据。

在 Azure 门户中，导航到[“成本管理 + 计费”](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts)。

1. 选择计费帐户。
2. 单击“使用情况 + 费用”。 
3. 单击“下载”  。  
![该示例显示可在 Azure 门户中的哪个位置下载 CSV 使用情况数据文件](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. 在“下载使用情况 + 费用”中的“使用情况详细信息版本 2”下，选择“所有费用(使用情况和购买项目)”，然后单击“下载”。    针对“摊销费用(使用情况和购买项目)”重复上述步骤。 

下载的 CSV 文件包含实际成本和摊销成本。

## <a name="common-cost-and-usage-tasks"></a>常见成本和用法任务

以下部分描述了大多数用户用来查看其预留成本和使用情况数据的常见任务。

### <a name="get-reservation-purchase-costs"></a>获取预留项购买成本

预留项购买成本在实际成本数据中提供。 根据 _ChargeType = Purchase_ 进行筛选。 参考 ProductOrderID 来确定订购了哪个预留项。

### <a name="get-underutilized-reservation-quantity-and-costs"></a>获取未充分利用的预留项的数量和成本

获取摊销成本数据，并根据 _ChargeType_ _= UnusedReservation_ 进行筛选。 你将获得每日未使用的预留项数量和成本。 可以分别使用 _ReservationId_ 和 _ProductOrderId_ 字段来筛选预留项或预留订单的数据。 如果预留项的利用率为 100%，则记录中包含数量 0。

### <a name="amortize-reservation-costs"></a>摊销预留成本

获取摊销成本数据，并使用 _ProductOrderID_ 筛选预留订单，以获取预留项的每日摊销成本。

### <a name="chargeback-for-a-reservation"></a>预留项的费用分摊

可以按订阅、资源组或标记将预留项使用费分摊到其他组织。 摊销成本数据按以下数据类型提供预留项的货币利用价值：

- 资源（例如 VM）
- Resource group
- Tags
- Subscription

### <a name="get-the-blended-rate-for-chargeback"></a>获取费用分摊的混合费率

若要确定混合费率，请获取摊销成本数据并聚合总成本。 对于 VM，可以使用 AdditionalInfo JSON 数据中的 MeterName 或 ServiceType 信息。 将总成本除以数量即可得出混合费率。

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>根据实例大小灵活性审核最佳预留用法

将 AdditionalInfo 中的数量与 _RINormalizationRatio_ 相乘。 结果会指出已将多少个预留项使用小时数应用到使用情况记录。

### <a name="determine-reservation-savings"></a>确定预留节省额

获取摊销成本数据，并筛选预留实例的数据。 然后：

1. 获取估计的即用即付成本。 如果预留折扣不适用于该用途，将 _UnitPrice_ 值与 _Quantity_ 值相乘可以得出估计的即用即付成本。
2. 获取预留成本。 将 _Cost_ 值求和可以得出为预留实例支付的货币价值。 这包括预留项的已用和未用成本。
3. 从估计的即用即付成本中减去预留成本可以得出估计的节省额。

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>成本分析中的预留项购买和摊销

[成本分析](https://aka.ms/costanalysis)中提供预留成本。 默认情况下，成本分析会显示“实际成本”，即帐单上显示的成本。  若要查看已细分的并与使用权益的资源关联的预留购买项目，请切换到“摊销成本”： 

![该示例显示可在成本分析中的哪个位置选择摊销成本](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

按费用类型分组可以查看使用情况、购买项目和退款的细目；按预留项分组可以查看预留项和按需成本的细目。 请记住，在查看实际成本时，只能看到购买项目的预留成本；但查看摊销成本时，成本将分配到使用权益的各个资源。 查看摊销成本时，还会看到新的 **UnusedReservation** 费用类型。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [什么是 Azure 预订？](billing-save-compute-costs-reservations.md)
- [通过 Azure 虚拟机预留实例为虚拟机预付费](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [通过 Azure SQL 数据库保留容量预付 SQL 数据库计算资源费用](../sql-database/sql-database-reserved-capacity.md)
- [管理 Azure 预留项](billing-manage-reserved-vm-instance.md)
- [了解预留折扣的应用方式](billing-understand-vm-reservation-charges.md)
- [了解即用即付订阅的预留使用情况](billing-understand-reserved-instance-usage.md)
- [预订未包含的 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)
