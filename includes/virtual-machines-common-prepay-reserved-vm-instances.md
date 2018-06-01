---
ms.topic: include
ms.openlocfilehash: 8e710bebf979b60f61552593ae550e95a8340d2b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
ms.locfileid: "34307560"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>通过 Azure 虚拟机预留实例预付虚拟机

通过 Azure 虚拟机 (VM) 预留实例预付虚拟机并节省资金。 有关详细信息，请参阅 [Azure 预留实例产品/服务](https://azure.microsoft.com/pricing/reserved-vm-instances/)。

可以在 [Azure 门户](https://portal.azure.com)中购买 Azure 虚拟机预留实例。 购买预留实例：
-   你必须至少具有一个企业或即用即付订阅的所有者角色。
-   对于企业订阅，必须在 [EA 门户](https://ea.azure.com)中启用预留实例购买。
-   对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理可以购买预留实例。

## <a name="buy-a-reserved-instance"></a>购买预留实例
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务” > “预订”。
3. 选择“添加”购买新的预留实例。
4. 填写必填字段。 与所选属性匹配的正在运行的 VM 实例有资格获得预留实例折扣。 实际获得折扣的 VM 实例数取决于所选范围和数量。

    | 字段      | 说明|
    |:------------|:--------------|
    |名称        |此预留实例的名称。| 
    |订阅|用于支付预留实例费用的订阅。 将向订阅的付款方式收取预留实例的预付费用。 订阅类型必须是“企业协议”（产品/服务编号：MS-AZR-0017P）或“即用即付”（产品/服务编号：MS-AZR-0003P）。 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。 对于即用即付订阅，将向订阅的信用卡或发票付款方式收取费用。|    
    |范围       |预留实例的范围可以包含一个或多个订阅（共享范围）。 如果选择： <ul><li>单个订阅 - 预留实例折扣将应用到此订阅中的 VM。 </li><li>共享 - 预留实例折扣将应用到计费上下文中任何订阅中运行的 VM。 对于企业客户，共享范围是许可登记表，包括许可登记表中的所有订阅（开发/测试订阅除外）。 对于即用即付客户，共享范围是由帐户管理员创建的所有即用即付订阅。</li></ul>|
    |Location    |预留实例涵盖的 Azure 区域。|    
    |VM 大小     |VM 实例的大小。|
    |术语        |一年或三年。|
    |数量    |预留实例中购买的实例数。 数量是可以获得计费折扣的正在运行的 VM 实例数。 例如，如果正在美国东部运行 10 个 Standard_D2 VM，则会将数量指定为 10，以将所有正在运行的虚拟机的权益最大化。 |
5. 选择“计算成本”时，可以查看预留实例的成本。

    ![提交预留实例购买之前的屏幕截图](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. 选择“购买”。
7. 选择“查看此预订”以查看购买的状态。

    ![提交预留实例购买之后的屏幕截图](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>后续步骤 
预留实例折扣将自动应用于正在运行的与预留实例范围和属性匹配的虚拟机数量。 可以通过 [Azure 门户](https://portal.azure.com)、PowerShell、CLI 或通过 API 更新预留实例的范围。 

若要了解如何管理预留实例，请参阅[管理 Azure 预留实例](../articles/billing/billing-manage-reserved-vm-instance.md)。

若要了解有关 Azure 预留实例的详细信息，请参阅以下文章：

- [通过预留实例节省虚拟机资金](../articles/billing/billing-save-compute-costs-reservations.md)
- [管理 Azure 预留实例](../articles/billing/billing-manage-reserved-vm-instance.md)
- [了解如何应用预留实例折扣](../articles/billing/billing-understand-vm-reservation-charges.md)
- [了解即用即付订阅的预订实例使用情况](../articles/billing/billing-understand-reserved-instance-usage.md)
- [了解企业许可登记表的预订实例使用情况](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [预订实例未包含的 Windows 软件成本](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [合作伙伴中心云解决方案提供商 (CSP) 计划中的保留实例](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。