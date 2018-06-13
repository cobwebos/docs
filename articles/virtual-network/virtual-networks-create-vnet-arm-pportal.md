---
title: 创建包含多个子网的 Azure 虚拟网络 - 门户 | Microsoft Docs
description: 了解如何使用 Azure 门户创建包含多个子网的虚拟网络。
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 898bdef779282d7312c76696f744b97ec2dfcded
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880477"
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-portal"></a>使用 Azure 门户创建包含多个子网的虚拟网络

虚拟网络能让几种类型的 Azure 资源与 Internet 进行通信以及彼此之间私下通信。 在虚拟网络中创建多个子网即可对网络进行划分，以便筛选或控制子网之间的通信流。 本文介绍如何执行以下操作：

> [!div class="checklist"]
> * 创建虚拟网络
> * 创建子网
> * 测试虚拟机之间的网络通信

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="log-in-to-azure"></a>登录 Azure 

通过 http://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-virtual-network"></a>创建虚拟网络

1. 选择 Azure 门户左上角的“+ 创建资源”。
2. 选择“网络”，然后选择“虚拟网络”。
3. 如下图所示，对于“名称”，输入“myVirtualNetwork”，对于“地址空间”，输入“10.0.0.0/16”，对于“资源组”，输入“myResourceGroup”，对于子网“名称”，输入“Public”，对于子网“地址范围”，输入 10.0.0.0/24，选择一个位置和你的订阅，接受其余默认设置，然后选择“创建”：

    ![创建虚拟网络](./media/virtual-networks-create-vnet-arm-pportal/create-virtual-network.png)

    “地址空间”和“地址范围”是以 CIDR 表示法指定的。 指定的“地址空间”包括 IP 地址 10.0.0.0-10.0.255.254。 为子网指定的“地址范围”必须在为虚拟网络定义的“地址空间”内。 Azure DHCP 将子网地址范围中的 IP 地址分配给在子网中部署的资源。 Azure 只将地址 10.0.0.4-10.0.0.254 分配给 **Public** 子网中部署的资源，因为 Azure 会在每个子网中保留前四个地址（10.0.0.0-10.0.0.3，用于此示例中的子网）和最后一个地址（10.0.0.255，用于此示例中的子网）。

## <a name="create-a-subnet"></a>创建子网

1. 在门户顶部的“搜索资源、服务和文档”框中，开始键入“myVirtualNetwork”。 当“myVirtualNetwork”出现在搜索结果中时，将其选中。
2. 选择“子网”，然后选“+ 子网”择，如下图中所示：

     ![添加子网](./media/virtual-networks-create-vnet-arm-pportal/add-subnet.png)
     
3. 在出现的“添加子网”框中，对于“名称”，输入“Private”，对于“地址范围”，输入“10.0.1.0/24”，然后选择“确定”。  子网地址范围不能与虚拟网络中其他子网的地址范围重叠。 

