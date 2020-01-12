---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/21/2019
ms.openlocfilehash: 9bed0a47c6c13b3c209f593509b71aa697c6eb4a
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901842"
---
当你提交到 Azure 保留 VM 实例时，你可以节省资金。 预订折扣将自动应用于正在运行的与预订范围和属性匹配的虚拟机数量。 无需为虚拟机分配预订即可获取折扣。 购买的预订实例仅涵盖 VM 使用率的计算部分。 对于 Windows Vm，使用情况计量分为两个不同的计量器。 有一个计算计量器，与 Linux 计量和 Windows IP 指标相同。 进行购买时看到的费用仅适用于计算成本。 费用不包括 Windows 软件费用。 有关软件成本的详细信息，请参阅[Azure 保留 VM 实例中未包含的软件成本](../articles/billing/billing-reserved-instance-windows-software-costs.md)。

## <a name="determine-the-right-vm-size-before-you-buy"></a>在购买前确定正确的 VM 大小

在购买预订之前，应确定所需的 VM 大小。 以下部分将帮助你确定正确的 VM 大小。

### <a name="use-reservation-recommendations"></a>使用预订建议

你可以使用预订建议来帮助确定你应购买的预订。

- 在 Azure 门户中购买 VM 预留实例时，将显示 "购买建议" 和 "推荐数量"。
- Azure 顾问为各个订阅提供购买建议。  
- 可以使用 Api 获取共享范围和单个订阅范围的购买建议。 有关详细信息，请参阅[保留实例购买适用于企业客户的建议 api](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)。
- 对于企业协议（EA）和 Microsoft 客户协议（MCA）客户， [Azure 使用见解 Power BI 内容包](/power-bi/service-connect-to-azure-consumption-insights)提供了针对共享订阅范围和单一订阅范围的购买建议。

### <a name="services-that-get-vm-reservation-discounts"></a>可获得 VM 保留折扣的服务

VM 预留可以应用于从多个服务发出的 VM 使用量 - 而不仅应用于 VM 部署。 可获得预留折扣的资源会根据实例大小灵活性设置而变化。

#### <a name="instance-size-flexibility-setting"></a>实例大小灵活性设置

实例大小灵活性设置确定哪些服务可获得预留实例折扣。

无论该设置是打开还是关闭，预留折扣在 *ConsumedService* 为 `Microsoft.Compute` 时会自动应用到任何匹配的 VM 使用量。 因此，请检查使用情况数据中的 ConsumedService 值。 示例包括：

- 虚拟机
- 虚拟机规模集
- 容器服务
- Azure Batch 部署（在用户订阅模式下）
- Azure Kubernetes 服务 (AKS)
- Service Fabric

打开此设置后，当 ConsumedService 为以下任一项时，预留折扣将自动应用于匹配的 VM 使用量：

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

检查使用情况数据中的 ConsumedService 值，以确定该使用是否符合预留折扣的条件。

有关实例大小灵活性的详细信息，请参阅[虚拟机预留实例的虚拟机大小灵活性](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md)。

### <a name="analyze-your-usage-information"></a>分析使用情况信息
分析你的使用情况信息，以帮助确定你应购买哪些预订。

使用情况数据在使用文件和 Api 中可用。 将它们一起用于确定要购买的预订。 检查每日具有高使用率的 VM 实例，以确定要购买的预订数量。

避免使用情况数据中的 `Meter` 子类别和 `Product` 字段。 它们不区分使用高级存储的 VM 大小。 如果你使用这些字段来确定用于预订购买的 VM 大小，则可能购买的大小不正确。 然后，你将不会获得预期的预订折扣。 相反，请参阅使用情况文件或使用情况 API 中的 `AdditionalInfo` 字段，以确定正确的 VM 大小。

### <a name="purchase-restriction-considerations"></a>购买限制注意事项

保留的 VM 实例适用于大多数 VM 大小，但有一些例外情况。 预订折扣不适用于以下 Vm：

- **VM 系列**-A 系列、Av2 系列或 G 系列。

- **预览或促销 vm** -任何采用预览版或使用促销计量的 vm 系列或大小。

- **云**预订不适用于德国或中国地区的购买。

- **配额不足**-作用于单个订阅的保留必须在新 RI 的订阅中具有 vCPU 配额。 例如，如果目标订阅的配额限制为 10 个 vCPU（适用于 D 系列），则不能为 11 个 Standard_D1 实例购买预留。 虚拟机预留实例的配额检查包括已在订阅中部署的 VM。 例如，如果该订阅的配额为针对 D 系列购买 10 个 vCPU，并且已部署两个 Standard_D1 实例，则可在该订阅中为 10 个 Standard_D1 实例购买预留。 你可以[创建报价增加请求](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md)以解决此问题。

