## 如何使用 PowerShell 创建 VNet
若要使用 PowerShell 创建 VNet，请执行下面的步骤。

1. 如果你从未使用过 Azure PowerShell，请参阅 [How to Install and Configure Azure PowerShell](../articles/powershell-install-configure.md)（如何安装和配置 Azure PowerShell），并始终按照说明进行操作，以登录到 Azure 并选择你的订阅。
2. 如有必要，创建一个新的资源组，如下所示。对于我们的方案，创建一个名为 *TestRG* 的资源组。有关资源组的详细信息，请访问 [Azure 资源管理器概述](../articles/resource-group-overview.md)。
   
        New-AzureRmResourceGroup -Name TestRG -Location centralus
   
    预期输出：
   
        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG    
3. 创建名为 *TestVNet* 的新 VNet，如下所示。
   
        New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
            -AddressPrefix 192.168.0.0/16 -Location centralus    
   
    预期输出：
   
        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                   : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState          : Succeeded
        Tags                       : 
        AddressSpace               : {
                                   "AddressPrefixes": [
                                     "192.168.0.0/16"
                                   ]
                                  }
        DhcpOptions                : {}
        Subnets                    : []
        VirtualNetworkPeerings     : []
4. 将虚拟网络对象存储在变量中，如下所示。
   
        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
   
   > [!TIP]
   > 可通过运行 **$vnet = New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus** 合并步骤 3 和步骤 4。
   > 
   > 
5. 将子网添加到新的 VNet 变量中，如下所示。
   
        Add-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd `
            -VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
   
    预期输出：
   
        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                  : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState     : Succeeded
        Tags                  :
        AddressSpace          : {
                                  "AddressPrefixes": [
                                    "192.168.0.0/16"
                                  ]
                                }
        DhcpOptions           : {}
        Subnets             : [
                                  {
                                    "Name": "FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24"
                                  }
                                ]
        VirtualNetworkPeerings     : []
6. 对要创建的每个子网重复执行上面的步骤 5。对于我们的方案，以下命令将创建 *BackEnd* 子网。
   
        Add-AzureRmVirtualNetworkSubnetConfig -Name BackEnd `
            -VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24
7. 尽管你创建了子网，但它们当前仅存在于用于检索你在上面的步骤 4 中创建的 VNet 的本地变量中。若要将所做的更改保存到 Azure，请运行 **Set-AzureRmVirtualNetwork** cmdlet（如下所示）。
   
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet    
   
    预期输出：
   
        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                  : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState     : Succeeded
        Tags                  :
        AddressSpace          : {
                                  "AddressPrefixes": [
                                    "192.168.0.0/16"
                                  ]
                                }
        DhcpOptions           : {
                                  "DnsServers": []
                                }
        Subnets               : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [],
                                    "ProvisioningState": "Succeeded"
                                  },
                                  {
                                    "Name": "BackEnd",
                                    "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                    "AddressPrefix": "192.168.2.0/24",
                                    "IpConfigurations": [],
                                    "ProvisioningState": "Succeeded"
                                  }
                                ]
        VirtualNetworkPeerings : []

<!---HONumber=AcomDC_0921_2016-->