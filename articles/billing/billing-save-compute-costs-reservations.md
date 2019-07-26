---
title: 什么是 Azure 预留项？
description: 了解 Azure 保留和定价, 以节省虚拟机、SQL 数据库、Azure Cosmos DB 和其他资源成本。
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: banders
ms.openlocfilehash: c63447ff0a3329e0cc0dc0605984ae4f26e9c25f
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359232"
---
# <a name="what-are-azure-reservations"></a>什么是 Azure 预留项？

通过预付一年或三年的虚拟机、SQL 数据库计算容量、Azure Cosmos DB 吞吐量或其他 Azure 资源的费用，Azure 预订可为你节省资金。 通过预付，能够以折扣价购买所用资源。 预订可将虚拟机、SQL 数据库计算、Azure Cosmos DB 或其他资源的成本大幅减少至即用即付价格的 72%。 预订提供计费折扣，并且不会影响资源的运行时状态。

可在 [Azure 门户](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)中购买预留。

## <a name="why-buy-a-reservation"></a>为什么要购买预订？

如果有很长一段时间运行的虚拟机、Azure Cosmos DB 或 SQL 数据库, 则购买保留将提供最经济实惠的选项。 例如, 当你连续运行四个服务实例而不进行保留时, 将按现用现付费率对你收费。 购买这些资源的预订后, 会立即获得预订折扣。 这些资源不再按即用即付费率收费。

## <a name="charges-covered-by-reservation"></a>预订涵盖的费用

服务计划：

- **保留虚拟机实例**-预订仅涵盖虚拟机计算成本。 而不涵盖软件、网络或存储等其他费用。
- **Azure Cosmos DB 保留容量**-预留包含为资源预配的吞吐量。 但它不涵盖存储和网络费用。
- **SQL Database Reserved vCore** -预留只包含计算成本。 许可证单独计费。

对于 Windows 虚拟机和 SQL 数据库，可以利用 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)来涵盖许可费用。

## <a name="whos-eligible-to-purchase-a-reservation"></a>谁有资格购买预留？

若要购买计划, 必须在企业中拥有订阅所有者角色 (BC-OP-NT-AZR-Ms-azr-0017p 或 BC-OP-NT-AZR-Ms-azr-0148p) 或即用即付订阅 (MS-BC-OP-NT-AZR-003P 或 MS-bc-op-nt-azr-0023P)。 云解决方案提供商可以使用 Azure 门户或 [合作伙伴中心](/partner-center/azure-reservations) 来购买 Azure 预订。

企业协议 (EA) 客户可以将购买限制为 EA 管理员。 它们通过在 EA 门户中禁用 "**添加保留实例**" 选项来执行此操作。 EA 管理员必须是至少一个 EA 订阅的订阅所有者才能购买预订。 如果企业需要集中式团队购买不同成本中心的预订, 则该选项非常有用。 购买后, 集中式团队可以将成本中心所有者添加到预订中。 然后, 所有者可以将预订的范围限定为其订阅。 中心团队无需拥有订阅所有者访问权限, 即可购买预订。

预订折扣仅适用于与通过企业、云解决方案提供商 (CSP) 购买的订阅相关联的资源, 以及使用现用现付费率的各个计划。

## <a name="scope-reservations"></a>作用域预订

可以将预订的范围限定为订阅或资源组。 设置预订的作用域会选择保留储蓄的位置。 将预订范围限定为资源组时, 预订折扣仅适用于资源组, 而不是整个订阅。

### <a name="reservation-scoping-options"></a>预订作用域选项

使用资源组作用域时, 可以根据需要使用三个选项来确定保留范围:

- **单个资源组范围**-仅将预订折扣应用于所选资源组中的匹配资源。
- **单个订阅范围**-将预订折扣应用于所选订阅中的匹配资源。
- **共享作用域**-将预订折扣应用于计费上下文中符合条件的订阅中的匹配资源。 对于企业协议客户, 计费上下文是注册。 对于使用即用即付费率的各个订阅, 计费范围是由帐户管理员创建的所有符合条件的订阅。

应用预订折扣时, Azure 会按以下顺序处理预留:

