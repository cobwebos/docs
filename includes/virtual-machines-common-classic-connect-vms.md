

![独立云服务中的虚拟机](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

如果将虚拟机放在虚拟网络中时，你可以决定多少云服务，你想要用于负载平衡和可用性集。 此外，可以将上子网的虚拟机组织方式相同，如你的本地网络，然后连接到你的本地网络的虚拟网络。 下面是一个示例：

![虚拟网络中的虚拟机](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

虚拟网络是推荐的方法，将在 Azure 中的虚拟机连接。 最佳做法是在单独的云服务中配置你的应用程序的每个层。 但是，你可能需要将不同应用程序层的部分虚拟机组合到同一云服务内的每个订阅 200 云服务的最大保持。 若要查看本限制和其他限制，请参阅[Azure 订阅和服务限制、 配额和约束](../articles/azure-subscription-service-limits.md)。

## <a name="connect-vms-in-a-virtual-network"></a>连接虚拟网络中的 Vm
将虚拟网络中的虚拟机的连接：

1. 创建虚拟网络中的[Azure 门户](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md)并指定经典部署。
2. 创建云服务为你的部署，以反映你的设计有关可用性集和负载平衡的集。 在 Azure 门户中，单击**新建 > 计算 > 云服务**每个云服务。

  请填写云服务详细信息，请选择相同_资源组_用于虚拟网络。

3. 若要创建每个新的虚拟机，请单击**新建 > 计算**，然后选择适当的 VM 映像从**精选应用**。

  虚拟机中**基础知识**边栏选项卡，选择相同_资源组_用于虚拟网络。

  ![当使用 VNet VM 基础知识边栏选项卡](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_VN.png)

4. 如填写 VM**设置**，选择正确_云服务_或_虚拟网络_vm。

  Azure 将选择基于你选择的其他项。

  ![当使用 VNet VM 设置边栏选项卡](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_VN.png)


## <a name="connect-vms-in-a-standalone-cloud-service"></a>连接独立云服务中的 Vm
若要连接独立云服务中的虚拟机：

1. 创建的云服务中[Azure 门户](http://portal.azure.com)。 单击**新建 > 计算 > 云服务**。 或者，您可以创建为你的部署的云服务，当你创建第一个虚拟机。
2. 你创建虚拟机，请选择使用与云服务相同的资源组。

  ![将虚拟机添加到现有的云服务](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_SA.png)

3.  你填写 VM 详细信息，请选择设置在第一步中创建的云服务的名称。

  ![选择用于虚拟机的云服务](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_SA.png)
