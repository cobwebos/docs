---
title: 什么是 Azure 预留项？
description: 了解 Azure 预订和定价以节省虚拟机、SQL 数据库、Azure Cosmos DB 和其他资源的成本。
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: banders
ms.openlocfilehash: cd0a70aa0fb5096c5b0157ae078c961da03109bc
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565317"
---
# <a name="what-are-azure-reservations"></a>什么是 Azure 预留项？

通过预付一年或三年的虚拟机、SQL 数据库计算容量、Azure Cosmos DB 吞吐量或其他 Azure 资源的费用，Azure 预订可为你节省资金。 通过预付，能够以折扣价购买所用资源。 预订可将虚拟机、SQL 数据库计算、Azure Cosmos DB 或其他资源的成本大幅减少至即用即付价格的 72%。 预订提供计费折扣，并且不会影响资源的运行时状态。

可在 [Azure 门户](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)中购买预留。

## <a name="why-buy-a-reservation"></a>为什么购买预订吗？

如果有虚拟机、 Azure Cosmos DB 或长时间运行的 SQL 数据库，购买预订提供最经济实惠的选项。 例如，在持续运行四个没有预留容量的服务实例时，我们即用即付费率收费。 如果你购买保留这些资源，你立即获得预订折扣。 这些资源不再按即用即付费率收费。

## <a name="charges-covered-by-reservation"></a>预订涵盖的费用

服务计划：

- **保留虚拟机实例**-预订仅涵盖虚拟机计算成本。 而不涵盖软件、网络或存储等其他费用。
- **Azure Cosmos DB 保留容量**-预订涵盖资源的预配的吞吐量。 但它不涵盖存储和网络费用。
- **SQL 数据库保留 vCore** -仅计算成本包括在内的预订。 许可证单独计费。

对于 Windows 虚拟机和 SQL 数据库，可以利用 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)来涵盖许可费用。

## <a name="whos-eligible-to-purchase-a-reservation"></a>谁有资格购买预留？

若要购买计划，必须具有 （MS-条-0017 P 或 MS-条-0148 P） 的企业或即用即付订阅 （MS-条-003 P 或 MS-条-0023 P） 中的订阅所有者角色。 云解决方案提供商可以使用 Azure 门户或 [合作伙伴中心](/partner-center/azure-reservations) 购买 Azure 保留项。

EA 客户可以通过禁用限制到 EA 管理员购买**添加保留实例**EA 门户中的选项。 EA 管理员必须至少一个 EA 订阅是订阅所有者才能购买预订。 选项为适用于想要购买的不同的成本中心保留一个集中式的团队企业非常有用。 在购买集中式的团队可以将成本中心所有者添加到保留项。 所有者可以然后范围限定到自己的订阅保留。 中心团队不需要购买预订时具有订阅所有者的访问。

预订折扣仅适用于与通过企业、 CSP 和即用即付费率与各个计划购买的订阅关联的资源。

## <a name="scope-reservations"></a>作用域保留

可以将预订与订阅或资源组。 设置为预订范围选择保留节省在何处应用。 当作用域将预订与资源组时，请保留折扣仅适用于资源组，不是整个订阅。

### <a name="reservation-scoping-options"></a>保留范围选项

与资源组范围您具有三种方法，作用域保留项，具体取决于你的需求：

- **单一资源组范围**— 将预订折扣应用于在所选的资源组中匹配的资源。
- **单一订阅范围内**— 将预订折扣应用于所选订阅中匹配的资源。
- **共享范围**-预订折扣适用于匹配符合条件的订阅中计费上下文中的资源。 对于企业协议客户，计费上下文是注册。 对于现用现付费率使用单独的订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。

同时将预订折扣应用使用情况，Azure 处理按以下顺序保留：

1. 保留已划归到资源组
2. 单个作用域保留项
3. 共享作用域保留

单个资源组可以从多个保留项，具体取决于你的预订的范围获取预订折扣。

### <a name="scope-a-reservation-to-a-resource-group"></a>作用域保留到资源组

购买预订，或在购买之后设置的作用域时，可以限制范围将预订与资源组。 您必须是订阅所有者才能作用域将预订与资源组。

若要设置的作用域，请转到[购买预订](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand)在 Azure 门户中的页。 然后，选择你想要购买的保留项类型。 上**选择你想要购买的产品**选择窗体中，更改**作用域**值设为**单个资源组**并选择资源组。

![显示 VM 预订购买选择内容的示例](./media/billing-save-compute-costs-reservations/select-product-to-purchase.png)

显示在虚拟机保留的资源组的购买建议。 建议将通过你的使用情况分析过去 30 天计算。 如果使用保留的实例中运行资源的成本是使用即用即付费率运行资源的成本比更便宜，进行购买建议。 有关预订购买建议的详细信息，请参阅[获得保留实例购买建议基于使用情况模式](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations)博客文章。

