---
title: "创建用户定义的路由以通过网络虚拟设备路由网络流量 - PowerShell | Microsoft Docs"
description: "了解如何创建用户定义的路由，以通过网络虚拟设备路由网络流量来替代 Azure 的默认路由。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: jdial
ms.openlocfilehash: 70ddec1c7ba76ef7f42048896079e5c5fa2bf60c
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-user-defined-route---powershell"></a>创建用户定义的路由 - PowerShell

本教程介绍如何创建用户定义的路由，以通过网络虚拟设备在两个[虚拟网络](virtual-networks-overview.md)子网之间路由流量。 网络虚拟设备是运行防火墙等网络应用程序的虚拟机。 若要深入了解可在 Azure 虚拟网络中部署的预配置网络虚拟设备，请参阅 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances)。

在虚拟网络中创建子网时，Azure 会创建默认的[系统路由](virtual-networks-udr-overview.md#system-routes)，以使所有子网中的资源彼此通信，如下图所示：

![默认路由](./media/create-user-defined-route/default-routes.png)

在本教程中，会创建具有公共子网、专用子网和 DMZ 子网的虚拟网络，如下图所示。 通常，Web 服务器可能会部署到公共子网，应用程序或数据库服务器可能会部署到专用子网。 创建一个虚拟机充当 DMZ 子网中的网络虚拟设备，（可选）并在每个子网中创建一个通过网络虚拟设备通信的虚拟机。 公共子网和专用子网之间的所有流量均通过该设备进行路由，如下图所示：

![用户定义路由](./media/create-user-defined-route/user-defined-routes.png)

本文提供通过资源管理器部署模型（创建用户定义的路由时建议使用的部署模型）创建用户定义的路由的步骤。 如需创建用户定义的路由（经典），请参阅[创建用户定义的路由（经典）](virtual-network-create-udr-classic-ps.md)。 如果不熟悉 Azure 的部署模型，请阅读[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 若要深入了解用户定义的路由，请参阅[用户定义的路由概述](virtual-networks-udr-overview.md#user-defined)。

## <a name="create-routes-and-network-virtual-appliance"></a>创建路由和网络虚拟设备

可以在电脑上安装和配置 PowerShell [AzureRM](https://www.powershellgallery.com/packages/AzureRM/) 模块的最新版本，或者只需在任意脚本中单击“试用”按钮，即可在 Azure Cloud Shell 中执行脚本。 Cloud Shell 已安装 PowerShell AzureRM 模块。
 
1. **先决条件**：通过完成[创建虚拟网络](#create-a-virtual-network)中的步骤，创建包含两个子网的虚拟网络。
2. 如果从计算机运行 PowerShell，则使用 `login-azurermaccount` 命令以 [Azure 帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录到 Azure。 如果使用 Cloud Shell，则会自动登录。 Cloud Shell 可能需要重启才能从创建必备虚拟网络所用的 Bash shell 切换过来。
3. 设置在各步骤中使用的几个变量：

    ```azurepowershell-interactive
    $rgName="myResourceGroup"
    $location="eastus"
    ```

4. 在现有虚拟网络中创建 DMZ 子网：

    ```azurepowershell-interactive
    $virtualNetwork = Get-AzureRmVirtualNetwork `
      -Name myVnet `
      -ResourceGroupName $rgName 
    Add-AzureRmVirtualNetworkSubnetConfig `
      -Name 'DMZ' `
      -AddressPrefix "10.0.2.0/24" `
      -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzureRmVirtualNetwork
    ```

5. 创建网络虚拟设备虚拟机的静态公共 IP 地址。

    ```azurepowershell-interactive
    $Pip = New-AzureRmPublicIpAddress `
      -AllocationMethod Static `
      -ResourceGroupName $rgName `
      -Location $location `
      -Name myPublicIp-myVm-Nva
    
6. Create a network interface in the *DMZ* subnet, assign a static private IP address to it, and enable IP forwarding for the network interface. By assigning a static IP address to the network interface, you ensure that it doesn't change for the life of the virtual machine the network interface is attached to. IP forwarding must be enabled for each network interface that receives traffic not addressed to an IP address assigned to it.

    ```azurepowershell-interactive
    $virtualNetwork = Get-AzureRmVirtualNetwork `
      -Name myVnet `
      -ResourceGroupName $rgName
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig `
      -Name DMZ `
      -VirtualNetwork $virtualNetwork
    $nic = New-AzureRmNetworkInterface `
      -ResourceGroupName $rgName `
      -Location $location `
      -Name 'myNic-Nva' `
      -SubnetId $subnet.Id `
      -PrivateIpAddress 10.0.2.4 `
      -EnableIPForwarding `
      -PublicIpAddressId $Pip.Id 
    ```

7. 创建 NVA 虚拟机。 NVA 可以是运行 Linux 或 Windows 操作系统的虚拟机。 若要创建该虚拟机，请复制任一操作系统的脚本，并将其粘贴到 PowerShell 会话中。 如果创建 Windows VM，请将脚本粘贴到文本编辑器中，并更改 $cred 变量的“value”，然后将修改后的文本粘贴到 PowerShell 会话中：

    **Linux**

    ```azurepowershell-interactive
    # Define the admin user name and a blank password.
    $securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

    # Define the virtual machine configuration.
    $vmConfig = New-AzureRmVMConfig `
      -VMName 'myVm-Nva' `
      -VMSize Standard_DS2 | `
      Set-AzureRmVMOperatingSystem -Linux `
      -ComputerName 'myVm-Nva' `
      -Credential $cred -DisablePasswordAuthentication| `
      Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 14.04.2-LTS `
      -Version latest | `
      Add-AzureRmVMNetworkInterface -Id $nic.Id

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"    

    # Create the virtual machine
    $vm = New-AzureRmVM `
      -ResourceGroupName $rgName `
      -Location $location `
      -VM $vmConfig
    ```

    **Windows**

    ```azurepowershell-interactive
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."

    $vmConfig = New-AzureRmVMConfig `
      -VMName 'myVm-Nva' `
      -VMSize Standard_DS2 | `
      Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName 'myVm-Nva' `
      -Credential $cred | `
      Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
      Add-AzureRmVMNetworkInterface -Id $nic.Id
    
    $vm = New-AzureRmVM `
      -ResourceGroupName $rgName `
      -Location $location `
      -VM $vmConfig
    ```

8. 默认情况下，Azure 会在虚拟网络中的所有子网之间路由流量。 创建一个路由以更改 Azure 的默认路由，从而将公共子网的流量路由到 NVA，而不是直接路由到专用子网。

    ```azurepowershell-interactive    
    $routePrivate = New-AzureRmRouteConfig `
      -Name 'ToPrivateSubnet' `
      -AddressPrefix 10.0.1.0/24 `
      -NextHopType VirtualAppliance `
      -NextHopIpAddress $nic.IpConfigurations[0].PrivateIpAddress
    ```

9. 创建公共子网的路由表。

    ```azurepowershell-interactive
    $routeTablePublic = New-AzureRmRouteTable `
      -Name 'myRouteTable-Public' `
      -ResourceGroupName $rgName `
      -location $location `
      -Route $routePrivate
    ```
    
10. 将路由表关联到公共子网。 将路由表关联到子网会导致 Azure 根据路由表中的路由从子网中路由所有出站流量。 一个路由表可与零个或多个子网相关联，而一个子网可与零个或一个路由表相关联。

    ```azurepowershell-interactive
    Set-AzureRmVirtualNetworkSubnetConfig `
      -VirtualNetwork $virtualNetwork `
      -Name 'Public' `
      -AddressPrefix 10.0.0.0/24 `
      -RouteTable $routeTablePublic | `
    Set-AzureRmVirtualNetwork
    ```

11. 通过 NVA 虚拟机创建从专用子网到公共子网的流量路由。

    ```azurepowershell-interactive    
    $routePublic = New-AzureRmRouteConfig `
      -Name 'ToPublicSubnet' `
      -AddressPrefix 10.0.0.0/24 `
      -NextHopType VirtualAppliance `
      -NextHopIpAddress $nic.IpConfigurations[0].PrivateIpAddress
    ```

12. 创建专用子网的路由表。

    ```azurepowershell-interactive
    $routeTablePrivate = New-AzureRmRouteTable `
      -Name 'myRouteTable-Private' `
      -ResourceGroupName $rgName `
      -location $location `
      -Route $routePublic
    ```
      
13. 将路由表关联到专用子网。

    ```azurepowershell-interactive
    Set-AzureRmVirtualNetworkSubnetConfig `
      -VirtualNetwork $virtualNetwork `
      -Name 'Private' `
      -AddressPrefix 10.0.1.0/24 `
      -RouteTable $routeTablePrivate | `
    Set-AzureRmVirtualNetwork
    ```
    
14. **可选：**在公共子网和专用子网中创建虚拟机，并通过完成[验证路由](#validate-routing)中的步骤，验证虚拟机之间的通信是不是通过网络虚拟设备进行路由的。
15. **可选**：若要删除在本教程中创建的资源，请完成[删除资源](#delete-resources)中所述的步骤。

## <a name="validate-routing"></a>验证路由

1. 如果尚未验证，请完成[创建路由和网络虚拟设备](#create-routes-and-network-virtual-appliance)中所述的步骤。
2. 在随后出现的框中单击“试用”按钮，打开 Azure Cloud Shell。 如果出现提示，请使用 [Azure 帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录到 Azure。 如果没有 Azure 帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。 Azure Cloud Shell 是预安装了 Azure 命令行接口的免费 bash shell。 

    以下脚本会创建两个虚拟机，一个在公共子网中，另一个在专用子网中。 这些脚本还会为 NVA 操作系统中的网络接口启用 IP 转发，使操作系统能够通过网络接口路由流量。 生产 NVA 通常会在路由流量之前对其进行检查，但在本教程中，简单 NVA 只会对流量进行路由，而不会检查。 

    在随后出现的 Linux 或 Windows 脚本中单击“复制”按钮，并将脚本内容粘贴到文本编辑器中。 更改 adminPassword 变量的密码，然后将脚本粘贴到 Azure Cloud Shell。 在[创建路由和网络虚拟设备](#create-routes-and-network-virtual-appliance)的步骤 7 中已创建网络虚拟设备，对创建过程中所选的操作系统，运行该脚本。 

    **Linux**

    ```azurecli-interactive
    #!/bin/bash

    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.    
    az vm extension set \
      --resource-group $rgName \
      --vm-name myVm-Nva \
      --name customScript \
      --publisher Microsoft.Azure.Extensions \
      --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
    ```

    **Windows**

    ```azurecli-interactive

    #!/bin/bash
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Public \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Private \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1"}'

    # Restart the NVA virtual machine.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Restart-Computer -ComputerName myVm-Nva -Force"}'
    ```

3. 验证公共子网和专用子网中虚拟机之间的通信。 

    - 打开与 myVm-Public 虚拟机公共 IP 地址相连的 [SSH](../virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Linux) 或[远程桌面](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Windows) 连接。
    - 在 myVm-Public 虚拟机的命令提示符中输入 `ping myVm-Private`。 由于 NVA 将公共子网的流量路由到专用子网，因此你会收到答复。
    - 在 myVm-Public 虚拟机中，运行公共子网和专用子网的虚拟机之间的跟踪路由。 根据你在公共子网和专用子网的虚拟机中安装的操作系统，输入以下的相应命令：
        - **Windows**：从命令提示符处，运行 `tracert myvm-private` 命令。
        - **Ubuntu**：运行 `tracepath myvm-private` 命令。
      流量会先通过 10.0.2.4 (NVA)，然后才到达 10.0.1.4（专用子网中的虚拟机）。 
    - 连接到 myVm-Private 虚拟机并 ping myVm-Private 虚拟机，完成以上步骤。 跟踪路由显示先通过 10.0.2.4 再到达 10.0.0.4（公共子网中的虚拟机）的通信。
    
      > [!NOTE]
      > 使用前面的步骤，可以确认 Azure 专用 IP 地址之间的路由。 如果想要转发或代理通过网络虚拟设备发送到公共 IP 地址的流量：
      > - 设备必须提供网络地址转换或代理功能。 如果进行网络地址转换，设备必须将源 IP 地址转换为其自己的地址，然后再将该请求转发到公共 IP 地址。 无论设备是对源地址进行网络地址转换还是进行代理，Azure 都会将网络虚拟设备的专用 IP 地址转换为公共 IP 地址。 有关 Azure 用于将专用 IP 地址转换为公共 IP 地址的不同方法的详细信息，请参阅[了解出站连接](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
      > - 路由表中的其他路由，如前缀 0.0.0.0/0、下一跃点类型 VirtualAppliance 和下一跃点 IP 地址 10.0.2.4（在前面的示例脚本中）。
      >
    - **可选**：使用 Azure 网络观察程序的下一跃点功能验证 Azure 中两个虚拟机之间的下一跃点。 使用网络观察程序之前，必须先为使用区域[创建 Azure 网络观察程序实例](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 本教程中使用的区域是美国东部。 启用该区域的网络观察程序实例后，输入以下命令查看公共子网和专用子网中虚拟机之间的下一跃点信息：
     
        ```azurecli-interactive
        az network watcher show-next-hop --resource-group myResourceGroup --vm myVm-Public --source-ip 10.0.0.4 --dest-ip 10.0.1.4
        ```

       输出返回 10.0.2.4 作为 nextHopIpAddress，并返回 VirtualAppliance 作为 nextHopType。

> [!NOTE]
> 为了说明本教程中的这些概念，我们将公共 IP 地址分配到公共子网和专用子网中的虚拟机，并在 Azure 中为这两个虚拟机启用了所有网络端口访问。 创建供生产使用的虚拟机时，可能不会为其分配公共 IP 地址，还有可能会通过向其部署网络虚拟设备或将网络安全组分配给子网、网络接口或同时分配给二者来筛选到专用子网的网络流量。 若要深入了解网络安全组，请参阅[网络安全组](virtual-networks-nsg.md)。

## <a name="create-a-virtual-network"></a>创建虚拟网络

本教程需要包含两个子网的现有虚拟网络。 在随后出现的框中单击“试用”按钮，快速创建虚拟网络。 单击“试用”按钮，打开 [Azure Cloud Shell](../cloud-shell/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 尽管 Cloud Shell 运行 PowerShell 或 Bash shell，但在本节中，Bash shell 用于创建虚拟网络。 Bash shell 已安装 Azure 命令行接口。 如果 Cloud Shell 出现提示，请使用 [Azure 帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录到 Azure。 如果没有 Azure 帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。 若要创建本教程中使用的虚拟网络，请在以下框中单击“复制”按钮，然后将该脚本粘贴到 Azure Cloud Shell：

```azurecli-interactive
#!/bin/bash

#Set variables used in the script.
rgName="myResourceGroup"
location="eastus"

# Create a resource group.
az group create \
  --name $rgName \
  --location $location

# Create a virtual network with one subnet named Public.
az network vnet create \
  --name myVnet \
  --resource-group $rgName \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24

# Create an additional subnet named Private in the virtual network.
az network vnet subnet create \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --vnet-name myVnet \
  --resource-group $rgName
```

若要深入了解如何使用门户、PowerShell 或 Azure 资源管理器模板创建虚拟网络，请参阅[创建虚拟网络](virtual-networks-create-vnet-arm-pportal.md)。

## <a name="delete-resources"></a>删除资源

完成本教程后，可以删除创建的资源，以免产生使用费。 删除资源组会删除其中包含的所有资源。 在 PowerShell 中输入以下命令：

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

- 创建[高度可用的网络虚拟设备](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json)。
- 网络虚拟设备通常分配有多个网络接口和 IP 地址。 了解如何[将网络接口添加到现有虚拟机](virtual-network-network-interface-vm.md#vm-add-nic)以及如何[将 IP 地址添加到现有网络接口](virtual-network-network-interface-addresses.md#add-ip-addresses)。 尽管所有虚拟机大小可以至少附加两个网络接口，但每个虚拟机大小都支持最大数量的网络接口。 若要了解每个虚拟机大小支持的网络接口数，请参阅 [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) 和 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机大小。 
- 创建用户定义的路由以强制本地隧道流量经过[站点到站点 VPN 连接](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
