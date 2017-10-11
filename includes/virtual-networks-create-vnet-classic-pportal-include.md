## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>如何在 Azure 门户中创建经典 VNet
若要创建经典 VNet 基于上述方案，请执行以下步骤。

1. 从浏览器中，导航到 http://portal.azure.com，并且，如果有必要，请注册使用 Azure 帐户。
2. 单击**新建** > **网络** > **虚拟网络**，请注意，**选择部署模型**列表已显示**经典**，然后单击**创建**下, 图中所示。
   
    ![在 Azure 门户中创建 VNet](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
3. 上**虚拟网络**边栏选项卡中，键入**名称**中 VNet，然后单击**地址空间**。 配置地址空间设置为 VNet 及其第一个子网，然后单击**确定**。 下图显示我们的方案的 CIDR 块设置。
   
    ![地址空间边栏选项卡](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
4. 单击**资源组**，然后选择要添加到 VNet，或单击的资源组**创建新资源组**将 VNet 添加到新的资源组。 下图显示的资源名的新资源组的组设置**TestRG**。 有关资源组的详细信息，请访问[Azure 资源管理器概述](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)。
   
    ![创建资源组边栏选项卡](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
5. 如有必要，更改**订阅**和**位置**设置你的 VNet。 
6. 如果您不想要看到该 VNet 作为中的磁贴**启动板**，禁用**固定到启动板**。 
7. 单击**创建**，并注意名为磁贴**创建虚拟网络**下图中所示。
   
    ![在门户中创建 VNet](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
8. 等待要创建 VNet，当看到下面的磁贴，请单击它以添加其他子网。
   
    ![在门户中创建 VNet](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
9. 你应该会看到**配置**你的 vnet，如下所示。 
   
    ![在门户中创建 VNet](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
10. 单击**子网** > **添加**，然后键入**名称**并指定**地址范围 （CIDR 块）**为你的子网，然后单击**确定**。 下图显示了当前方案的设置。
    
    ![在 Azure 门户中创建 VNet](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

