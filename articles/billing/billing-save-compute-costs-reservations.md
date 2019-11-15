---
title: 什么是 Azure 预留项？
description: 了解 Azure 预留项和定价以节省虚拟机、SQL 数据库、Azure Cosmos DB 和其他资源的成本。
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: banders
ms.openlocfilehash: a002f5a1657454ff5048ab695e62eccd268a5891
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580094"
---
# <a name="what-are-azure-reservations"></a>什么是 Azure 预留项？

Azure 预留承诺预付适用于虚拟机、Azure Blob 存储或 Azure Data Lake Storage Gen2、SQL 数据库计算容量、Azure Cosmos DB 吞吐量或其他 Azure 资源的一年或三年计划，为你节省资金。 通过承诺预付，能够以折扣价购买所用资源。 预留可显著将资源的成本，最多减少至即用即付价格的 72%。 预订提供计费折扣，并且不会影响资源的运行时状态。

可以提前或每月为预留项付费。 预付和每月预订的总费用相同，选择每月支付不会产生额外的费用。 每月付款适用于 Azure 预留项，而不适用于第三方产品。

可在 [Azure 门户](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)中购买预留。

## <a name="why-buy-a-reservation"></a>为何购买预留项？

如果你有使用大容量/高吞吐量或者长时间运行的虚拟机、Blob 存储数据、Azure Cosmos DB 或 SQL 数据库，那么购买预留项不失为一项获得最实惠价格的选择。 例如，如果持续运行四个服务实例，在不购买预留项的情况下，需按即用即付价格付费。 如果购买这些资源的预留项，则将立即获得预留折扣。 这些资源不再按即用即付费率收费。

## <a name="charges-covered-by-reservation"></a>预留项涵盖的费用

服务计划：

- **虚拟机预留实例** - 预留项仅涵盖虚拟机计算成本。 而不涵盖软件、网络或存储等其他费用。
- **Azure 存储预留容量** - 该预留项涵盖用于 Blob 存储或 Azure Data Lake Gen2 存储的标准存储帐户的存储容量。 不涵盖带宽或事务费率。
- **Azure Cosmos DB 预留容量** - 预留项涵盖为资源预配的吞吐量的费用。 但它不涵盖存储和网络费用。
- **SQL 数据库预留 vCore** - 预留项仅包含计算成本。 许可证单独计费。
- **SQL 数据仓库** - 预留项涵盖 cDWU 用量。 它不涵盖与 SQL 数据仓库用量相关的存储或网络费用。
- **应用服务印花费** - 预留项涵盖印花使用费。 它不适用于辅助角色，因此与印花相关的任何其他资源将单独收费。

对于 Windows 虚拟机和 SQL 数据库，可以利用 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)来涵盖许可费用。

## <a name="whos-eligible-to-purchase-a-reservation"></a>谁有资格购买预留？

若要购买计划，必须在企业订阅（MS-AZR-0017P 或 MS-AZR-0148P）或即用即付订阅（MS-AZR-0003P 或 MS-AZR-0023P）或 Microsoft 客户协议订阅中具有订阅所有者角色。 云解决方案提供商可使用 Azure 门户或 [合作伙伴中心](/partner-center/azure-reservations)购买 Azure 预留项。

企业协议 (EA) 客户可以通过在 EA 门户中禁用“添加预留实例”选项，将购买权限制为 EA 管理员。  EA 管理员必须是至少一个 EA 订阅的订阅所有者才能购买预留项。 对于希望通过一个核心团队为不同的成本中心购买预留项的企业而言，该选项非常有用。 购买后，核心团队可将成本中心所有者添加到预留项。 然后，所有者可将预留范围限定为其订阅。 购买预留项时，核心团队无需拥有订阅所有者访问权限。

预留折扣仅适用于与通过企业、云解决方案提供商 (CSP)、采用即用即付费率的 Microsoft 客户协议和单个计划购买的订阅关联的资源。

## <a name="scope-reservations"></a>限定预留范围

可将预留范围限定为订阅或资源组。 设置预留范围可以选择要在哪些方面应用预留节省。 将预留范围限定为某个资源组时，预留折扣仅应用到该资源组，而不会应用到整个订阅。

### <a name="reservation-scoping-options"></a>预留范围选项

使用资源组范围时，可以根据需要使用三个选项来限定预留范围：

