---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/27/2020
ms.openlocfilehash: d41affc55134ad34c325c12ab4a14f4013c58f9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371670"
---
提交到 Azure 保留 VM 实例时，可以节省资金。 预订折扣将自动应用于正在运行的与预订范围和属性匹配的虚拟机数量。 您无需为虚拟机分配预留来获得折扣。 预留实例购买仅涵盖 VM 使用的计算部分。 对于 Windows VM，使用仪表分为两个单独的仪表。 有一个计算仪表，它和Linux仪表相同，还有一个Windows IP仪表。 购买时看到的费用仅适用于计算成本。 费用不包括 Windows 软件成本。 有关软件成本的详细信息，请参阅[Azure 预留 VM 实例中未包括的软件成本](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md)。

## <a name="determine-the-right-vm-size-before-you-buy"></a>在购买前确定正确的 VM 大小

在购买预留之前，应确定所需的 VM 的大小。 以下各节将帮助您确定正确的 VM 大小。

### <a name="use-reservation-recommendations"></a>使用预订建议

您可以使用预订建议来帮助确定应购买的预订。

- 在 Azure 门户中购买 VM 预留实例时，将显示购买建议和建议的数量。
- Azure 顾问为各个订阅提供购买建议。  
- 您可以使用 API 获取共享范围和单个订阅作用域的购买建议。 有关详细信息，请参阅[为企业客户的预留实例购买建议 API。](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)
- 对于企业协议 （EA） 和 Microsoft 客户协议 （MCA） 客户[，Azure 消耗洞察 Power BI 内容包](/power-bi/service-connect-to-azure-consumption-insights)提供了共享和单个订阅范围的采购建议。

### <a name="services-that-get-vm-reservation-discounts"></a>可获得 VM 保留折扣的服务

VM 预留可以应用于从多个服务发出的 VM 使用量 - 而不仅应用于 VM 部署。 可获得预留折扣的资源会根据实例大小灵活性设置而变化。

#### <a name="instance-size-flexibility-setting"></a>实例大小灵活性设置

实例大小灵活性设置确定哪些服务可获得预留实例折扣。

无论该设置是打开还是关闭，预留折扣在 *ConsumedService* 为 `Microsoft.Compute` 时会自动应用到任何匹配的 VM 使用量。 因此，请检查使用情况数据中的 ConsumedService** 值。 示例包括：

- 虚拟机
- 虚拟机规模集
- 容器服务
- Azure Batch 部署（在用户订阅模式下）
- Azure Kubernetes 服务 (AKS)
- Service Fabric

打开此设置后，当 ConsumedService** 为以下任一项时，预留折扣将自动应用于匹配的 VM 使用量：

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

检查使用情况数据中的 ConsumedService** 值，以确定该使用是否符合预留折扣的条件。

有关实例大小灵活性的详细信息，请参阅[虚拟机预留实例的虚拟机大小灵活性](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md)。

### <a name="analyze-your-usage-information"></a>分析使用情况信息

分析使用情况信息，以帮助确定应购买哪些预订。 使用情况数据在使用情况文件和 API 中可用。 将它们一起使用以确定购买哪个预订。 检查每天使用率高的 VM 实例，以确定要购买的预留数量。 避免使用`Meter`数据中的子`Product`类别和字段。 它们不区分使用高级存储的 VM 大小。 如果您使用这些字段来确定用于预留购买的 VM 大小，则可能会购买错误的大小。 然后，您将得不到您期望的预订折扣。 相反，请参阅使用文件`AdditionalInfo`或使用情况 API 中的字段以确定正确的 VM 大小。

您的使用情况文件按计费周期和每日使用情况显示您的费用。 有关下载使用情况文件的信息，请参阅[查看和下载 Azure 使用情况和费用](../articles/cost-management-billing/understand/download-azure-daily-usage.md)。 然后，通过使用使用情况文件信息，您可以[确定要购买哪些预留](../articles/cost-management-billing/reservations/determine-reservation-purchase.md)。

### <a name="purchase-restriction-considerations"></a>购买限制注意事项

保留 VM 实例可用于大多数 VM 大小，但有一些例外。 预订折扣不适用于以下 VM：

- **VM 系列**- A 系列、Av2 系列或 G 系列。

- **预览或促销 VM** - 预览或使用促销仪表的任何 VM 系列或大小。

- **云**- 预订不适用于在德国或中国地区购买。

- **配额不足**- 限定为单个订阅的预留必须在新 RI 的订阅中具有 vCPU 配额。 例如，如果目标订阅的配额限制为 10 个 vCPU（适用于 D 系列），则不能为 11 个 Standard_D1 实例购买预留。 虚拟机预留实例的配额检查包括已在订阅中部署的 VM。 例如，如果该订阅的配额为针对 D 系列购买 10 个 vCPU，并且已部署两个 Standard_D1 实例，则可在该订阅中为 10 个 Standard_D1 实例购买预留。 您可以创建[报价增加请求](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md)以解决此问题。

