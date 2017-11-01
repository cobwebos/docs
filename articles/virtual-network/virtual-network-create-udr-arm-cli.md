---
title: "创建用户定义的路由以通过网络虚拟设备路由网络流量 - Azure CLI | Microsoft Docs"
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
ms.openlocfilehash: b41754e74708a095d95eee31da5e304771a6ae05
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2017
---
# <a name="create-a-user-defined-route---azure-cli"></a>创建用户定义的路由 - Azure CLI

本教程介绍如何创建用户定义的路由，以通过网络虚拟设备在两个[虚拟网络](virtual-networks-overview.md)子网之间路由流量。 网络虚拟设备是运行防火墙等网络应用程序的虚拟机。 若要深入了解可在 Azure 虚拟网络中部署的预配置网络虚拟设备，请参阅 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances)。

在虚拟网络中创建子网时，Azure 会创建默认的[系统路由](virtual-networks-udr-overview.md#system-routes)，以使所有子网中的资源彼此通信，如下图所示：

![默认路由](./media/create-user-defined-route/default-routes.png)

在本教程中，会创建具有公共子网、专用子网和 DMZ 子网的虚拟网络，如下图所示。 通常，Web 服务器可能会部署到公共子网，应用程序或数据库服务器可能会部署到专用子网。 创建一个虚拟机充当 DMZ 子网中的网络虚拟设备，（可选）并在每个子网中创建一个通过网络虚拟设备通信的虚拟机。 公共子网和专用子网之间的所有流量均通过该设备进行路由，如下图所示：

![用户定义路由](./media/create-user-defined-route/user-defined-routes.png)

本文提供通过资源管理器部署模型（创建用户定义的路由时建议使用的部署模型）创建用户定义的路由的步骤。 如需创建用户定义的路由（经典），请参阅[创建用户定义的路由（经典）](virtual-network-create-udr-classic-cli.md)。 如果不熟悉 Azure 的部署模型，请阅读[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 若要深入了解用户定义的路由，请参阅[用户定义的路由概述](virtual-networks-udr-overview.md#user-defined-routes)。

## <a name="create-routes-and-network-virtual-appliance"></a>创建路由和网络虚拟设备

无论是从 Windows、 Linux 还是 macOS 执行命令，Azure CLI 命令都相同。 不过在操作系统 shell 之间存在脚本差异。 在 Bash shell 中进行所需的安装操作和执行 Azure CLI 命令时需执行以下步骤中的脚本。 可以在电脑上[安装和配置 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)，或者只需在任意脚本中单击“试用”按钮，即可在 Azure Cloud Shell 中执行脚本。
 
1. **先决条件**：通过完成[创建虚拟网络](#create-a-virtual-network)中的步骤，创建包含两个子网的虚拟网络。
2. 如果从计算机运行 Azure CLI，则使用 `az login` 命令登录到 Azure。 如果使用 Cloud Shell，则会自动登录。
3. 设置几个在剩余步骤中使用的变量：

    ```azurecli-interactive
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    ```

4. 在先决条件中创建的虚拟网络内创建 DMZ 子网：

    ```azurecli-interactive
    az network vnet subnet create \
      --name DMZ \
      --address-prefix 10.0.2.0/24 \
      --vnet-name myVnet \
      --resource-group $rgName
    ```

5. 创建 NVA 虚拟机。 将静态公共和专用 IP 地址分配给 CLI 创建的网络接口。 在虚拟机的生命周期中，静态地址不会更改。 NVA 可以是运行 Linux 或 Windows 操作系统的虚拟机。 若要创建该虚拟机，请复制任一操作系统的脚本，并将其粘贴到 CLI 中。 如果创建 Windows VM，请将脚本粘贴到文本编辑器中，并更改 AdminPassword 变量的“value”，然后将修改后的文本粘贴到 CLI 中。

    **Linux**

    ```azurecli-interactive
    az vm create \
      --resource-group $rgName \
      --name myVm-Nva \
      --image UbuntuLTS \
      --private-ip-address 10.0.2.4 \
      --public-ip-address myPublicIp-myVm-Nva \
      --public-ip-address-allocation static \
      --subnet DMZ \
      --vnet-name myVnet \
      --admin-username azureuser \
      --generate-ssh-keys
    ```

    **Windows**

    ```azurecli-interactive
    AdminPassword=ChangeToYourPassword
    az vm create \
      --resource-group $rgName \
      --name myVm-Nva \
      --image win2016datacenter \
      --private-ip-address 10.0.2.4 \
      --public-ip-address myPublicIp-myVm-Nva \
      --public-ip-address-allocation static \
      --subnet DMZ \
      --vnet-name myVnet \
      --admin-username azureuser \
      --admin-password $AdminPassword      
    ```

6. 启用 NVA 网络接口的 IP 转发。 启用网络接口的 IP 转发会导致 Azure 不检查源/目标 IP 地址。 如果不启用此设置，Azure 会放弃指定流向某个 IP 地址而实际流向 NIC 的流量。

    ```azurecli-interactive
    az network nic update \
      --name myVm-NvaVMNic \
      --resource-group $rgName \
      --ip-forwarding true
    ```

7. 创建公共子网的路由表。

    ```azurecli-interactive
    az network route-table create \
      --name myRouteTable-Public \
      --resource-group $rgName
    ```
    
8. 默认情况下，Azure 会在虚拟网络中的所有子网之间路由流量。 创建一个路由以更改 Azure 的默认路由，从而通过 NVA 将公共子网的流量路由到专用子网，而非直接路由到专用子网。

    ```azurecli-interactive    
    az network route-table route create \
      --name ToPrivateSubnet \
      --resource-group $rgName \
      --route-table-name myRouteTable-Public \
      --address-prefix 10.0.1.0/24 \
      --next-hop-type VirtualAppliance \
      --next-hop-ip-address 10.0.2.4
    ```

9. 将 myRouteTable-Public 路由表关联到公共子网。 将路由表关联到子网会导致 Azure 根据路由表中的路由从子网中路由所有出站流量。 一个路由表可与零个或多个子网相关联，而一个子网可与零个或一个路由表相关联。

    ```azurecli-interactive
    az network vnet subnet update \
      --name Public \
      --vnet-name myVnet \
      --resource-group $rgName \
      --route-table myRouteTable-Public
    ```

10. 创建专用子网的路由表。

    ```azurecli-interactive
    az network route-table create \
      --name myRouteTable-Private \
      --resource-group $rgName
    ```
      
11. 创建路由，以便通过 NVA 虚拟机将专用子网的流量路由到公共子网。

    ```azurecli-interactive
    az network route-table route create \
      --name ToPublicSubnet \
      --resource-group $rgName \
      --route-table-name myRouteTable-Private \
      --address-prefix 10.0.0.0/24 \
      --next-hop-type VirtualAppliance \
      --next-hop-ip-address 10.0.2.4
    ```

12. 将路由表关联到专用子网。

    ```azurecli-interactive
    az network vnet subnet update \
      --name Private \
      --vnet-name myVnet \
      --resource-group $rgName \
      --route-table myRouteTable-Private
    ```
    
13. **可选：**在公共子网和专用子网中创建虚拟机，并通过完成[验证路由](#validate-routing)中的步骤，验证虚拟机之间的通信是不是通过网络虚拟设备进行路由的。
14. **可选**：若要删除在本教程中创建的资源，请完成[删除资源](#delete-resources)中所述的步骤。

## <a name="validate-routing"></a>验证路由

1. 如果尚未验证，请完成[创建路由和网络虚拟设备](#create-routes-and-network-virtual-appliance)中所述的步骤。
2. 在随后出现的框中单击“试用”按钮，打开 Azure Cloud Shell。 如果出现提示，请使用 [Azure 帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录到 Azure。 如果没有 Azure 帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。 Azure Cloud Shell 是预安装了 Azure 命令行接口的免费 bash shell。 

    以下脚本会创建两个虚拟机，一个在公共子网中，另一个在专用子网中。 这些脚本还会为 NVA 操作系统中的网络接口启用 IP 转发，使操作系统能够通过网络接口路由流量。 生产 NVA 通常会在路由流量之前对其进行检查，但在本教程中，简单 NVA 只会对流量进行路由，而不会检查。 

    在随后出现的 Linux 或 Windows 脚本中单击“复制”按钮，并将脚本内容粘贴到文本编辑器中。 更改 adminPassword 变量的密码，然后将脚本粘贴到 Azure Cloud Shell。 针对创建网络虚拟设备时（[创建路由和网络虚拟设备](#create-routes-and-network-virtual-appliance)中的步骤 5）多选的操作系统运行改脚本。 

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

完成本教程后，可以删除创建的资源，以免产生使用费。 删除资源组会删除其中包含的所有资源。 在 CLI 会话中输入以下命令：

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>后续步骤

- 创建[高度可用的网络虚拟设备](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json)。
- 网络虚拟设备通常分配有多个网络接口和 IP 地址。 了解如何[将网络接口添加到现有虚拟机](virtual-network-network-interface-vm.md#vm-add-nic)以及如何[将 IP 地址添加到现有网络接口](virtual-network-network-interface-addresses.md#add-ip-addresses)。 尽管所有虚拟机大小可以至少附加两个网络接口，但每个虚拟机大小都支持最大数量的网络接口。 若要了解每个虚拟机大小支持的网络接口数，请参阅 [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) 和 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机大小。 
- 创建用户定义的路由以强制本地隧道流量经过[站点到站点 VPN 连接](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
