---
title: "通过网络虚拟设备创建用户定义的路由，对网络流量进行路由 - Azure 门户 | Microsoft Docs"
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
ms.openlocfilehash: 0319029277091611673f15c94604604850cbfcbe
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2018
---
# <a name="create-a-user-defined-route---azure-portal"></a>创建用户定义的路由 - Azure 门户

本教程介绍如何创建用户定义的路由，以通过网络虚拟设备在两个[虚拟网络](virtual-networks-overview.md)子网之间路由流量。 网络虚拟设备是运行防火墙等网络应用程序的虚拟机。 若要深入了解可在 Azure 虚拟网络中部署的预配置网络虚拟设备，请参阅 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances)。

在虚拟网络中创建子网时，Azure 会创建默认的[系统路由](virtual-networks-udr-overview.md#system-routes)，以使所有子网中的资源彼此通信，如下图所示：

![默认路由](./media/create-user-defined-route/default-routes.png)

在本教程中，会创建具有公共子网、专用子网和 DMZ 子网的虚拟网络，如下图所示。 通常，Web 服务器可能会部署到公共子网，应用程序或数据库服务器可能会部署到专用子网。 创建一个虚拟机充当 DMZ 子网中的网络虚拟设备，（可选）并在每个子网中创建一个通过网络虚拟设备通信的虚拟机。 公共子网和专用子网之间的所有流量均通过该设备进行路由，如下图所示：

![用户定义路由](./media/create-user-defined-route/user-defined-routes.png)

本文提供通过资源管理器部署模型（创建用户定义的路由时建议使用的部署模型）创建用户定义的路由的步骤。 如需创建用户定义的路由（经典），请参阅[创建用户定义的路由（经典）](virtual-network-create-udr-classic-ps.md)。 如果不熟悉 Azure 的部署模型，请阅读[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 若要深入了解用户定义的路由，请参阅[用户定义的路由概述](virtual-networks-udr-overview.md#user-defined)。

## <a name="create-routes-and-network-virtual-appliance"></a>创建路由和网络虚拟设备

1. **先决条件**：通过完成[创建虚拟网络](#create-a-virtual-network)中的步骤，创建包含两个子网的虚拟网络。
2. 创建虚拟网络之后，在 Internet 浏览器中转到 [Azure 门户](https://portal.azure.com)。 使用 [Azure 帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录。
3. 在门户顶部的“搜索资源”框中输入 myResourceGroup。 当“myResourceGroup”出现在搜索结果中时，请单击它。 已按先决条件要求创建资源组。
4. 单击“myVnet”，如下图所示：

    ![网络接口设置](./media/create-user-defined-route/virtual-network.png)

5. 创建网络虚拟设备的子网：
 
    - 在“myVnet”下，左侧“设置”下单击“子网”。
    - 单击“+ 子网”，如下图所示：

        ![子网](./media/create-user-defined-route/subnets.png) 
    - 在“添加子网”下输入下列值，然后单击“确定”：

        |设置|值|
        |-----|-----|
        |名称|外围网络|
        |地址范围（CIDR 块）|10.0.2.0/24|

6. 创建网络虚拟设备虚拟机：

    - 在门户左边，依次单击“+ 新建”、“计算”，然后单击“Windows Server 2016 Datacenter”或“Ubuntu Server 16.04 LTS”。
    - 在显示的“基本信息”边栏选项卡中，输入以下值，然后单击“确定”。

        |设置|值|
        |---|---|
        |名称|myVm-Nva|
        |用户名|azureuser|
        |密码和确认密码|输入所选密码|
        |订阅|选择订阅|
        |资源组|单击“使用现有资源组”，然后单击“myResourceGroup”|
        |Location|美国东部|
    - 在所显示的“选择大小”边栏选项卡上单击“DS1_V2 标准”，并单击“选择”。
    - 在显示的“设置”边栏选项卡上，单击“虚拟网络”。 在所显示的“选择虚拟网络”边栏选项卡中，单击“myVnet”。
    - 在“设置”边栏选项卡中，单击“子网”。 在所显示的“选择子网”边栏选项卡中单击“DMZ”。 
    - 其余设置均保留为默认值，单击“确定”。
    - 在所显示的“创建”边栏选项卡中单击“创建”。 部署虚拟机需花费数分钟。

    > [!NOTE]
    > 除创建虚拟机之外，Azure 门户还会默认创建公共 IP 地址并将其分配给虚拟机。 如果在生产环境中部署虚拟机，可选择不向虚拟机分配 IP 地址。 仍可从虚拟网络中的其他虚拟机访问网络虚拟设备。 若要了解有关公共 IP 地址的详细信息，请参阅[公共 IP 地址](virtual-network-public-ip-address.md)。

7. 为门户先前创建的网络接口分配静态专用 IP 地址及启用 IP 转发。 
    - 在门户顶部的“搜索资源”框中输入 myVm-Nva。
    - 单击出现在搜索结果中的“myVm-Nva”。
    - 在左侧“设置”下单击“网络”。
    - 在“myVm-Nva - 网络接口”下单击网络接口名称。 名称是**myvm-nva***X*，其中 *X* 是门户分配的数字。
    - 在网络接口的“设置”下，单击“IP 配置”，如下图所示：

        ![网络接口设置](./media/create-user-defined-route/network-interface-settings.png)
        
    - 单击“IP 转发”设置的“已启用”，然后单击“保存”。 必须为接收流向不是向其分配的 IP 地址的流量的每个网络接口启用 IP 转发。 启用 IP 转发会禁用网络接口的 Azure 源/目标检查。
    - 单击 IP 部署列表中的“ipconfig1”。
    - 在“ipconfig1”下单击“静态”以分配专用 IP 地址，如下图所示：

        ![IP 配置](./media/create-user-defined-route/ip-configuration.png)
    - 如上一张图片所示，在“专用 IP 地址设置”中的“IP 地址”下输入 10.0.2.4。 将静态 IP 地址分配给网络接口，并确保此地址在此网络接口连接到的虚拟机整个生命周期中不改变。 输入的地址当前未分配给网络接口所在的 DMZ 子网中的其他资源。 
    - 若要保存配置，请单击“ipconfig1”下的“保存”。 接收到门户发送的表示此网络接口已保存完毕的通知之前，请不要关闭 ipconfig1 框。
 
8. 创建两个路由表。 路由表包含零个或多个路由：

    - 在门户的左边，单击“+ 新建” > “网络” > “路由表”。
    - 在“创建路由表”中输入以下值，并单击“创建”：

        |设置|值|
        |---|---|
        |名称|myRouteTable-Public|
        |订阅|选择订阅|
        |资源组|选择“使用现有资源组”，然后单击“myResourceGroup”|
        |Location|美国东部|
    
    - 再次完成步骤 8 的上一个分步，将路由表命名为 myRouteTable-Private。
9. 将路由添加到 myRouteTable-Private 路由表中，并将此路由表与“公共”子网关联：

    - 在门户顶部的“搜索资源”框中输入 myRouteTable-Public。 当其出现在搜索结果中时，请单击“myRouteTable-Public”。
    - 在“myRouteTable-Public”下单击“设置”列表中的“路由”。
    - 在“myRouteTable-Public - 路由”下单击“+ 新建”。
    -  默认情况下，Azure 会在虚拟网络中的所有子网之间路由流量。 创建一个路由，更改 Azure 的默认路由，以便将公共子网的流量通过 NVA 路由到专用子网，而非直接路由到专用子网。 在显示的“添加路由”边栏选项卡中，输入以下值，然后单击“确定”：

        |设置|值|说明|
        |---|---|---|
        |路由名称|ToPrivateSubnet|此路由通过网络虚拟设备将流量定向到专用子网。|
        |地址前缀|10.0.1.0/24| 任何发送到以此地址前缀（10.0.1.0 - 10.0.1.254）开头的地址的流量会发送到此网络虚拟设备。|
        |下一跃点类型| 选择“虚拟设备”||
        |下一跃点地址|10.0.2.4| 网络接口的静态专用 IP 地址连接到网络虚拟设备。 可指定地址的唯一跃点类型是虚拟设备。|

    - 在“myRouteTable-Public”下，单击“设置”下的“子网”。 
    - 在“myRouteTable-Public - 子网”下单击“+ 关联”。
    - 单击“关联子网”下的“虚拟网络”，然后单击“myVnet”。
    - 单击“关联子网”下的“子网”，然后单击“选择子网”下的“公共”。 
    - 若要保存配置，请单击“关联子网”下的“确定”。 子网可以没有或有一个与其相关联的路由表。
10. 再次进行步骤 9，搜索“myRouteTable-Private”，创建一个有下列设置的路由，然后，将路由表关联到 myVnet 虚拟网络的专用子网：

    |设置|值|说明|
    |---|---|---|
    |路由名称|ToPublicSubnet|此路由将流量通过网络虚拟设备定向到公共子网。|
    |地址前缀|10.0.0.0/24| 任何发送到以此地址前缀（10.0.0.0 - 10.0.1.254）开头的地址的流量会发送到此网络虚拟设备。|
    |下一跃点类型| 选择“虚拟设备”||
    |下一跃点地址|10.0.2.4||

    专用子网和公共子网中任何资源之间的网络流量都流经网络虚拟设备。 
11. **可选：**在公共子网和专用子网中创建虚拟机，并通过完成[验证路由](#validate-routing)中的步骤，验证虚拟机之间的通信是不是通过网络虚拟设备进行路由的。 
12. **可选**：若要删除在本教程中创建的资源，请完成[删除资源](#delete-resources)中所述的步骤。

## <a name="validate-routing"></a>验证路由

1. 如果尚未验证，请完成[创建路由和网络虚拟设备](#create-routes-and-network-virtual-appliance)中所述的步骤。
2. 在随后出现的框中单击“试用”按钮，打开 Azure Cloud Shell。 如果出现提示，请使用 [Azure 帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录到 Azure。 如果没有 Azure 帐户，可以注册[免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)。 Azure Cloud Shell 是预安装了 Azure 命令行接口的免费 bash shell。 

    以下脚本会创建两个虚拟机，一个在公共子网中，另一个在专用子网中。 这些脚本还会为 NVA 操作系统中的网络接口启用 IP 转发，使操作系统能够通过网络接口路由流量。 生产 NVA 通常会在路由流量之前对其进行检查，但在本教程中，简单 NVA 只会对流量进行路由，而不会检查。 

    在随后出现的 Linux 或 Windows 脚本中单击“复制”按钮，并将脚本内容粘贴到文本编辑器中。 更改 adminPassword 变量的密码，然后将脚本粘贴到 Azure Cloud Shell。 在[创建路由和网络虚拟设备](#create-routes-and-network-virtual-appliance)的步骤 6 中已创建网络虚拟设备，对创建过程中所选的操作系统，运行该脚本。 

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
    - **可选**：若要验证 Azure 中两个虚拟机之间的下一跃点，请使用 Azure 网络观察程序的下一跃点功能。 使用网络观察程序之前，必须先为使用区域[创建 Azure 网络观察程序实例](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 本教程中使用的区域是美国东部。 启用该区域的网络观察程序实例后，输入以下命令查看公共子网和专用子网中虚拟机之间的下一跃点信息：
     
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

完成本教程后，可以删除创建的资源，以免产生使用费。 删除资源组会删除其中包含的所有资源。 打开门户后，完成以下步骤：

1. 在门户的搜索框中，输入 **myResourceGroup**。 在搜索结果中，单击“myResourceGroup”。
2. 在“myResourceGroup”边栏选项卡中，单击“删除”图标。
3. 若要确认删除，请在“键入资源组名称”框中输入 **myResourceGroup**，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

- 创建[高度可用的网络虚拟设备](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json)。
- 网络虚拟设备通常分配有多个网络接口和 IP 地址。 了解如何[将网络接口添加到现有虚拟机](virtual-network-network-interface-vm.md#vm-add-nic)以及如何[将 IP 地址添加到现有网络接口](virtual-network-network-interface-addresses.md#add-ip-addresses)。 尽管所有虚拟机大小可以至少附加两个网络接口，但每个虚拟机大小都支持最大数量的网络接口。 若要了解每个虚拟机大小支持的网络接口数，请参阅 [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) 和 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机大小。 
- 创建用户定义的路由以强制本地隧道流量经过[站点到站点 VPN 连接](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
