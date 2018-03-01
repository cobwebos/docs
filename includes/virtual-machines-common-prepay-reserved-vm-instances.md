# <a name="prepay-for-virtual-machines-with-reserved-vm-instances"></a>通过保留 VM 实例预付虚拟机

通过保留虚拟机实例预付虚拟机并节省资金。 有关详细信息，请参阅[保留虚拟机实例产品/服务](https://azure.microsoft.com/pricing/reserved-vm-instances/)。

可以在 [Azure 门户](https://portal.azure.com)中购买保留虚拟机实例。 若要购买保留虚拟机实例，请执行以下操作：
-   你必须至少具有一个企业或即用即付订阅的所有者角色。
-   对于企业订阅，必须在 [EA 门户](https://ea.azure.com)中启用预订购买。

## <a name="buy-a-reserved-virtual-machine-instance"></a>购买保留虚拟机实例
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务” > “预订”。
3. 选择“添加”购买新的预订。
4. 填写必填字段。 与所选属性匹配的正在运行的 VM 实例有资格获得预订折扣。 实际获得折扣的 VM 实例数取决于所选范围和数量。

    | 字段      | 说明|
    |:------------|:--------------|
    |名称        |此预订的名称。| 
    |订阅|用于支付预订费用的订阅。 将向订阅的付款方式收取预订的预付费用。 订阅类型必须是“企业协议”（产品/服务编号：MS-AZR-0017P）或“即用即付”（产品/服务编号：MS-AZR-0003P）。 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。 对于即用即付订阅，将向订阅的信用卡或发票付款方式收取费用。|    
    |范围       |预订的范围可以包含一个订阅或多个订阅（共享范围）。 如果选择： <ul><li>单个订阅 - 预订折扣将应用到此订阅中的 VM。 </li><li>共享 - 预订折扣将应用到计费上下文中任何订阅中运行的 VM。 对于企业客户，共享范围是许可登记表，包括许可登记表中的所有订阅（开发/测试订阅除外）。 对于即用即付客户，共享范围是由帐户管理员创建的所有即用即付订阅。</li></ul>|
    |Location    |预订涵盖的 Azure 区域。|    
    |VM 大小     |VM 实例的大小。|
    |术语        |一年或三年。|
    |数量    |预订中购买的实例数。 数量是可以获得计费折扣的正在运行的 VM 实例数。 例如，如果正在美国东部运行 10 个 Standard_D2 VM，则会将数量指定为 10，以将所有正在运行的虚拟机的权益最大化。 |
5. 选择“计算成本”时，可以查看预订的成本。

    ![提交预订购买之前的屏幕截图](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. 选择“购买”。
7. 选择“查看此预订”以查看购买的状态。

    ![提交预订购买之前的屏幕截图](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>后续步骤 
预订折扣将自动应用于正在运行的与预订范围和属性匹配的虚拟机数量。 可以通过 [Azure 门户](https://portal.azure.com)、PowerShell、CLI 或通过 API 更新预订的范围。 

若要了解如何管理预订，请参阅[管理 Azure 预订虚拟机实例](../articles/billing/billing-manage-reserved-vm-instance.md)。

若要了解有关预订虚拟机实例的详细信息，请参阅以下文章。

- [通过预订虚拟机实例节省虚拟机资金](../articles/billing/billing-save-compute-costs-reservations.md)
- [了解如何应用预订虚拟机实例折扣](../articles/billing/billing-understand-vm-reservation-charges.md)
- [了解即用即付订阅的预订实例使用情况](../articles/billing/billing-understand-reserved-instance-usage.md)
- [了解企业许可登记表的预订实例使用情况](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [预订实例未包含的 Windows 软件成本](../articles/billing/billing-reserved-instance-windows-software-costs.md)