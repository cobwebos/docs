---
title: 了解企业协议的 Azure 保留使用情况
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
ms.openlocfilehash: 507ad62a917120689bee3f1e293e23c9ab8b0f66
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598102"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>获取企业协议预订成本和使用情况

预订成本和使用情况数据可用于 Azure 门户和 REST Api 中的企业协议客户。 本文将帮助你:

- 获取预订采购数据
- 了解哪个订阅、资源组或资源使用了保留
- 保留利用率的分摊
- 计算保留节省
- 获取正在使用的保留数据
- 摊销预留成本

应用商店费用合并为使用情况数据。 您可以查看第一方使用情况、marketplace 使用情况以及单个数据源的购买费用。

## <a name="reservation-charges-in-azure-usage-data"></a>Azure 使用情况数据中的保留费用

数据分为两个不同的数据集:_实际成本_和_摊销成本_。 这两个数据集的不同之处:

**实际成本**-提供与每月帐单一致的数据。 此数据具有预订采购成本和预订应用程序详细信息。 使用此数据, 你可以知道哪个订阅或资源组或资源在特定的一天内接收到预订折扣。 接收预订折扣的使用情况的 EffectivePrice 为零。

**分期成本**-此数据集与实际成本数据集类似, 但用于获取预订折扣的使用量的 EffectivePrice 是预订的按比例分配成本 (而不是零)。 这可以帮助您了解订阅、资源组或资源的预留消耗量的货币值, 并可帮助您在内部为预订利用率进行收费。 数据集还包含未使用的预订小时。 数据集不包含预订购买记录。 

比较两个数据集:

| Data | 实际成本数据集 | 分期成本数据集 |
| --- | --- | --- |
| 预订购买 | 在此视图中可用。<br><br>  若要在 ChargeType = &quot;Purchase&quot;上获取此数据筛选器。 <br><br> 请参阅 ReservationID 或 ReservationName, 了解收取的费用是哪个预订。  | 不适用于此视图。 <br><br> 分期数据中未提供采购成本。 |
| EffectivePrice | 对于获取预订折扣的使用情况, 值为零。 | 对于具有预订折扣的使用量, 该值为预留的按小时的按比例分配成本。 |
| 未使用的预订 (提供保留时间未在一天内使用的小时数以及浪费的货币值) | 在此视图中不适用。 | 在此视图中可用。<br><br> 若要获取此数据, 请筛选 ChargeType &quot;=&quot;UnusedReservation。<br><br>  请参阅 ReservationID 或 ReservationName, 了解哪个预订未充分利用。 这是一天内浪费的预订量。  |
| 单价 (价格表中资源的价格) | 可用 | 可用 |

Azure 使用情况数据中提供的其他信息已更改:

- 产品和计量信息-Azure 不会将最初使用的计量器替换为 ReservationId 和 ReservationName, 就像以前一样。
- ReservationId 和 ReservationName-它们是数据中的自己的字段。 以前, 它仅在 AdditionalInfo 下可用。
- ProductOrderId-预留订单 ID, 添加为其自己的字段。
- ProductOrderName-采购预订的产品名称。
- 字词-12 个月或36个月。
- RINormalizationRatio-在 AdditionalInfo 下可用。 这是将预订应用于使用记录的比率。 如果为你的预订启用了实例大小灵活性, 则它可应用于其他大小。 此值显示对使用记录应用预订的比率。

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>使用 API 获取 Azure 消耗和预订使用情况数据

可以使用 API 获取数据, 也可以从 Azure 门户中下载数据。