- **容量限制**- 在极少数情况下，Azure 会限制购买 VM 大小的子集的新预留，因为区域中的容量较低。

## <a name="buy-a-reserved-vm-instance"></a>购买虚拟机预留实例

您可以在[Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)中购买保留的 VM 实例。 通过[提前付款或按月付款](../articles/cost-management-billing/reservations/monthly-payments-reservations.md)的方式为预留付款。
这些要求适用于购买保留的 VM 实例：

- 您必须在至少一个 EA 订阅或具有即用即付费率的订阅中担任所有者角色。
- 对于 EA 订阅，必须在[EA 门户](https://ea.azure.com/)中启用 **"添加预留实例"** 选项。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。
- 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理才能购买预留。

若要购买实例：

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 选择**所有服务** > **预订**。
1. 选择 **"添加"** 以购买新的预订，然后单击 **"虚拟机**"。
1. 输入必填字段。 与所选属性匹配的正在运行的 VM 实例有资格获得预订折扣。 实际获得折扣的 VM 实例数取决于所选范围和数量。

如果您有 EA 协议，则可以使用 **"添加更多"选项**快速添加其他实例。 该选项不适用于其他订阅类型。


| 字段      | 描述|
|------------|--------------|
|订阅|用于支付预订费用的订阅。 订阅上的付款方式将收取预订费用。 订阅类型必须是企业协议（产品/服务编号：MS-AZR-0017P 或 MS-AZR-0148P）或 Microsoft 客户协议或具有即用即付费率的个人订阅（产品编号：MS-AZR-0003P 或 MS-AZR-0023P）。 费用从货币承诺余额中扣除（如有）或按超额收取。 对于具有即用即付费率的订阅，费用将计入订阅上的信用卡或发票付款方式。|    
|范围       |预订的范围可以包含一个订阅或多个订阅（共享范围）。 如果选择： <ul><li>**单个资源组范围** - 仅将预留折扣应用到所选资源组中匹配的资源。</li><li>**单个订阅范围** - 将预留折扣应用到所选订阅中匹配的资源。</li><li>**共享范围** - 将预留折扣应用到计费上下文中符合条件的订阅中的匹配资源。 对于 EA 客户，计费上下文是注册。 对于采用即用即付费率的单个订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。</li></ul>|
|区域    |预订涵盖的 Azure 区域。|    
|VM 大小     |VM 实例的大小。|
|优化对象     |默认情况下，可以选择 VM 实例大小灵活性。 单击 **"高级设置**"更改实例大小灵活性值，以将预留折扣应用于同一[VM 大小组中的其他 VM。](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md) 容量优先级可以对部署优先使用数据中心容量。 它增加了您对在需要时启动 VM 实例的能力的信心。 仅当预留范围为单个订阅时，容量优先级才可用。 |
|术语        |一年或三年。|
|数量    |预订中购买的实例数。 数量是可以获得计费折扣的正在运行的 VM 实例数。 例如，如果在美国东部运行 10Standard_D2 VM，则将数量指定为 10，以最大化所有正在运行的 VM 的好处。 |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>使用数据和预订利用率

对于获得预留折扣的使用量，使用数据的有效价格为零。 您可以看到哪个 VM 实例为每个预留提供了预留折扣。

有关预订折扣如何显示在使用情况数据中的详细信息，请参阅[了解企业注册的 Azure 预留使用情况](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)（如果您是 EA 客户）。 如果您有单独的订阅，请参阅[了解即用即付订阅的 Azure 预留使用情况](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md)。

## <a name="change-a-reservation-after-purchase"></a>购买后更改预订

可以在购买后对预留进行以下类型的更改：

- 更新预留范围
- 实例大小灵活性（如果适用）
- 所有权

您还可以将预留拆分为较小的块，并将已拆分的预留合并。 任何更改都不会导致新的商业交易或更改预订的结束日期。

购买后不能直接进行以下类型的更改：

- 现有保留区域
- SKU
- 数量
- Duration

但是，如果要进行更改，可以*交换*预订。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何管理预留，请参阅[管理 Azure 预留](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md)。
- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
    - [什么是 Azure 预留？](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
    - [管理 Azure 中的预留](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md)
    - [了解预留折扣的应用方式](../articles/cost-management-billing/manage/understand-vm-reservation-charges.md)
    - [了解采用即用即付费率的订阅的预留使用情况](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md)
    - [了解企业合约的预留使用情况](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    - [预留未包含的 Windows 软件成本](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md)
    - [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](https://docs.microsoft.com/partner-center/azure-reservations)