- **容量限制**-在极少数情况下，Azure 会限制购买 VM 大小子集的新保留，因为区域中的容量不足。

## <a name="buy-a-reserved-vm-instance"></a>购买虚拟机预留实例

可以在[Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)中购买保留 VM 实例。 通过[提前付款或按月付款](../articles/billing/billing-monthly-payments-reservations.md)的方式为预留付款。
这些要求适用于购买保留 VM 实例：

- 对于至少一个 EA 订阅或具有即用即付费率的订阅，必须是所有者角色。
- 对于 EA 订阅，必须在[ea 门户](https://ea.azure.com/)中启用 "**添加保留实例**" 选项。 或者，如果禁用此设置，则必须是订阅的 EA 管理员。
- 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理才能购买预留。

若要购买实例：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 选择“所有服务” > “预订”。
1. 选择 "**添加**" 购买新的预订，然后单击 "**虚拟机**"。
1. 输入必填字段。 与所选属性匹配的正在运行的 VM 实例有资格获得预订折扣。 实际获得折扣的 VM 实例数取决于所选范围和数量。

如果你有 EA 协议，则可以使用 "**添加更多" 选项**快速添加其他实例。 选项不适用于其他订阅类型。


| 字段      | Description|
|------------|--------------|
|订阅|用于支付预订费用的订阅。 订阅的付款方式将按预订费用收费。 订阅类型必须是企业协议（产品/服务编号： BC-OP-NT-AZR-Ms-azr-0017p 或 BC-OP-NT-AZR-Ms-azr-0148p）或 Microsoft 客户协议，或者使用即用即付费率的单个订阅（产品/服务编号： MS-BC-OP-NT-AZR-Ms-azr-0003p 或-bc-op-nt-azr）。 将从货币承诺余额中扣除费用（如果可用），或按超额计费。 对于使用即用即付费率的订阅，将对订阅上的信用卡或发票付款方式收取费用。|    
|范围       |预订的范围可以包含一个订阅或多个订阅（共享范围）。 如果选择： <ul><li>**单个资源组范围** - 仅将预留折扣应用到所选资源组中匹配的资源。</li><li>**单个订阅范围** - 将预留折扣应用到所选订阅中匹配的资源。</li><li>**共享范围** - 将预留折扣应用到计费上下文中符合条件的订阅中的匹配资源。 对于 EA 客户，计费上下文为 "注册"。 对于采用即用即付费率的单个订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。</li></ul>|
|地区    |预订涵盖的 Azure 区域。|    
|VM 大小     |VM 实例的大小。|
|优化对象     |默认情况下选择了 VM 实例大小灵活性。 单击 "**高级设置**" 以更改实例大小的灵活性值，以将预订折扣应用于同一[VM 大小组](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md)中的其他 vm。 容量优先级可以对部署优先使用数据中心容量。 它可让你在需要时启动 VM 实例，从而提供更多的信心。 仅当预留范围为单个订阅时，容量优先级才可用。 |
|条款        |一年或三年。|
|数量    |预订中购买的实例数。 数量是可以获得计费折扣的正在运行的 VM 实例数。 例如，如果你在美国东部运行10个 Standard_D2 Vm，则可将数量指定为10，以最大程度地提高所有正在运行的 Vm 的权益。 |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>使用情况数据和预订利用率

对于获得预留折扣的使用量，使用数据的有效价格为零。 你可以看到哪个 VM 实例收到每个预订的预订折扣。

有关如何在使用情况数据中显示预订折扣的详细信息，请参阅[了解企业注册的 Azure 保留使用情况](../articles/billing/billing-understand-reserved-instance-usage-ea.md)（如果你是 EA 客户）。 如果有单独的订阅，请参阅[了解即用即付订阅的 Azure 保留使用情况](../articles/billing/billing-understand-reserved-instance-usage.md)。

## <a name="change-a-reservation-after-purchase"></a>在购买后更改保留

可以在购买后对预留进行以下类型的更改：

- 更新预留范围
- 实例大小灵活性（如果适用）
- 所有权

还可以将保留拆分为较小的区块，并合并已拆分的保留项。 任何更改都不会导致新的商业交易，也不会更改预订的结束日期。

在购买后，不能进行以下类型的更改：

- 现有预订的区域
- SKU
- 数量
- 持续时间

不过，如果想要进行更改，则可以*交换*预订。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何管理预留，请参阅[管理 Azure 预留](../articles/billing/billing-manage-reserved-vm-instance.md)。
- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
    - [什么是 Azure 预订？](../articles/billing/billing-save-compute-costs-reservations.md)
    - [管理 Azure 中的预留](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [了解预留折扣的应用方式](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [了解采用即用即付费率的订阅的预留使用情况](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [了解企业合约的预留使用情况](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [预留未包含的 Windows 软件成本](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](https://docs.microsoft.com/partner-center/azure-reservations)