使用 api 版本&quot;2019-04-01-preview&quot;调用[使用情况详细信息 api](/rest/api/consumption/usagedetails/list)以获取新数据。 有关术语的详细信息,[请参阅使用条款](billing-understand-your-usage.md)。 调用方应为使用[EA 门户](https://ea.azure.com)的企业协议的企业管理员。 只读企业管理员也可以获取数据。

数据在[适用于企业客户的报告 api 中不可用-使用情况详细信息](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail)。

下面是对 API 的调用示例:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-04-01-preview&amp;$filter={filter}
```

有关 {enrollmentId} 和 {billingPeriodId} 的详细信息, 请参阅 "[使用情况详细信息-列表](https://docs.microsoft.com/rest/api/consumption/usagedetails/list)API" 一文。

下表中有关指标和筛选器的信息可帮助解决常见的预订问题。

| **API 数据的类型** | API 调用操作 |
| --- | --- |
| **所有费用 (使用情况和购买情况)** | 将 {公制} 替换为 ActualCost |
| **获得预订折扣的使用情况** | 将 {公制} 替换为 ActualCost<br><br>将 {filter} 替换为: properties/reservationId% 20ne% 20 |
| **未获得预订折扣的使用量** | 将 {公制} 替换为 ActualCost<br><br>将 {filter} 替换为: properties/reservationId% 20eq% 20 |
| **分期支付费用 (使用情况和购买情况)** | 将 {公制} 替换为 AmortizedCost |
| **未使用的预订报表** | 将 {公制} 替换为 AmortizedCost<br><br>将 {filter} 替换为: properties/ChargeType% 20eq% 20 "UnusedReservation" |
| **预订购买** | 将 {公制} 替换为 ActualCost<br><br>将 {filter} 替换为: properties/ChargeType% 20eq% 20 "Purchase"  |
| **退款** | 将 {公制} 替换为 ActualCost<br><br>将 {filter} 替换为: properties/ChargeType% 20eq% 20 ' 退款 ' |

## <a name="download-the-usage-csv-file-with-new-data"></a>使用新数据下载使用情况 CSV 文件

如果你是 EA 管理员, 则可以从 Azure 门户下载包含新使用情况数据的 CSV 文件。 此数据在[EA 门户](https://ea.azure.com)中不可用。

在 Azure 门户中, 导航到 "[成本管理 + 计费](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts)"。

1. 选择计费帐户。
2. 单击 "**使用情况 + 费用**"。
3. 单击“下载”。  
![示例显示了在何处下载 CSV 使用情况数据文件 Azure 门户](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. 在 "**下载用量和费用**" 中的 "**使用情况详细信息版本 2** " 下, 选择 "**所有费用 (使用情况和购买情况)** ", 然后单击 " 重复执行**摊销费用 (使用情况和购买)** 。

所下载的 CSV 文件包含实际成本和摊销成本。

## <a name="common-cost-and-usage-tasks"></a>常见成本和使用情况任务

以下部分是大多数用户用于查看其预订成本和使用情况数据的常见任务。

### <a name="get-reservation-purchase-costs"></a>获取预订采购成本

预订购买成本在实际成本数据中可用。 筛选_ChargeType = Purchase_。 请参阅 ProductOrderID, 以确定购买的预订订单。

### <a name="get-underutilized-reservation-quantity-and-costs"></a>获取未充分利用的预订数量和成本

获取摊销成本数据并筛选_ChargeType_ _= UnusedReservation_。 您将获得每日未使用的预订数量和成本。 您可以分别使用_ReservationId_和_ProductOrderId_字段筛选预订或预订订单的数据。 如果预留为 100%, 则记录的数量为0。

### <a name="amortize-reservation-costs"></a>摊销预留成本

获取摊销成本数据并使用_ProductOrderID_筛选预订订单, 以获取预订的每日摊销成本。

### <a name="chargeback-for-a-reservation"></a>预留的退款

你可以按订阅、资源组或标记将保留使用预留给其他组织。 分期成本数据在以下数据类型中提供预订利用率的货币值:

- 资源 (例如 VM)
- 资源组
- Tags
- 订阅

### <a name="get-the-blended-rate-for-chargeback"></a>获取按使用计费的混合速率

若要确定混合速率, 请获取摊销成本数据并聚合总成本。 对于 Vm, 可以使用 MeterName 或 ServiceType 信息来自 AdditionalInfo JSON 数据。 将总成本除以用于获取混合费率的数量。

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>审核最佳保留使用实例大小灵活性

具有_RINormalizationRatio_的多个数量, 从 AdditionalInfo。 结果指示将使用的保留使用小时数应用到使用记录。

### <a name="determine-reservation-savings"></a>确定保留节省

获取摊销成本数据并筛选预订实例的数据。 然后：

1. 获取估计的即用即付成本。 如果预订折扣不适用于使用量, 则将 "_单价_" 值与 "_数量_" 值相乘以获得估计的即用即付成本。
2. 获取预订成本。 对_成本_值求和, 获取为预订实例支付的金额的货币值。 它包括预留的已用和未使用的成本。
3. 从估算的即用即付成本减去保留成本以获得估计的节省量。

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>成本分析中的预订购买和摊销

[成本分析](https://aka.ms/costanalysis)提供预订成本。 默认情况下, 成本分析显示**实际成本**, 即计费上显示成本的方式。 若要查看已细分并与使用该权益的资源关联的预订购买, 请切换到**分期成本**:

![显示成本分析中选择分摊成本的示例](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

按计费类型分组, 查看使用情况、购买情况和退款情况下的中断;或按预订来细分预订和按需收费。 请记住, 查看实际成本时你会看到的唯一预订成本是购买的, 但在查看分期成本时, 成本将分配给使用 benfit 的单个资源。 查看摊销成本时, 还会看到新的**UnusedReservation**费用类型。

## <a name="need-help-contact-us"></a>需要帮助? 请联系我们。

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
