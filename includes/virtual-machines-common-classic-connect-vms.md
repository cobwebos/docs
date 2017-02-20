

![独立云服务中的虚拟机](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

如果将虚拟机放在虚拟网络中，可以决定要用于负载平衡和可用性集的云服务数量。 此外，可以采用与本地网络相同的方式，在子网上组织虚拟机，并将虚拟网络连接到本地网络。 下面是一个示例：

![虚拟网络中的虚拟机](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

若要在 Azure 中连接虚拟机，建议使用虚拟网络。 最佳做法是在单独的云服务中配置应用程序的每一层。 但可能需要将来自不同应用程序层的一些虚拟机组合到同一云服务中，以保留每个订阅 200 个云服务的最大值。 若要查看此限制以及其他限制，请参阅 [Azure 订阅和服务限制、配额和约束](../articles/azure-subscription-service-limits.md)。

## <a name="connect-vms-in-a-virtual-network"></a>连接虚拟网络中的 VM
若要连接虚拟网络中的虚拟机，请执行以下步骤：

1. 在 [Azure 门户](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md)中创建虚拟网络。
2. 为部署创建一组云服务，以反映可用性集和负载平衡的设计。 在 Azure 经典门户中，针对每一个云服务，单击“新建”>“计算”>“云服务”>“自定义创建”。
3. 若要逐一创建新的虚拟机，请单击“新建”>“计算”>“虚拟机”>“从库中”。 为 VM 选择正确的云服务和虚拟网络。 如果云服务已加入虚拟网络，系统则会为用户选择服务名称。

![为虚拟机选择云服务](./media/virtual-machines-common-classic-connect-vms/VMConfig1.png)

## <a name="connect-vms-in-a-standalone-cloud-service"></a>连接独立云服务中的 VM
若要连接独立云服务中的虚拟机，请执行以下步骤：

1. 在 [Azure 经典门户](http://manage.windowsazure.com)中创建云服务。 单击“新建”>“计算”>“云服务”>“自定义创建”。 或者，在创建第一个虚拟机时，可以为部署创建云服务。
2. 创建虚拟机时，请选择上一个步骤中创建的云服务名称。
   
   ![将虚拟机添加到现有云服务](./media/virtual-machines-common-classic-connect-vms/Connect-VM-to-CS.png)



<!--HONumber=Nov16_HO3-->