在部署 Azure 虚拟网络和子网供生产用之前，建议你全面了解地址空间[注意事项](manage-virtual-network.md#create-a-virtual-network)和[虚拟网络限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 将资源部署到子网后，如果对虚拟网络和子网进行某些更改（例如更改地址范围），则可能需要重新部署子网中部署的现有 Azure 资源。

## <a name="test-network-communication"></a>测试网络通信

虚拟网络能让几种类型的 Azure 资源与 Internet 进行通信以及彼此之间私下通信。 虚拟机是一种能部署到虚拟网络的资源类型。 在虚拟网络中创建两个虚拟机，以便在稍后的步骤中测试其与 Internet 的通信。

### <a name="create-virtual-machines"></a>创建虚拟机

1. 选择 Azure 门户左上角的“+ 创建资源”。
2. 选择“计算”，然后选择“Windows Server 2016 Datacenter”。 可以选择不同的操作系统，但剩余步骤假定你选择了“Windows Server 2016 Datacenter”。 
3. 对于“基本信息”，选择或输入以下信息，然后选择“确定”：
    - **名称**：*myVmWeb*
    - **资源组**：选择“使用现有”，然后选择“myResourceGroup”。
    - **位置**：选择“美国东部”。

    稍后的步骤中将使用你输入的“用户名”和“密码”。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。 所选的“位置”和“订阅”必须与虚拟网络所在的位置和订阅相同。 不要求选择在其中创建了虚拟网络的同一资源组，但本教程中选择了同一资源组。
4. 在“选择大小”下选择 VM 大小。
5. 对于“设置”，选择或输入以下信息，然后选择“确定”：
    - **虚拟网络**：确保选择“myVirtualNetwork”。 如果没有，请选择“虚拟网络”，然后在“选择虚拟网络”下选择“myVirtualNetwork”。
    - **子网**：确保选择“Public”。 如果没有，请选择“子网”，然后在“选择子网”下选择“Public”，如下图所示：
    
        ![虚拟机设置](./media/virtual-networks-create-vnet-arm-pportal/virtual-machine-settings.png)
 
6. 在“摘要”中的“创建”下，选择“创建”以启动虚拟机部署。
7. 再次完成步骤 1-6，但对于虚拟机的“名称”，输入“myVmMgmt”，对于“子网”，选择“Private”。

创建虚拟机需花费几分钟的时间。 在两个虚拟机完成创建之前，不要继续执行剩余的步骤。

本文中创建的虚拟机有一个[网络接口](virtual-network-network-interface.md)，该网络接口有一个动态分配给它的 IP 地址。 部署 VM 后，可以[添加多个公用 IP 地址和专用 IP 地址，或将 IP 地址分配方法更改为“静态”](virtual-network-network-interface-addresses.md#add-ip-addresses)。 可以[添加网络接口](virtual-network-network-interface-vm.md#vm-add-nic)，但其数目不能超过创建虚拟机时选择的 [VM 大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)所允许的限制。 还可以为 VM [启用单根 I/O 虚拟化 (SR-IOV)](create-vm-accelerated-networking-powershell.md)，但前提是创建的 VM 的 VM 大小支持该功能。

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>在虚拟机之间以及与 Internet 进行通信

1. 在门户顶部的“搜索”框中，开始键入“myVmMgmt”。 当“myVmMgmt”出现在搜索结果中时，将其选中。
2. 通过选择“连接”创建到 *myVmMgmt* 虚拟机的远程桌面连接，如下图中所示：

    ![连接到虚拟机](./media/virtual-networks-create-vnet-arm-pportal/connect-to-virtual-machine.png)  

3. 若要连接到 VM，请打开已下载的 RDP 文件。 出现提示时，选择“连接”。
4. 输入在创建虚拟机时指定的用户名和密码（可能需要选择“更多选择”，然后选择“使用其他帐户”，以便指定在创建虚拟机时输入过的凭据），然后选择“确定”。
5. 你可能会在登录过程中收到证书警告。 选择“是”以继续进行连接。
6. 在后面的步骤中，将使用 ping 从 *myVmWeb* 虚拟机与 *myVmMgmt* 虚拟机进行通信。 Ping 使用默认情况下无法通过 Windows 防火墙的 ICMP。 在命令提示符处输入以下命令，允许 ICMP 通过 Windows 防火墙：

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    虽然本文中使用 ping，但在进行生产部署时，不建议允许 ICMP 通过 Windows 防火墙。
7. 出于安全原因，通常会限制虚拟网络中可远程连接到的虚拟机数。 在本教程中，*myVmMgmt* 虚拟机用于管理虚拟网络中的 *myVmWeb* 虚拟机。 若要从 *myVmMgmt* 虚拟机建立与 *myVmWeb* 虚拟机的远程桌面连接，请在命令提示符处输入以下命令：

    ``` 
    mstsc /v:myVmWeb
    ```
8. 若要从 *myVmWeb* 虚拟机与 *myVmMgmt* 虚拟机进行通信，请在命令提示符处输入以下命令：

    ```
    ping myvmmgmt
    ```

    收到的输出类似于以下示例输出：
    
    ```
    Pinging myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net [10.0.1.4] with 32 bytes of data:
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    
    Ping statistics for 10.0.1.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 0ms, Average = 0ms
    ```
      
    可以看到 *myVmMgmt* 虚拟机的地址是 10.0.1.4。 在之前的步骤中，已将 *myVmMgmt* 虚拟机部署到一个专用子网，而 10.0.1.4 则是该专用子网的地址范围中的第一个可用 IP 地址。  可以看到，该虚拟机的完全限定域名是 *myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net*。 尽管该域名的 *dar5p44cif3ulfq00wxznl3i3f* 部分不同于你的虚拟机，但其余部分是相同的。 

    默认情况下，所有 Azure 虚拟机均使用默认的 Azure DNS 服务。 虚拟网络中的所有虚拟机均可使用 Azure 的默认 DNS 服务解析同一虚拟网络中所有其他虚拟机的名称。 可以不使用 Azure 的默认 DNS 服务，而使用自己的 DNS 服务器或 Azure DNS 服务的专用域功能。 有关详细信息，请参阅[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)或[对专用域使用 Azure DNS](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

9. 若要在 *myVmWeb* 虚拟机上安装用于 Windows Server 的 Internet Information Services (IIS)，请在 PowerShell 会话中输入以下命令：

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

10. 安装完 IIS 以后，请断开 *myVmWeb* 远程桌面会话的连接，留在 *myVmMgmt* 远程桌面会话中。 打开 Web 浏览器并浏览到 http://myvmweb。 此时会看到 IIS 欢迎页。
11. 断开 *myVmMgmt* 远程桌面会话的连接。
12. 查找 *myVmWeb* 虚拟机的公用 IP 地址。 Azure 创建 *myVmWeb* 虚拟机时，还创建了一个名为 *myVmWeb* 的公用 IP 地址资源并将其分配给了该虚拟机。 在步骤 2 中的图片中可以看到，为 *myVmMgmt* 虚拟机的**公用 IP 地址**分配了 52.170.5.92。 若要查找分配给 *myVmWeb* 虚拟机的公用 IP 地址，请在门户的搜索框中搜索“myVmWeb”，然后，在它出现在搜索结果中时，将其选中。

    尽管虚拟机不需要分配有公用 IP 地址，但默认情况下 Azure 仍会为你创建的每个虚拟机分配一个公用 IP 地址。 若要通过 Internet 与虚拟机进行通信，必须为虚拟机分配公用 IP 地址。 所有虚拟机都可以与 Internet 进行出站通信，无论是否为虚拟机分配了公用 IP 地址。 若要详细了解 Azure 中的出站 Internet 连接，请参阅 [Azure 中的出站连接](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
13. 在你自己的计算机上，浏览到 *myVmWeb* 虚拟机的公用 IP 地址。 尝试从自己的计算机查看 IIS 欢迎页失败。 尝试之所以失败，是因为在部署虚拟机时，Azure 已默认为每个虚拟机创建网络安全组。 

     网络安全组包含安全规则，这些规则可根据端口和 IP 地址允许或拒绝入站和出站网络流量。 Azure 创建的默认网络安全组允许通过同一虚拟网络中资源之间的所有端口进行通信。 对于 Windows 虚拟机，默认网络安全组会拒绝通过所有端口来自 Internet 的所有入站流量，TCP 端口 3389 (RDP) 除外。 因此，默认情况下，还可以通过 RDP 直接从 Internet 连接到 *myVmWeb* 虚拟机。当然，你可能不希望向 Web 服务器开放端口 3389。 由于在进行 Web 浏览时是通过端口 80 通信，因此如果从 Internet 通信失败，则是因为默认网络安全组中没有允许流量通过端口 80 的规则。

## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，可将资源组及其包含的所有资源一并删除： 

1. 在门户顶部的“搜索”框中输入“myResourceGroup”。 当在搜索结果中看到“myResourceGroup”时，将其选中。
2. 选择“删除资源组”。
3. 对于“键入资源组名称:”，输入“myResourceGroup”，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何部署具有多个子网的虚拟网络。 你还了解了创建 Windows 虚拟机时，Azure 将创建一个网络接口并将它附加到该虚拟机，并且还创建一个只允许通过端口 3389 来自 Internet 的流量的网络安全组。 学习下一个教程，了解如何筛选流向子网（而不是流向单个虚拟机）的网络流量。

> [!div class="nextstepaction"]
> [筛选流向子网的网络流量](./virtual-networks-create-nsg-arm-pportal.md)