- **单个资源组范围** - 仅将预留折扣应用到所选资源组中匹配的资源。
- **单个订阅范围** - 将预留折扣应用到所选订阅中匹配的资源。
- **共享范围** - 将预留折扣应用到计费上下文中符合条件的订阅中的匹配资源。 对于企业协议客户，计费上下文为注册。 对于 Microsoft 客户协议客户，计费范围为计费对象信息。 对于采用即用即付费率的单个订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。

对用途应用预留折扣时，Azure 将按以下顺序处理预留：

1. 范围限定为资源组的预留项
2. 单一范围的预留项
3. 共享范围的预留项

根据预留项的限定范围，单个资源组可从多个预留项获得预留折扣。

### <a name="scope-a-reservation-to-a-resource-group"></a>将预留范围限定为资源组

可以在购买预留时将预留范围限定为资源组，也可以在购买后设置范围。 只能订阅所有者才能将预留范围限定为资源组。

若要设置范围，请转到 Azure 门户中的[购买预留项](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand)页。 选择要购买的预留项类型。 在“选择要购买的产品”选项表单中，将“范围”值更改为“单个资源组”。  然后选择一个资源组。

![显示 VM 预留项购买选项的示例](./media/billing-save-compute-costs-reservations/select-product-to-purchase.png)

此时会显示虚拟机预留项中资源组的购买建议。 建议值是通过分析过去 30 天的使用情况计算得出的。 如果运行包含预留实例的资源的成本，比运行采用即用即付费率的资源的成本更低，则会给出购买建议。 有关预留项购买建议的详细信息，请参阅[根据使用模式获取预留实例购买建议](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations)。