1. 作用域为资源组的保留项
2. 单范围预留
3. 共享作用域预订

单个资源组可以根据预订的范围, 从多个预订获得保留折扣。

### <a name="scope-a-reservation-to-a-resource-group"></a>将保留范围限定为资源组

你可以在购买预订时将预留范围限定为资源组, 或者在购买后设置作用域。 你必须是订阅所有者才能将保留范围限定为资源组。

若要设置作用域, 请参阅 Azure 门户中的 "[购买保留](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand)" 页。 选择要购买的预订类型。 在 "**选择要购买的产品**" 选项窗体上, 将 "作用域" 值更改为 "单个资源组"。 然后, 选择一个资源组。

![显示 VM 保留购买选择的示例](./media/billing-save-compute-costs-reservations/select-product-to-purchase.png)

将显示虚拟机保留中资源组的购买建议。 建议是通过分析过去30天内的使用情况来计算的。 如果运行具有预订实例的资源的成本比使用即用即付费率运行资源的成本更低, 则会进行购买建议。 有关预订购买建议的详细信息, 请参阅[根据使用模式获取保留实例购买建议](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations)。

在购买预订后, 你始终可以更新作用域。 为此, 请依次单击 "**配置**" 和 "rescope", 然后单击 "保留"。 支付餐费保留不是商业交易。 你的预订条款未更改。 有关更新作用域的详细信息, 请参阅[在购买保留后更新作用域](billing-manage-reserved-vm-instance.md#change-the-reservation-scope)。

![显示保留范围更改的示例](./media/billing-save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>监视和优化保留使用情况

可以通过多种方式监视保留使用情况-通过 Azure 门户、通过 Api 或通过使用情况数据。 若要查看你有权访问的所有预订, 请在 Azure 门户中进行**保留**。 预订网格显示预订的最后记录的利用率百分比。 单击预订以查看预订的长期使用量。

如果您是企业协议客户, 还可以使用[api](billing-reservation-apis.md#see-reservation-usage)和[使用情况数据](billing-understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks)来获取预订利用率。

如果你注意到资源组范围内的保留使用率较低, 则可以将预订范围更新为单个订阅或在计费上下文中共享。 你还可以拆分预订, 并将生成的预订应用于不同的资源组。

### <a name="other-considerations"></a>其他注意事项

如果资源组中没有匹配的资源, 则预留空间将使用不足。 预订不会自动应用于使用率低的其他资源组或订阅。

如果将资源组从一个订阅移到另一个订阅, 则保留范围不会自动更新。 如果删除资源组, 则该范围不会更新。 必须[rescope 预留](billing-manage-reserved-vm-instance.md#change-the-reservation-scope)。 否则, 预订将被充分利用。

## <a name="discounted-subscription-and-offer-types"></a>折扣订阅和产品/服务类型

预订折扣适用于以下合格订阅和产品/服务类型。

- 企业协议 (产品/服务编号:BC-OP-NT-AZR-Ms-azr-0017p 或 BC-OP-NT-AZR-Ms-azr-0148p)
- 采用即用即付费率的单个计划 (产品/服务编号:BC-OP-NT-AZR-Ms-azr-0003p 或 BC-OP-NT-AZR-0023P)
- CSP 订阅

订阅中运行的其他产品/服务类型的资源无法获得预订折扣。

## <a name="how-is-a-reservation-billed"></a>预留如何计费？

预留通过与订阅绑定的付款方式付款。 如果订阅为“企业”订阅，则虚拟机预留实例费用会从货币承诺余额中扣除。 当您的资金承诺余额不会涵盖预订费用时, 您将对超额收费。 如果你有来自具有即用即付费率的单个计划的订阅, 则将立即对你的帐户中的信用卡计费。 当你通过发票进行计费时, 你将看到下一张发票上的费用。

## <a name="how-reservation-discount-is-applied"></a>如何应用预订折扣

预订折扣适用于在购买预订时所选属性匹配的资源使用情况。 属性包括匹配的 Vm、SQL 数据库、Azure Cosmos DB 或其他资源运行的作用域。 例如, 如果想要在 "美国西部" 区域中为四个标准 D2 虚拟机提供预订折扣, 请选择运行 Vm 的订阅。

预订折扣是 "*it 使用-或丢失*"。 如果你没有任何小时的匹配资源, 则会丢失该小时的预订量。 不能结转未使用的保留小时数。

关闭资源时, 预订折扣将自动应用于指定范围内的另一个匹配资源。 如果在指定的范围内找不到任何匹配资源, 则保留的时间将*丢失*。

例如, 你可能会在以后创建一个资源, 并且具有使用不足的匹配保留项。 预订折扣将自动应用于新的匹配资源。

如果虚拟机是在注册/帐户的不同订阅中运行，则请选择作为共享的范围。 共享范围允许跨订阅应用预留折扣。 可在购买预留后更改范围。 有关详细信息，请参阅[管理 Azure 预订](billing-manage-reserved-vm-instance.md)。

预订折扣仅适用于与企业、CSP 或订阅相关联的资源, 这些资源使用即用即付费率。 订阅中运行的其他产品/服务类型的资源无法获得预订折扣。

## <a name="when-the-reservation-term-expires"></a>保留期到期时

在保留期结束时, 计费折扣过期。 虚拟机、SQL 数据库、Azure Cosmos DB 或其他资源按即用即付价格计费。 Azure 预订不会自动续订。 若要继续获得计费折扣，必须为符合条件的服务和软件购买新的预订。

## <a name="discount-applies-to-different-sizes"></a>折扣适用于不同大小

购买预订时，折扣可以应用于具有同一大小组中属性的其他实例。 此功能称为实例大小灵活性。 折扣覆盖范围的灵活性取决于预订的类型以及购买预订时选择的特性。

服务计划：

- 虚拟机预留实例：当你购买保留并选择针对**实例大小灵活性进行优化**时, 折扣覆盖将取决于你选择的 VM 大小。 预订可以应用于同一大小系列组中的虚拟机 (VM) 大小。 有关详细信息，请参阅[预订 VM 实例的虚拟机大小灵活性](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)。
- SQL 数据库预留容量：折扣覆盖范围取决于所选的性能层。 有关详细信息，请参阅[了解如何应用 Azure 预订折扣](billing-understand-reservation-charges.md)。
- Azure Cosmos DB 预留容量：折扣覆盖范围取决于预配的吞吐量。 有关详细信息，请参阅[了解如何应用 Azure Cosmos DB 预订折扣](billing-understand-cosmosdb-reservation-charges.md)。

## <a name="reservation-notifications"></a>预订通知

根据你对 Azure 订阅的费用, 我们将向你组织中的以下用户发送电子邮件预订通知。 针对各种事件发送通知, 包括:

- 购买
- 即将到期的保留期
- 到期
- 续租
- 取消
- 范围更改

对于具有 EA 订阅的客户:
- 采购通知将发送到买方和 EA 通知联系人。
- 其他预订生命周期通知只发送到 EA 通知联系人。
- 使用 RBAC (IAM) 权限添加到预订的用户不会收到任何电子邮件通知。

对于具有单独订阅的客户:
- 买方收到购买通知。
- 在购买时, 订阅计费帐户所有者将收到购买通知。
- 帐户所有者接收所有其他通知。


## <a name="need-help-contact-us"></a>需要帮助? 请联系我们。

如果你有疑问或需要帮助, 请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
    - [管理 Azure 预留项](billing-manage-reserved-vm-instance.md)
    - [使用即用即付费率了解订阅的保留使用情况](billing-understand-reserved-instance-usage.md)
    - [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
    - [预留未包含的 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)
    - [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](/partner-center/azure-reservations)

- 详细了解服务计划的预留:
    - [具有 Azure 保留 VM 实例的虚拟机](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Azure Cosmos DB 具有 Azure Cosmos DB 保留容量的资源](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [具有 AZURE Sql 数据库保留容量的 SQL 数据库计算资源](../sql-database/sql-database-reserved-capacity.md)了解有关软件计划的预订的详细信息:
    - [Azure 预订的 Red Hat 软件计划](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [来自 Azure 预订的 SUSE 软件计划](../virtual-machines/linux/prepay-suse-software-charges.md)