购买预订后，您始终可以更新作用域。 若要执行此操作，请转到预订，单击**配置**和会重新确定预订。 Rescoping 保留不是商业事务。 预订期不会更改。 有关更新作用域的详细信息，请参阅[购买预订后更新范围](billing-manage-reserved-vm-instance.md#change-the-reservation-scope)。

![示例显示预订范围更改](./media/billing-save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>监视和优化预订使用情况

你可以监视多个方法 — 通过 Azure 门户、 Api，或使用情况数据在预订使用情况。 若要查看所有必须的保留访问权限，请转到**预订**在 Azure 门户中。 保留项网格将显示预订的最后一个记录的使用率百分比。 单击要查看长期保留的利用率的保留。

此外可以获取预订利用率使用[Api](billing-reservation-apis.md#see-reservation-usage)并从你[使用情况数据](billing-understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks)如果你是企业协议客户。

如果您注意到你的资源组的使用率作用域保留不足，则可以更新预订范围单一订阅或计费上下文中共享。 此外可以将该预订拆分，并将生成的保留项应用于不同的资源组。

### <a name="other-considerations"></a>其他注意事项

如果没有匹配的资源组中的资源，然后预订将没有得到充分利用。 不会自动将预订应用于不同的资源组或订阅的利用率较低。

如果从一个订阅的资源组移动到另一个，不会自动更新预订范围。 必须将会重新确定预订。 否则，将充分利用预订。

## <a name="discounted-subscription-and-offer-types"></a>折扣的订阅和产品/服务类型

预订折扣应用于以下符合条件的订阅和产品/服务类型。

- 企业协议 (产品/服务编号：MS-AZR-0017P 或 MS-条-0148 P)
- 与现用现付费率的各个计划 (产品/服务编号：MS-AZR-0003P 或 MS-条-0023 P)
- CSP 订阅

订阅中运行的其他产品/服务类型的资源无法获得预订折扣。

## <a name="how-is-a-reservation-billed"></a>预留如何计费？

预留通过与订阅绑定的付款方式付款。 如果订阅为“企业”订阅，则虚拟机预留实例费用会从货币承诺余额中扣除。 如果货币承诺余额不足以支付虚拟机预留实例的费用，则还会向你收取所欠费用。 如果必须使用现用现付费率的单个计划的订阅，将立即计费信用卡对你的帐户。 如果采用账单付费的方式，则费用显示在下次的账单上。

## <a name="how-reservation-discount-is-applied"></a>如何应用预订折扣

预订折扣适用于匹配您在购买预订时选择的属性的资源使用情况。 这些特性包括匹配的 VM、SQL 数据库、Azure Cosmos DB 或其他资源的运行范围。 例如，如果希望将预订折扣为美国西部区域中的四个标准 D2 虚拟机，然后选择在其中运行 Vm 的订阅。

预订折扣是"*使用-it-或-丢失-it*"。 如果没有匹配的任何小时内的资源，然后将该小时的丢失预订数量。 不能执行将转发未使用保留的小时数。

在关闭资源时，将预订折扣将自动应用于指定范围内的另一个匹配的资源。 如果没有匹配找到资源在指定范围内，则保留的时间为*丢失*。

例如，可能更高版本创建资源，并具有使用不足的匹配保留。 在此示例中，预订折扣会自动适用于新匹配的资源。

如果虚拟机是在注册/帐户的不同订阅中运行，则请选择作为共享的范围。 共享范围允许跨订阅应用预留折扣。 可在购买预留后更改范围。 有关详细信息，请参阅[管理 Azure 预订](billing-manage-reserved-vm-instance.md)。

预订折扣仅适用于企业，CSP，与关联的资源，或使用付款现订阅转费率。 订阅中运行的其他产品/服务类型的资源无法获得预订折扣。

## <a name="when-the-reservation-term-expires"></a>预订期到期时

预订期限结束时，计费折扣随即过期，并且虚拟机、SQL 数据库、Azure Cosmos DB 或其他资源将按即用即付的价格计费。 Azure 预订不会自动续订。 若要继续获得计费折扣，必须为符合条件的服务和软件购买新的预订。

## <a name="discount-applies-to-different-sizes"></a>折扣适用于不同的大小

购买预订时，折扣可以应用于具有同一大小组中属性的其他实例。 此功能被称为实例大小灵活性。 折扣覆盖范围的灵活性取决于预订的类型以及购买预订时选择的特性。

服务计划：

- 虚拟机预留实例：在购买预订并选中**针对优化**:**实例大小灵活性**，折扣覆盖率取决于你选择的 VM 大小。 预订可以应用于同一大小系列组中的虚拟机 (VM) 大小。 有关详细信息，请参阅[预订 VM 实例的虚拟机大小灵活性](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)。
- SQL 数据库预留容量：折扣覆盖范围取决于所选的性能层。 有关详细信息，请参阅[了解如何应用 Azure 预订折扣](billing-understand-reservation-charges.md)。
- Azure Cosmos DB 预留容量：折扣覆盖范围取决于预配的吞吐量。 有关详细信息，请参阅[了解如何应用 Azure Cosmos DB 预订折扣](billing-understand-cosmosdb-reservation-charges.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
    - [管理 Azure 预留项](billing-manage-reserved-vm-instance.md)
    - [了解有关在订阅中使用即用即付费率的预订使用情况](billing-understand-reserved-instance-usage.md)
    - [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
    - [预留未包含的 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)
    - [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](/partner-center/azure-reservations)

- 了解有关服务计划的保留项的详细信息：
    - [使用 Azure 保留的 VM 实例的虚拟机](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [使用 Azure Cosmos DB 的 azure Cosmos DB 资源保留容量](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [使用 Azure SQL 数据库的 SQL 数据库计算资源保留容量](../sql-database/sql-database-reserved-capacity.md)了解有关软件计划的保留项的详细信息：
    - [Azure 保留项中的 Red Hat 软件计划](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [从 Azure 预订 SUSE 软件计划](../virtual-machines/linux/prepay-suse-software-charges.md)
