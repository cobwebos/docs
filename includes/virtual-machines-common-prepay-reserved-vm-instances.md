---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 07/01/2019
ms.openlocfilehash: 9e0caa8b98133dad3af083e8910d0603bbd2563b
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67489908"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances-ri"></a>预付 Azure 保留的 VM 实例 (RI) 的虚拟机

通过 Azure 虚拟机 (VM) 预留实例预付虚拟机并节省资金。 有关详细信息，请参阅 [Azure 虚拟机预留实例产品/服务](https://azure.microsoft.com/pricing/reserved-vm-instances/)。

可在 [Azure 门户](https://portal.azure.com)中购买虚拟机预留实例。 若要购买实例：

- 您必须至少一个企业订阅或现用现付费率的订阅的所有者角色。
- 对于企业订阅，必须在 [EA 门户](https://ea.azure.com)中启用“添加预留实例”  。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。
- 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理才能购买预留。

预订折扣将自动应用于正在运行的与预订范围和属性匹配的虚拟机数量。 可以通过 [Azure 门户](https://portal.azure.com)、PowerShell、CLI 或 API 更新预留的范围。

## <a name="determine-the-right-vm-size-before-you-buy"></a>在购买前确定正确的 VM 大小

在购买预订之前，应确定所需的 VM 的大小。 以下部分将帮助你确定适当的 VM 大小。

### <a name="use-reservation-recommendations"></a>使用保留的建议

可以使用保留的建议来帮助确定应该购买的预订。

- 购买建议和建议的数量会显示在 Azure 门户中的 VM 保留的实例购买时。
- Azure 顾问提供了单独的订阅的购买建议。  
- 可以使用 Api 以获得共享作用域和单个订阅范围内的购买建议。 有关详细信息，请参阅[保留实例购买建议 Api 适用于企业客户](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)。
- EA 客户的购买建议的共享和单个订阅作用域是附带[Azure 使用情况见解 Power BI 内容包](/power-bi/service-connect-to-azure-consumption-insights)。

### <a name="classic-vms-and-cloud-services"></a>经典虚拟机和云服务

虚拟机保留实例会自动将应用于这两个经典 Vm 和云服务，启用实例大小灵活性。 没有任何特殊的 Sku 为经典 Vm 或云服务。 相同的 VM Sku 适用于它们。

例如，您可能将经典 Vm 或云服务转换为基于 Azure 资源管理器的 Vm。 在此示例中，预订折扣会自动适用于匹配的 Vm。 无需*exchange*现有的保留的实例-它会自动应用。

### <a name="analyze-your-usage-information"></a>分析使用情况信息
您应分析你的使用情况信息以帮助确定应该购买的预订。

使用情况数据目前在使用情况文件和 Api。 使用它们一起确定要购买的保留。 您应检查以确定要购买保留数量的每天都能使用率过高的 VM 实例。

避免`Meter`子类别和`Product`使用情况数据中的字段。 它们不区分使用高级存储的 VM 大小。 如果您使用这些字段来确定预订购买的 VM 大小，可能会购买大小有误。 然后，则不会希望将预订折扣。 相反，请参阅`AdditionalInfo`字段中使用情况文件或使用情况 API 来确定正确的 VM 大小。

### <a name="purchase-restriction-considerations"></a>购买限制注意事项

保留的 VM 实例都可用于大多数 VM 大小，有一些例外情况。 预订折扣不适用于以下 Vm:

- **VM 系列**-A 系列、 Av2 系列或 G 系列。

- **在预览版中的 Vm**的任何 VM 系列或大小处于预览状态。

- **云**-保留项不是可供购买德国或中国区域中。

- **配额不足**-预订的范围限定为单个订阅必须具有在新的 RI 的订阅中可用的 vCPU 配额。 例如，如果目标订阅的配额限制为 10 个 vCPU（适用于 D 系列），则不能为 11 个 Standard_D1 实例购买预留。 虚拟机预留实例的配额检查包括已在订阅中部署的 VM。 例如，如果该订阅的配额为针对 D 系列购买 10 个 vCPU，并且已部署两个 Standard_D1 实例，则可在该订阅中为 10 个 Standard_D1 实例购买预留。 你可以[引号创建增加请求](../articles/azure-supportability/resource-manager-core-quotas-request.md)若要解决此问题。

- **容量限制**-在极少数情况下，Azure VM 的新的子集的保留项的采购大小，由于在区域中的低容量的限制。

## <a name="buy-a-reserved-vm-instance"></a>购买虚拟机预留实例

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务”   > “预订”  。
3. 选择“添加”  购买新的预订。
4. 输入必填的字段。 与所选属性匹配的正在运行的 VM 实例有资格获得预订折扣。 实际获得折扣的 VM 实例数取决于所选范围和数量。

    | 字段      | 描述|
    |------------|--------------|
    |名称        |此预订的名称。|
    |订阅|用于支付预订费用的订阅。 将向订阅的付款方式收取预订的预付费用。 订阅类型必须是企业协议（产品/服务编号：MS-AZR-0017P 或 MS-条-0148 P) 或单个订阅中的通过即用即付费率 (产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）。 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。 对于现用现付费率使用的订阅，费用将信用卡或发票付款方式的订阅计费。|    
    |范围       |预订的范围可以包含一个订阅或多个订阅（共享范围）。 如果选择： <ul><li>单个订阅 - 预订折扣将应用到此订阅中的 VM。 </li><li>共享 - 预订折扣将应用到计费上下文中任何订阅中运行的 VM。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于使用具有现用现付费率的订阅的客户，共享作用域是由帐户管理员创建的即用即付费率使用的所有订阅。</li></ul>|
    |区域    |预订涵盖的 Azure 区域。|    
    |VM 大小     |VM 实例的大小。|
    |优化对象     |VM 实例大小可以灵活调整，因此可将预留折扣应用到同一 [VM 大小组](https://aka.ms/RIVMGroups)中的其他 VM。 容量优先级可以对部署优先使用数据中心容量。 使你在需要时能够更加有把握地启动 VM 实例。 仅当预留范围为单个订阅时，容量优先级才可用。 |
    |术语        |一年或三年。|
    |数量    |预订中购买的实例数。 数量是可以获得计费折扣的正在运行的 VM 实例数。 例如，如果正在美国东部运行 10 个 Standard_D2 VM，则会将数量指定为 10，以将所有正在运行的虚拟机的权益最大化。 |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="change-a-reservation-after-purchase"></a>更改预订购买后

对预订购买后，可以进行以下类型的更改：

- 更新预订范围
- 实例大小灵活性 （如果适用）
- 所有权

也可以拆分成较小的区块和合并已拆分预订的保留。 无所做的更改会导致新的商业事务或更改保留的结束日期。

您不能直接购买后进行以下类型的更改：

- 现有预订的区域
- SKU
- 数量
- Duration

但是，你可以*exchange*一个保留项，如果你想要进行更改。

## <a name="cancellations-and-exchanges"></a>取消和更换

如果需要取消预留，可能会产生 12% 的提前终止费。 退款根据购买价格和当前预留价格中的最低者计算。 退款限制为每年 50,000 美元。 收到的退款为按比例计算出的余额减去 12% 的提前终止费。 若要请求取消，请在 Azure 门户中访问该预留，并选择“退款”  创建支持请求。

如果需要将虚拟机预留实例的订购项目更改为其他区域、VM 大小组或期限，可将其交换为价值相同或更高的另一个订购项目。 新订购项目的期限开始日期不是从交换的订购项目延续。 1 年或 3 年期限从创建新订购项目开始算起。 若要请求交换，请在 Azure 门户中访问该预留，并选择“交换”  创建支持请求。

有关如何对 exchange 或退款保留的详细信息，请参阅[保留交换和退款](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何管理预留，请参阅[管理 Azure 预留](../articles/billing/billing-manage-reserved-vm-instance.md)。
- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
    - [什么是 Azure 预订？](../articles/billing/billing-save-compute-costs-reservations.md)
    - [管理 Azure 中的预留](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [了解预留折扣的应用方式](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [了解即用即付费率使用订阅的预订使用情况](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [了解企业合约的预留使用情况](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [预留未包含的 Windows 软件成本](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](https://docs.microsoft.com/partner-center/azure-reservations)
