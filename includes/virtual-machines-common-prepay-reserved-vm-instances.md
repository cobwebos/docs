---
author: yashesvi
ms.author: yashar
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 08-07-2018
ms.openlocfilehash: 19a153a5cdc9d5f878494984313baebd12dbcbb5
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39631157"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>通过 Azure 虚拟机预留实例为虚拟机预付费

通过 Azure 虚拟机 (VM) 预留实例预付虚拟机并节省资金。 有关详细信息，请参阅 [Azure 虚拟机预留实例产品/服务](https://azure.microsoft.com/pricing/reserved-vm-instances/)。

可在 [Azure 门户](https://portal.azure.com)中购买虚拟机预留实例。 若要购买实例：

- 你必须至少具有一个企业或即用即付订阅的所有者角色。
- 对于企业订阅，必须在 [EA 门户](https://ea.azure.com)中启用预订购买。
- 对于云解决方案提供商 (CSP) 计划，只有管理代理或销售代理可以购买预订。

## <a name="determine-the-right-vm-size-before-purchase"></a>在购买前确定正确的 VM 大小

使用数据中的“计量子类别”和“产品”字段不区分使用高级存储的 VM 大小和不使用高级存储的 VM 大小。使用这些字段来确定预留购买的 VM 大小可能导致预留购买错误以及无法获得预留折扣。 请使用下述方法之一来确定适合预留购买的 VM 大小。

- 请参阅使用情况文件或使用情况 API 中的 AdditionalInfo 字段，以确定预订购买的正确 VM 大小。 请勿使用“计量子类别”或“产品”字段的值，因为这些字段不区分 VM 的 S 和非 S 版本。
- 还可以使用 Powershell、Azure 资源管理器或从 Azure 门户中的 VM 详细信息处获取准确的 VM 大小信息。

虚拟机预留实例适用于大多数 VM 大小，只有一些例外：

- 预览版 VM：以预览版形式提供的任何 VM 系列或大小都不适用于预留购买。
- 云：不能在 Azure 美国政府、德国或中国区域进行预留购买。
- 配额不足：其范围仅限单个订阅的预留必须在订阅中具有可用的 vCPU 配额才能获得新的 RI。 例如，如果目标订阅的配额限制为 10 个 vCPU（适用于 D 系列），则不能为 11 个 Standard_D1 实例购买预留。 虚拟机预留实例的配额检查包括已在订阅中部署的 VM。 例如，如果该订阅的配额为针对 D 系列购买 10 个 vCPU，并且已部署两个 Standard_D1 实例，则可在该订阅中为 10 个 Standard_D1 实例购买预留。 
- 容量限制：在极少数情况下，Azure 限制购买部分 VM 大小的新虚拟机预留实例，因为某个区域的容量不足。

## <a name="buy-a-reserved-vm-instance"></a>购买虚拟机预留实例

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务” > “预订”。
3. 选择“添加”购买新的预订。
4. 填写必填字段。 与所选属性匹配的正在运行的 VM 实例有资格获得预订折扣。 实际获得折扣的 VM 实例数取决于所选范围和数量。

    | 字段      | Description|
    |:------------|:--------------|
    |名称        |此预订的名称。| 
    |订阅|用于支付预订费用的订阅。 将向订阅的付款方式收取预订的预付费用。 订阅类型必须是“企业协议”（套餐编号：MS-AZR-0017P）或“即用即付”（套餐编号：MS-AZR-0003P）。 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。 对于即用即付订阅，将向订阅的信用卡或发票付款方式收取费用。|    
    |范围       |预订的范围可以包含一个订阅或多个订阅（共享范围）。 如果选择： <ul><li>单个订阅 - 预订折扣将应用到此订阅中的 VM。 </li><li>共享 - 预订折扣将应用到计费上下文中任何订阅中运行的 VM。 对于企业客户，共享范围是许可登记表，包括许可登记表中的所有订阅（开发/测试订阅除外）。 对于即用即付客户，共享范围是由帐户管理员创建的所有即用即付订阅。</li></ul>|
    |位置    |预订涵盖的 Azure 区域。|    
    |VM 大小     |VM 实例的大小。|
    |优化对象     |VM 实例大小可以灵活调整，因此可将预留折扣应用到同一 [VM 大小组](https://aka.ms/RIVMGroups)中的其他 VM。 容量优先级可以为部署预留数据中心容量， 使你在需要时能够更加有把握地启动 VM 实例。 仅当预留范围为单个订阅时，容量优先级才可用。 |
    |术语        |一年或三年。|
    |数量    |预订中购买的实例数。 数量是可以获得计费折扣的正在运行的 VM 实例数。 例如，如果正在美国东部运行 10 个 Standard_D2 VM，则会将数量指定为 10，以将所有正在运行的虚拟机的权益最大化。 |
5. 选择“计算成本”时，可以查看预订的成本。

    ![提交预留购买之前的屏幕截图](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. 选择“购买”。
7. 选择“查看此预订”以查看购买的状态。

    ![提交预留购买之后的屏幕截图](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>后续步骤

预订折扣将自动应用于正在运行的与预订范围和属性匹配的虚拟机数量。 可以通过 [Azure 门户](https://portal.azure.com)、PowerShell、CLI 或 API 更新预留的范围。

若要了解如何管理预留，请参阅[管理 Azure 预留](../articles/billing/billing-manage-reserved-vm-instance.md)。

若要了解有关 Azure 预留的详细信息，请参阅以下文章：

- [什么是 Azure 预留？](../articles/billing/billing-save-compute-costs-reservations.md)
- [管理 Azure 中的预留](../articles/billing/billing-manage-reserved-vm-instance.md)
- [了解如何应用预留折扣](../articles/billing/billing-understand-vm-reservation-charges.md)
- [了解即用即付订阅的预留使用情况](../articles/billing/billing-understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [预留未包含的 Windows 软件成本](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预留](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
