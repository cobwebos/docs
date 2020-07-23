---
title: 确定要购买哪种 Azure 预留项
description: 本文帮助你确定要购买哪种预留项。
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: banders
ms.openlocfilehash: feee7475dcadc6d06693d9e60020097f8dc9149c
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628599"
---
# <a name="determine-what-reservation-to-purchase"></a>确定要购买的预留项

除 Azure Databricks 以外的所有预留项都是按小时应用的。 应该根据稳定的基本用量来购买预留项。 可按多种方式确定要购买的内容，本文将帮助你确定要购买的预留项。

如果购买的容量超过以往的用量，会导致预留利用不足的情况。 应尽量避免利用不足的情况。 过去一小时未使用的预留容量不会转结到下一小时。 超过预留数量的用量将按照更高的即用即付费率收费。

## <a name="analyze-usage-data"></a>分析用量数据

参考以下部分来帮助分析每日用量数据，以确定基线用量以及要购买的预留项。

### <a name="analyze-usage-for-a-vm-reserved-instance-purchase"></a>分析用量以确定要购买哪种 VM 预留实例

确定要购买的正确 VM 大小。 例如，为 ES 系列 VM 购买的预留项不适用于 E 系列 VM，反之亦然。

促销系列的 VM 不提供预留折扣，因此请从分析中将其删除。

若要将分析范围缩小到符合条件的 VM 用量，请对用量数据应用以下筛选器：

- 筛选“计量类别”以显示“虚拟机”。  
- 从“更多信息”中获取“服务类型”信息。   这些信息会推荐正确的 VM 大小。 例如“标准 E32”。
- 使用“资源位置”字段确定用量数据中心。 

忽略当天使用时间小于 24 小时的资源。

若要在实例大小系列级别进行分析，可以从 [https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv) 获取实例大小灵活性值。 将这些值与数据合并即可执行分析。 有关实例大小灵活性的详细信息，请参阅[虚拟机预留实例的虚拟机大小灵活性](../../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)。

### <a name="analyze-usage-for-an-azure-synapse-analytics-reserved-instance-purchase"></a>分析所购买的 Azure Synapse Analytics 预留实例的用量

预留容量应用于 Azure Synapse Analytics DWU 定价。 它不应用于 Azure Synapse Analytics 许可成本，也不应用于除了计算以外的任何成本。

若要缩小符合条件的用量范围，请对用量数据应用以下筛选器：


- 筛选“计量类别”以显示“SQL 数据库”。  
- 筛选“计量名称”以显示“vCore”。  
- 筛选“计量子类别”以显示名称中包含“Compute”的所有用量记录。  

从“更多信息”中获取“vCore”值。   此值会告知使用了多少个 vCore。 数量为 **vCore** 数量乘以数据库的使用小时数。

该数据会告知以下各项的持续用量：

- 数据库类型的组合。 例如，每个单一数据库的托管实例或弹性池。
- 服务层。 例如，“常规用途”或“业务关键”。
- 代系。 例如“第 5 代”。
- 资源位置

### <a name="analysis-for-azure-synapse-analytics"></a>对 Azure Synapse Analytics 的分析

预留容量应用于 Azure Synapse Analytics DWU 用量，以 100 个 DWU 为增量购买。 若要缩小符合条件的用量范围，请对用量数据应用以下筛选器：

- 筛选“计量名称”以显示“100 个 DWU”。  
- 筛选“计量子类别”以显示“第 2 代计算优化”。  

使用“资源位置”字段确定某个区域中 Azure Synapse Analytics 的用量。 

Azure Synapse Analytics 用量在一天中可能会上下波动。 请咨询管理 Azure Synapse Analytics 实例的团队来了解基本用量。

在 Azure 门户中转到“预留”，以 100 个 DWU 的倍数购买 Azure Synapse Analytics 预留容量。

## <a name="reservation-purchase-recommendations"></a>预留项购买建议

预留项购买建议是在分析过去 7 天、30 天和 60 天的每小时用量数据后计算出来的。 如果你购买了预留项，Azure 将会计算现有的成本，并将其与某个持续时间内的实际即用即付成本进行比较。 这种计算是针对该时间范围内使用的每个数量执行的。 Azure 会推荐一个可以最大程度地实现节省的数量。

例如，你可能在大多数时间使用 500 个 VM，偶尔会在高峰期使用 700 个 VM。 在此示例中，Azure 会计算使用 500 和 700 个 VM 可实现的节省。 由于使用 700 个 VM 是偶发性的，因此建议计算会确定为 500 个 VM 购买预留项可实现的最大节省，并针对数量 500 提供建议。

请注意以下几点：

- 预留建议是使用适用于你的按需使用费率计算的。
- 建议是按各种大小计算的，而不是按实例大小系列计算的。
- 针对某个范围建议的数量将在为该范围购买预留项的同一日减少。
    - 但是，针对不同范围的预留数量建议最长可能需要在 25 天后才能更新。 例如，如果你根据共享的范围建议购买了预留项，则单个订阅范围建议最长可能需要在 25 天后才能下调。

## <a name="recommendations-in-the-azure-portal"></a>Azure 门户中的建议

建议引擎计算的预留购买内容显示在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/docs)中的“建议”选项卡上。  下面是一个示例插图。

![显示建议的插图](./media/determine-reservation-purchase/select-product-ri.png)

## <a name="recommendations-in-the-cost-management-power-bi-app"></a>成本管理 Power BI 应用中的建议

企业协议和 Microsoft 客户协议客户可以使用“VM RI 覆盖范围”报表来查看 VM 和购买建议。 覆盖范围报表显示总用量以及预留实例涵盖的用量。

1. 获取[成本管理应用](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)。
2. 根据要在哪个范围购买预留项，转到“VM RI 覆盖范围”报表 –“共享范围”或“单个范围”。
3. 选择区域、要查看其用量的实例大小系列、RI 覆盖范围，以及应用所选筛选器后显示的购买建议。

## <a name="recommendations-in-azure-advisor"></a>Azure 顾问中的建议

[Azure 顾问](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview)中提供了预留购买建议。

- 顾问只有单一订阅范围的建议。
- 顾问建议是使用 30 天回溯期数据计算的。 预计节省量适用于 3 年预留期。
- 如果购买共享范围预留，则顾问预留购买建议最多可能需要 30 天才会消失。

## <a name="recommendations-using-apis"></a>使用 API 的建议

使用[预留建议](/rest/api/consumption/reservationrecommendations/list) REST API 能够以编程方式查看建议。

## <a name="next-steps"></a>后续步骤

- [管理 Azure 预留项](manage-reserved-vm-instance.md)
- [了解采用即用即付费率的订阅的预留项使用情况](understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](understand-reserved-instance-usage-ea.md)
- [预留未包含的 Windows 软件成本](reserved-instance-windows-software-costs.md)