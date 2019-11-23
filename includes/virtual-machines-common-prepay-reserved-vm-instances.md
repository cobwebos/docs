---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/21/2019
ms.openlocfilehash: f583796fc353852ef3898e28fa96524e08cfb4ad
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414545"
---
When you commit to an Azure reserved VM instance you can save money. 预订折扣将自动应用于正在运行的与预订范围和属性匹配的虚拟机数量。 You don't need to assign a reservation to a virtual machine to get the discounts. A reserved instance purchase covers only the compute part of your VM usage. For Windows VMs, the usage meter is split into two separate meters. There's a compute meter, which is same as the Linux meter, and a Windows IP meter. The charges that you see when you make the purchase are only for the compute costs. Charges don't include Windows software costs. For more information about software costs, see [Software costs not included with Azure Reserved VM Instances](../articles/billing/billing-reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>在购买前确定正确的 VM 大小

Before you buy a reservation, you should determine the size of the VM that you need. The following sections will help you determine the right VM size.

### <a name="use-reservation-recommendations"></a>Use reservation recommendations

You can use reservation recommendations to help determine the reservations you should purchase.

- Purchase recommendations and recommended quantity are show when you purchase a VM reserved instance in the Azure portal.
- Azure Advisor provides purchase recommendations for individual subscriptions.  
- You can use the APIs to get purchase recommendations for both shared scope and single subscription scope. For more information, see [Reserved instance purchase recommendation APIs for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- For Enterprise Agreement (EA) and Microsoft Customer Agreement (MCA) customers, purchase recommendations for shared and single subscription scopes are available with the [Azure Consumption Insights Power BI content pack](/power-bi/service-connect-to-azure-consumption-insights).

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

### <a name="analyze-your-usage-information"></a>Analyze your usage information
Analyze your usage information to help determine which reservations you should purchase.

Usage data is available in the usage file and APIs. Use them together to determine which reservation to purchase. Check for VM instances that have high usage on daily basis to determine the quantity of reservations to purchase.

Avoid the `Meter` subcategory and `Product` fields in usage data. They don't distinguish between VM sizes that use premium storage. If you use these fields to determine the VM size for reservation purchase, you may buy the wrong size. Then you won't get the reservation discount you expect. Instead, refer to the `AdditionalInfo` field in your usage file or usage API to determine the correct VM size.

### <a name="purchase-restriction-considerations"></a>Purchase restriction considerations

Reserved VM Instances are available for most VM sizes with some exceptions. Reservation discounts don't apply for the following VMs:

- **VM series** - A-series, Av2-series, or G-series.

- **Preview or Promo VMs** - Any VM-series or size that is in preview or uses promotional meter.

- **Clouds** - Reservations aren't available for purchase in Germany or China regions.

- **Insufficient quota** - A reservation that is scoped to a single subscription must have vCPU quota available in the subscription for the new RI. 例如，如果目标订阅的配额限制为 10 个 vCPU（适用于 D 系列），则不能为 11 个 Standard_D1 实例购买预留。 虚拟机预留实例的配额检查包括已在订阅中部署的 VM。 例如，如果该订阅的配额为针对 D 系列购买 10 个 vCPU，并且已部署两个 Standard_D1 实例，则可在该订阅中为 10 个 Standard_D1 实例购买预留。 You can [create quote increase request](../articles/azure-supportability/resource-manager-core-quotas-request.md) to resolve this issue.

- **Capacity restrictions** - In rare circumstances, Azure limits the purchase of new reservations for subset of VM sizes, because of low capacity in a region.

## <a name="buy-a-reserved-vm-instance"></a>购买虚拟机预留实例

You can buy a reserved VM instance in the [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). 通过[提前付款或按月付款](../articles/billing/billing-monthly-payments-reservations.md)的方式为预留付款。
These requirements apply to buying a reserved VM instance:

- You must be in an Owner role for at least one EA subscription or a subscription with a pay-as-you-go rate.
- For EA subscriptions, the **Add Reserved Instances** option must be enabled in the [EA portal](https://ea.azure.com/). Or, if that setting is disabled, you must be an EA Admin for the subscription.
- 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理才能购买预留。

若要购买实例：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 选择“所有服务” > “预订”。
1. Select **Add** to purchase a new reservation and then click **Virtual machine**.
1. Enter required fields. 与所选属性匹配的正在运行的 VM 实例有资格获得预订折扣。 实际获得折扣的 VM 实例数取决于所选范围和数量。

If you have an EA agreement, you can use the **Add more option** to quickly add additional instances. The option isn't available for other subscription types.


| 字段      | 描述|
|------------|--------------|
|Subscription|用于支付预订费用的订阅。 The payment method on the subscription is charged the costs for the reservation. The subscription type must be an enterprise agreement (offer numbers: MS-AZR-0017P or MS-AZR-0148P) or Microsoft Customer Agreement or an individual subscription with pay-as-you-go rates (offer numbers: MS-AZR-0003P or MS-AZR-0023P). The charges are deducted from the monetary commitment balance, if available, or charged as overage. For a subscription with pay-as-you-go rates, the charges are billed to the credit card or invoice payment method on the subscription.|    
|范围       |预订的范围可以包含一个订阅或多个订阅（共享范围）。 如果选择： <ul><li>**单个资源组范围** - 仅将预留折扣应用到所选资源组中匹配的资源。</li><li>**单个订阅范围** - 将预留折扣应用到所选订阅中匹配的资源。</li><li>**共享范围** - 将预留折扣应用到计费上下文中符合条件的订阅中的匹配资源。 For EA customers, the billing context is the enrollment. 对于采用即用即付费率的单个订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。</li></ul>|
|地区    |预订涵盖的 Azure 区域。|    
|VM 大小     |VM 实例的大小。|
|优化对象     |VM instance size flexibility is selected by default. Click **Advanced settings** to change the instance size flexibility value to apply the reservation discount to other VMs in the same [VM size group](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). 容量优先级可以对部署优先使用数据中心容量， It offers additional confidence in your ability to launch the VM instances when you need them. 仅当预留范围为单个订阅时，容量优先级才可用。 |
|条款        |一年或三年。|
|数量    |预订中购买的实例数。 数量是可以获得计费折扣的正在运行的 VM 实例数。 For example, if you are running 10 Standard_D2 VMs in the East US, then you would specify quantity as 10 to maximize the benefit for all running VMs. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Usage data and reservation utilization

对于获得预留折扣的使用量，使用数据的有效价格为零。 You can see which VM instance received the reservation discount for each reservation.

For more information about how reservation discounts appear in usage data, see [Understand Azure reservation usage for your Enterprise enrollment](../articles/billing/billing-understand-reserved-instance-usage-ea.md) if you are an EA customer. If you have an individual subscription, see [Understand Azure reservation usage for your Pay-As-You-Go subscription](../articles/billing/billing-understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Change a reservation after purchase

可以在购买后对预留进行以下类型的更改：

- 更新预留范围
- Instance size flexibility (if applicable)
- Ownership

You can also split a reservation into smaller chunks and merge already split reservations. None of the changes cause a new commercial transaction or change the end date of the reservation.

You can't make the following types of changes after purchase, directly:

- An existing reservation’s region
- SKU
- 数量
- Duration

However, you can *exchange* a reservation if you want to make changes.

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何管理预留，请参阅[管理 Azure 预留](../articles/billing/billing-manage-reserved-vm-instance.md)。
- 若要了解有关 Azure 预留的详细信息，请参阅以下文章：
    - [什么是 Azure 预留？](../articles/billing/billing-save-compute-costs-reservations.md)
    - [管理 Azure 中的预留](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [了解预留折扣的应用方式](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [了解采用即用即付费率的订阅的预留使用情况](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [了解企业合约的预留使用情况](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [预留未包含的 Windows 软件成本](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](https://docs.microsoft.com/partner-center/azure-reservations)