始终可在购买预留项后更新范围。 为此，请转到该预留项，单击“配置”，然后重新设置预留范围。  重新设置预留范围不属于商业交易。 预留条款不会有变化。 有关更新范围的详细信息，请参阅[在购买预留项后更新范围](billing-manage-reserved-vm-instance.md#change-the-reservation-scope)。

![演示如何更改预留范围的示例](./media/billing-save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>监视和优化预留项的用量

可通过多种方式监视预留项的用量 - 通过 Azure 门户、API 或使用情况数据。 若要查看你有权访问的所有预留项，请在 Azure 门户中转到“预留项”。  预留项网格显示预留的最新记录的利用率百分比。 单击该预留可查看其长期利用率。

如果是企业协议客户或 Microsoft 客户协议客户，则还可以通过 [API](billing-reservation-apis.md#see-reservation-usage) 和[使用情况数据](billing-understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks)来获取预留项利用率。

如果你发现范围限定为资源组的预留项的利用率偏低，可将预留范围更新为单个订阅，或者在计费上下文中共享该预留项。 还可以拆分预留项，并将最终的预留项应用到不同的资源组。

### <a name="other-considerations"></a>其他注意事项

如果资源组中没有匹配的资源，则预留项会出现利用不足的情况。 存在利用率不足的情况时，预留项不会自动应用到其他资源组或订阅。

如果将资源组从一个订阅移到另一个订阅，预留范围不会自动更新。 如果删除资源组，范围不会更新。 必须[重新设置预留范围](billing-manage-reserved-vm-instance.md#change-the-reservation-scope)。 否则，预留项将出现利用不足的情况。

## <a name="discounted-subscription-and-offer-types"></a>打折的订阅和套餐类型

预留折扣适用于以下符合条件的订阅和套餐类型。

- 企业协议（套餐编号：MS-AZR-0017P 或 MS-AZR-0148P）
- Microsoft 客户协议订阅。
- 采用即用即付费率的单个计划（套餐编号：MS-AZR-0003P 或 MS-AZR-0023P）
- CSP 订阅

订阅中运行的其他产品/服务类型的资源无法获得预订折扣。

## <a name="how-is-a-reservation-billed"></a>预留如何计费？

预留通过与订阅绑定的付款方式付款。 预留成本将从货币承诺余额中扣除（如果可用）。 如果货币承诺余额不足以支付虚拟机预留实例的费用，则还会向你收取所欠费用。 如果你的订阅来自采用即用即付费率的单个计划，将立即从你帐户中的信用卡提前扣取购买费。 每月付款额将显示在发票上，并且每月将从你的信用卡扣费。 如果采用按发票计费的方式，则费用将显示在下一份发票上。

## <a name="how-reservation-discount-is-applied"></a>如何应用预留折扣

预留折扣适用于与购买预留时选择的属性相匹配的资源使用情况。 属性包括匹配的 VM、SQL 数据库、Azure Cosmos DB 或其他资源的运行范围。 例如，如果需要“美国西部”区域四个标准 D2 虚拟机的预留折扣，请选择正在运行的 VM 所在的订阅。

预留折扣的性质是“不用就会失效”  。 如果你在任何小时内没有匹配资源，那么你将丢失该小时的预留数量。 不能结转未使用的预留小时数。

关闭资源时，预留折扣将自动应用于指定范围内的另一个匹配资源。 如果在指定的范围内找不到匹配的资源，则预留小时数将丢失  。

例如，你可能会在以后创建一个资源，并包含一个利用不足的匹配预留项。 预留折扣将自动应用到新的匹配资源。

如果虚拟机是在注册/帐户的不同订阅中运行，则请选择作为共享的范围。 共享范围允许跨订阅应用预留折扣。 可在购买预留后更改范围。 有关详细信息，请参阅[管理 Azure 预订](billing-manage-reserved-vm-instance.md)。

预留折扣仅适用于与企业、Microsoft 客户协议、CSP 或采用即用即付费率的订阅相关联的资源。 订阅中运行的其他产品/服务类型的资源无法获得预订折扣。

## <a name="when-the-reservation-term-expires"></a>当预留期限过期时

预留期限结束时，计费折扣随即过期，资源将按即用即付价格计费。 默认情况下，预留未设置为自动续订。 可以通过在续订设置中选择相应的选项来选择启用预留的自动续订。 如果使用自动续订，则在现有预留到期后，将购买替换预留。 默认情况下，替换预留具有与过期预留相同的属性，你可以选择更改续订设置中的计费频率、期限或数量。 对用于计费的预留和订阅拥有所有者访问权限的任何用户都可以设置续订。  

## <a name="discount-applies-to-different-sizes"></a>折扣适用于不同的大小

购买预订时，折扣可以应用于具有同一大小组中属性的其他实例。 此功能称为“实例大小灵活性”。 折扣覆盖范围的灵活性取决于预订的类型以及购买预订时选择的特性。

服务计划：

- 虚拟机预留实例：购买预留项时，如果选择“针对实例大小灵活性进行优化”  ，则折扣覆盖范围将取决于所选的 VM 大小。 预订可以应用于同一大小系列组中的虚拟机 (VM) 大小。 有关详细信息，请参阅[预订 VM 实例的虚拟机大小灵活性](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)。
- Azure 存储预留容量：你可以购买标准 Azure 存储帐户的预留容量（单位为每月 100 TiB 或 1 PiB）。 适用于任何访问层（热、冷或存档）和任何复制选项（LRS、GRS 或 ZRS）的所有区域中均提供 Azure 存储预留容量。
- SQL 数据库预留容量：折扣覆盖范围取决于所选的性能层。 有关详细信息，请参阅[了解如何应用 Azure 预订折扣](billing-understand-reservation-charges.md)。
- Azure Cosmos DB 预留容量：折扣覆盖范围取决于预配的吞吐量。 有关详细信息，请参阅[了解如何应用 Azure Cosmos DB 预订折扣](billing-understand-cosmosdb-reservation-charges.md)。

## <a name="reservation-notifications"></a>预留通知

根据 Azure 订阅的付款方式，我们将向组织中的以下用户发送预留电子邮件通知。 我们会针对各种事件发送通知，包括：

- 购买
- 预留即将过期
- Expiry
- 续订
- 取消
- 范围更改

对于使用 EA 订阅的客户：
- 购买通知将发送到购买者和 EA 通知联系人。
- 其他预留生命周期通知只会发送到 EA 通知联系人。
- 使用 RBAC (IAM) 权限添加到预留项的用户不会收到任何电子邮件通知。

对于使用单个订阅的客户：
- 购买者会收到购买通知。
- 在购买时，订阅计费帐户所有者将收到购买通知。
- 帐户所有者将收到所有其他通知。


## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
    - [管理 Azure 预留项](billing-manage-reserved-vm-instance.md)
    - [了解采用即用即付费率的订阅的预留项使用情况](billing-understand-reserved-instance-usage.md)
    - [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
    - [预留未包含的 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)
    - [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](/partner-center/azure-reservations)

- 详细了解服务计划的预留项：
    - [包含 Azure 虚拟机预留实例的虚拟机](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [具有 Azure Cosmos DB 预留容量的 Azure Cosmos DB 资源](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [具有 Azure SQL 数据库预留容量的 SQL 数据库计算资源](../sql-database/sql-database-reserved-capacity.md)详细了解软件计划的预留项：
    - [Azure 预留项中的 Red Hat 软件计划](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Azure 预留项中的 SUSE 软件计划](../virtual-machines/linux/prepay-suse-software-charges.md)
