---
author: yashesvi
ms.author: yashar
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09-05-2018
ms.openlocfilehash: 4b51631e8b7d3f53edd1afdba76de3031b112254
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2018
ms.locfileid: "49805067"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>通过 Azure 虚拟机预留实例为虚拟机预付费

通过 Azure 虚拟机 (VM) 预留实例预付虚拟机并节省资金。 有关详细信息，请参阅 [Azure 虚拟机预留实例产品/服务](https://azure.microsoft.com/pricing/reserved-vm-instances/)。

可在 [Azure 门户](https://portal.azure.com)中购买虚拟机预留实例。 若要购买实例：

- 你必须至少具有一个企业或即用即付订阅的所有者角色。
- 对于企业订阅，必须在 [EA 门户](https://ea.azure.com)中启用预订购买。
- 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理才能购买预留。

## <a name="determine-the-right-vm-size-before-you-buy"></a>在购买前确定正确的 VM 大小

使用情况数据中的“计量子类别”和“产品”字段在使用高级存储的 VM 大小和不使用高级存储的 VM 之间不进行区分。 如果使用这些字段来确定要用于预留的 VM 大小，可能会购买错误的大小，并且无法获得期望的预留折扣。 在购买预留时，使用以下方法之一可确定适当的 VM 大小：

- 请参阅使用情况文件或使用情况 API 中的 AdditionalInfo 字段，以确定正确的 VM 大小。 不要使用来自“计量子类别”或“产品”字段的值。 这些字段在 S 版的 VM 和非 S 版的 VM 之间不进行区分。
- 使用 Powershell、Azure 资源管理器或从 Azure 门户中的 VM 详细信息获取准确的 VM 大小信息。

虚拟机预留实例适用于大多数 VM 大小，只有一些例外：

- 预留折扣不适用于以下 VM：
  - 经典 VM 和云服务
  - 受约束的 vCPU 大小
  - VM 系列：A 系列、Av2 系列或 G 系列
  - 预览版 VM：预览版中的任何 VM 系列或大小
- 云：不能在德国或中国区域中购买预留。
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
    |区域    |预订涵盖的 Azure 区域。|    
    |VM 大小     |VM 实例的大小。|
    |优化对象     |VM 实例大小可以灵活调整，因此可将预留折扣应用到同一 [VM 大小组](https://aka.ms/RIVMGroups)中的其他 VM。 容量优先级可以对部署优先使用数据中心容量。 使你在需要时能够更加有把握地启动 VM 实例。 仅当预留范围为单个订阅时，容量优先级才可用。 |
    |术语        |一年或三年。|
    |数量    |预订中购买的实例数。 数量是可以获得计费折扣的正在运行的 VM 实例数。 例如，如果正在美国东部运行 10 个 Standard_D2 VM，则会将数量指定为 10，以将所有正在运行的虚拟机的权益最大化。 |
5. 选择“计算成本”时，可以查看预订的成本。

    ![提交预留购买之前的屏幕截图](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. 选择“购买”。
7. 选择“查看此预订”以查看购买的状态。

    ![提交预留购买之后的屏幕截图](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="cancellations-and-exchanges"></a>取消和更换

如果需要取消预留，可能会产生 12% 的提前终止费。 退款根据购买价格和当前预留价格中的最低者计算。 退款限制为每年 50,000 美元。 收到的退款为按比例计算出的余额减去 12% 的提前终止费。 若要请求取消，请在 Azure 门户中访问该预留，并选择“退款”创建支持请求。

如果需要将虚拟机预留实例的订购项目更改为其他区域、VM 大小组或期限，可将其交换为价值相同或更高的另一个订购项目。 新订购项目的期限开始日期不是从交换的订购项目延续。 1 年或 3 年期限从创建新订购项目开始算起。 若要请求交换，请在 Azure 门户中访问该预留，并选择“交换”创建支持请求。

## <a name="next-steps"></a>后续步骤

预订折扣将自动应用于正在运行的与预订范围和属性匹配的虚拟机数量。 可以通过 [Azure 门户](https://portal.azure.com)、PowerShell、CLI 或 API 更新预留的范围。

若要了解如何管理预留，请参阅[管理 Azure 预留](../articles/billing/billing-manage-reserved-vm-instance.md)。

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [什么是 Azure 预订？](../articles/billing/billing-save-compute-costs-reservations.md)
- [管理 Azure 中的预留](../articles/billing/billing-manage-reserved-vm-instance.md)
- [了解预留折扣的应用方式](../articles/billing/billing-understand-vm-reservation-charges.md)
- [了解即用即付订阅的预留使用情况](../articles/billing/billing-understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [预留未包含的 Windows 软件成本](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
