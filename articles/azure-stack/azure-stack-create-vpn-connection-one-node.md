---
title: "创建在不同的 Azure 堆栈开发工具包环境中的两个虚拟网络之间的站点到站点 VPN 连接 |Microsoft 文档"
description: "云管理员用于创建两个单节点 Azure 堆栈开发工具包环境之间的站点到站点 VPN 连接的分步过程。"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: scottnap
ms.openlocfilehash: fa2a940620e06521fa110fa13dcbc3050635a502
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-development-kit-environments"></a>创建在不同的 Azure 堆栈开发工具包环境中的两个虚拟网络之间的站点到站点 VPN 连接
## <a name="overview"></a>概述
这篇文章演示了如何创建在两个单独的 Azure 堆栈开发工具包环境中的两个虚拟网络之间的站点到站点 VPN 连接。 虽然配置连接时，你了解 Azure 堆栈中的 VPN 网关的工作方式。

### <a name="connection-diagram"></a>连接关系图
下图显示什么连接配置应如下所示完成后。

![站点到站点 VPN 连接配置](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>开始之前
若要完成的连接配置，确保你拥有以下项目，在开始之前：

* 满足 Azure 堆栈开发工具包硬件要求，从而通过定义的两个服务器[Azure 堆栈部署先决条件](azure-stack-deploy.md)。 确保在出现的其他先决条件[文章](azure-stack-deploy.md)太满足。
* [Azure 堆栈开发工具包](https://azure.microsoft.com/en-us/overview/azure-stack/try/)部署包。

## <a name="deploy-the-azure-stack-development-kit-environments"></a>部署 Azure 堆栈开发工具包环境
若要完成的连接配置，你必须部署两个 Azure 堆栈开发工具包环境。
> [!NOTE] 
> 对于每个部署的 Azure 堆栈开发工具包，请按照[部署说明](azure-stack-run-powershell-script.md)。 在本文中，称为 Azure 堆栈开发工具包环境*POC1*和*POC2*。


## <a name="prepare-an-offer-on-poc1-and-poc2"></a>准备 POC1 和 POC2 产品/服务
在 POC1 和 POC2，准备提议，以便用户可以订阅到产品/服务和部署虚拟机。 有关如何创建产品的信息，请参阅[让 Azure 堆栈用户能够使用虚拟机](azure-stack-tutorial-tenant-vm.md)。

## <a name="review-and-complete-the-network-configuration-table"></a>查看并完成网络配置表
下表总结了这两个 Azure 堆栈开发工具包环境的网络配置。 使用显示在要添加特定于你的网络的外部 BGPNAT 地址的表后面的过程。

**网络配置表**
|   |POC1|POC2|
|---------|---------|---------|
|虚拟网络名称     |VNET-01|VNET-02 |
|虚拟网络地址空间 |10.0.10.0/23|10.0.20.0/23|
|子网名称     |子网-01|子网-02|
|子网地址范围|10.0.10.0/24 |10.0.20.0/24 |
|网关子网     |10.0.11.0/24|10.0.21.0/24|
|外部 BGPNAT 地址     |         |         |

> [!NOTE]
> 示例环境中的外部 BGPNAT IP 地址是为 POC1，10.16.167.195 和 POC2 的 10.16.169.131。 使用以下过程来为你的 Azure 堆栈开发工具包主机，确定外部 BGPNAT IP 地址，然后将它们添加到以前的网络配置表。


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>获取 NAT VM 的外部适配器的 IP 地址
1. 登录到 Azure 堆栈物理机 POC1。
2. 编辑以下的 Powershell 代码，以将你的管理员密码，然后运行 POC 主机上的代码:

   ```powershell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "AzS-bgpnat01" `
    -Password $Password
   ```
3. 将 IP 地址添加到上一节中出现的网络配置表。

4. 上重复此过程 POC2。

## <a name="create-the-network-resources-in-poc1"></a>在 POC1 中创建的网络资源
现在，您创建您需要设置您的网关向上 POC1 网络资源。 下面的说明演示了如何通过使用用户门户中创建资源。 PowerShell 代码还可用于创建资源。

![用于创建资源的工作流](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>作为租户登录
服务管理员可以登录作为租户以测试计划、 服务和其租户可能使用的订阅。 如果你还没有一个，[创建的租户帐户](azure-stack-add-new-user-aad.md)在登录之前。

### <a name="create-the-virtual-network-and-vm-subnet"></a>创建虚拟网络和 VM 子网
1. 使用租户帐户登录到用户门户。
2. 在用户门户中，选择**新建**。

    ![创建新的虚拟网络](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)

3. 转到**Marketplace**，然后选择**网络**。
4. 选择**虚拟网络**。
5. 有关**名称**，**地址空间**，**子网名称**，和**子网地址范围**，使用前面在网络中显示的值配置表。
6. 在**订阅**，先前创建的订阅显示。
7. 有关**资源组**，你可以创建资源组，也可以已经拥有一个帐户，如果选择**使用现有**。
8. 验证默认位置。
9. 选择“固定到仪表板”。
10. 选择“创建” 。

### <a name="create-the-gateway-subnet"></a>创建网关子网
1. 在仪表板中，打开你前面创建的 VNET-01 虚拟网络资源。
2. 在“设置”边栏选项卡中，选择“子网”。
3. 若要将网关子网添加到虚拟网络中，选择**网关子网**。
   
    ![添加网关子网](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. 默认情况下，子网名称设置为**GatewaySubnet**。
   网关的子网是特殊的。 若要正常运行，它们必须使用*GatewaySubnet*名称。
5. 在**地址范围**，验证地址是否**10.0.11.0/24**。
6. 选择**确定**创建网关子网。

### <a name="create-the-virtual-network-gateway"></a>创建虚拟网络网关
1. 在 Azure 门户中，选择**新建**。 
2. 转到**Marketplace**，然后选择**网络**。
3. 从网络资源的列表中选择**虚拟网络网关**。
4. 在**名称**，输入**GW1**。
5. 选择**虚拟网络**项选择虚拟网络。
   选择**VNET-01**从列表中。
6. 选择**公共 IP 地址**菜单项。 当**选择公共 IP 地址**边栏选项卡将打开，选择**新建**。
7. 在**名称**，输入**GW1 PiP**，然后选择**确定**。
8.  默认情况下，为**VPN 类型**，**基于路由的**选择。
    保留**基于路由的**VPN 类型。
9. 验证“订阅”和“位置”是否正确。 你可以固定到仪表板的资源。 选择“创建” 。

### <a name="create-the-local-network-gateway"></a>创建本地网关
在此 Azure Stack 评估部署中实现*本地网关* 稍微不同于实际 Azure 部署中的情况。

在 Azure 部署中，本地网络网关表示的本地 （在租户中） 物理设备，用于连接到 Azure 虚拟网络网关。 在此 Azure 堆栈评估部署中，连接两端是虚拟网络网关 ！

更广义考虑这一方法是，本地网络网关资源始终指示远程网关连接另一端。 由于 Azure 堆栈开发工具包旨在的方法，你需要提供外部网络适配器的 IP 地址在网络地址转换 (NAT) 的其他 Azure 堆栈开发工具包的公共 IP 地址的 VM 上的本地网络网关。 然后情况下，若要确保两个 ends 连接正确 NAT VM 上需要创建 NAT 映射。


### <a name="create-the-local-network-gateway-resource"></a>创建本地网络网关资源
1. 登录到 Azure 堆栈物理机 POC1。
2. 在用户门户中，选择**新建**。
3. 转到**Marketplace**，然后选择**网络**。
4. 从资源的列表中选择**本地网络网关**。
5. 在**名称**，输入**POC2 GW**。
6. 在**IP 地址**，输入 POC2 外部 BGPNAT 地址。 此地址将显示在网络配置表的前面部分中。
7. 在**地址空间**，对于更高版本创建的 POC2 VNET 的地址空间中，输入**10.0.20.0/23**。
8. 验证你**订阅**，**资源组**，和**位置**正确无误，，然后选择**创建**。

### <a name="create-the-connection"></a>创建连接
1. 在用户门户中，选择**新建**。
2. 转到**Marketplace**，然后选择**网络**。
3. 从资源的列表中选择**连接**。
4. 上**基础知识**设置边栏选项卡，为**连接类型**，选择**站点到站点 (IPSec)**。
5. 选择**订阅**，**资源组**，和**位置**，然后选择**确定**。
6. 上**设置**边栏选项卡，选择**虚拟网络网关**，然后选择**GW1**。
7. 选择**本地网络网关**，然后选择**POC2 GW**。
8. 在**连接名称**，输入**POC1 POC2**。
9. 在**共享密钥 (PSK)**，输入**12345**，然后选择**确定**。
10. 上**摘要**边栏选项卡，选择**确定**。

### <a name="create-a-vm"></a>创建 VM
若要验证的数据，通过 VPN 连接，你需要要发送和接收数据，每个 Azure 堆栈开发工具包中的虚拟机。 现在，在 POC1 中创建虚拟机，然后在虚拟网络中，将其置于你的 VM 子网。

1. 在 Azure 门户中，选择**新建**。
2. 转到**Marketplace**，然后选择**计算**。
3. 在虚拟机映像的列表中，选择**Windows Server 2016 数据中心 Eval**映像。
4. 上**基础知识**边栏选项卡，请在**名称**，输入**VM01**。
5. 输入有效的用户名和密码。 此帐户用于登录到 VM，将在创建后。
6. 提供**订阅**，**资源组**，和**位置**，然后选择**确定**。
7. 上**大小**边栏选项卡，此实例中，选择虚拟机大小，然后选择**选择**。
8. 上**设置**边栏选项卡中，接受默认设置。 确保**VNET-01**选择虚拟网络。 验证子网是否设置为**10.0.10.0/24**。 然后选择“确定”。
9. 上**摘要**边栏选项卡，查看设置，，然后选择**确定**。



## <a name="create-the-network-resources-in-poc2"></a>在 POC2 中创建的网络资源

下一步是为 POC2 创建网络资源。 下面的说明演示如何通过使用用户门户中创建资源。

### <a name="sign-in-as-a-tenant"></a>作为租户登录
服务管理员可以登录作为租户以测试计划、 服务和其租户可能使用的订阅。 如果你还没有一个，[创建的租户帐户](azure-stack-add-new-user-aad.md)在登录之前。

### <a name="create-the-virtual-network-and-vm-subnet"></a>创建虚拟网络和 VM 子网

1. 通过使用租户帐户登录。
2. 在用户门户中，选择**新建**。
3. 转到**Marketplace**，然后选择**网络**。
4. 选择**虚拟网络**。
5. 使用前面在网络配置表中显示的信息以确定的值为 POC2**名称**，**地址空间**，**子网名称**，和**子网地址范围**。
6. 在**订阅**，先前创建的订阅显示。
7. 有关**资源组**，创建新的资源组或如果你已经有一个，则选择**使用现有**。
8. 验证默认**位置**。
9. 选择“固定到仪表板”。
10. 选择“创建” 。

### <a name="create-the-gateway-subnet"></a>创建网关子网
1. 打开刚才创建的虚拟网络资源 (**VNET-02**) 从仪表板。
2. 在“设置”边栏选项卡中，选择“子网”。
3. 选择**网关子网**将网关子网添加到虚拟网络。
4. 默认情况下，子网的名称设置为 **GatewaySubnet**。
   网关子网很特殊，必须使用该特定名称才能正常运行。
5. 在**地址范围**字段中，验证地址是否**10.0.21.0/24**。
6. 选择**确定**创建网关子网。

### <a name="create-the-virtual-network-gateway"></a>创建虚拟网络网关
1. 在 Azure 门户中，选择**新建**。  
2. 转到**Marketplace**，然后选择**网络**。
3. 从网络资源的列表中选择**虚拟网络网关**。
4. 在**名称**，输入**GW2**。
5. 若要选择一个虚拟网络，选择**虚拟网络**。 然后选择**VNET-02**从列表中。
6. 选择**公共 IP 地址**。 当**选择公共 IP 地址**边栏选项卡将打开，选择**新建**。
7. 在**名称**，输入**GW2 PiP**，然后选择**确定**。
8. 默认情况下，为**VPN 类型**，**基于路由的**选择。
    保留**基于路由的**VPN 类型。
9. 验证“订阅”和“位置”是否正确。 你可以固定到仪表板的资源。 选择“创建” 。

### <a name="create-the-local-network-gateway-resource"></a>创建本地网络网关资源

1. 在 POC2 用户门户中，选择**新建**。 
4. 转到**Marketplace**，然后选择**网络**。
5. 从资源的列表中选择**本地网络网关**。
6. 在**名称**，输入**POC1 GW**。
7. 在**IP 地址**，输入网络配置表中的前面列出的 POC1 外部 BGPNAT 地址。
8. 在**地址空间**，从 POC1，输入**10.0.10.0/23**地址空间的**VNET-01**。
9. 验证你**订阅**，**资源组**，和**位置**正确无误，，然后选择**创建**。

## <a name="create-the-connection"></a>创建连接
1. 在用户门户中，选择**新建**。 
2. 转到**Marketplace**，然后选择**网络**。
3. 从资源的列表中选择**连接**。
4. 上**基本**设置边栏选项卡，为**连接类型**，选择**站点到站点 (IPSec)**。
5. 选择**订阅**，**资源组**，和**位置**，然后选择**确定**。
6. 上**设置**边栏选项卡，选择**虚拟网络网关**，然后选择**GW2**。
7. 选择**本地网络网关**，然后选择**POC1 GW**。
8. 在**连接名称**，输入**POC2 POC1**。
9. 在**共享密钥 (PSK)**，输入**12345**。 如果你选择不同的值，请记住它*必须*匹配你在 POC1 创建的共享密钥的值。 选择“确定”。
10. 查看**摘要**边栏选项卡，，然后选择**确定**。

## <a name="create-a-virtual-machine"></a>创建虚拟机
现在，在 POC2 中创建虚拟机并将其放在虚拟网络中你的 VM 子网。

1. 在 Azure 门户中，选择**新建**。
2. 转到**Marketplace**，然后选择**计算**。
3. 在虚拟机映像的列表中，选择**Windows Server 2016 数据中心 Eval**映像。
4. 上**基础知识**边栏选项卡，为**名称**，输入**VM02**。
5. 输入有效的用户名和密码。 此帐户用于登录到虚拟机将在创建后。
6. 提供**订阅**，**资源组**，和**位置**，然后选择**确定**。
7. 上**大小**边栏选项卡，选择虚拟机大小调整为此实例，然后选择**选择**。
8. 上**设置**边栏选项卡，你可以接受默认值。 确保**VNET-02**虚拟网络已选中，然后验证子网是否设置为**10.0.20.0/24**。 选择“确定”。
9. 上查看设置**摘要**边栏选项卡，，然后选择**确定**。

## <a name="configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal"></a>在网关遍历每个 Azure 堆栈开发工具包上配置 NAT 虚拟机
因为 Azure 堆栈开发工具包是自包含且独立于部署的物理主机网络*外部*网关连接到的 VIP 网络不会实际外部。 相反，VIP 网络隐藏执行网络地址转换的路由器后面。 

路由器是 Windows Server 虚拟机，调用*AzS bgpnat01*，Azure 堆栈开发工具包基础结构中运行路由和远程访问服务 (RRAS) 角色。 你必须以允许站点到站点 VPN 连接来连接两端 AzS bgpnat01 虚拟机上配置 NAT。 

若要配置 VPN 连接，必须创建到边缘网关池的 VIP 映射 BGPNAT 虚拟机上的外部接口的静态 NAT 映射路由。 静态 NAT 映射路由是必需的 VPN 连接中的每个端口。

> [!NOTE]
> 此配置是必需的仅限 Azure 堆栈开发工具包环境。
> 
> 

### <a name="configure-the-nat"></a>配置 NAT
> [!IMPORTANT]
> 你必须完成此过程为*同时*Azure 堆栈开发工具包环境。

1. 确定**内部 IP 地址**以下 PowerShell 脚本中使用。 打开虚拟网络网关 （GW1 和 GW2），然后在**概述**边栏选项卡，保存的值**公共 IP 地址**以供将来使用。
![内部 IP 地址](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)
2. 登录到 Azure 堆栈物理机 POC1。
3. 复制和编辑以下 PowerShell 脚本。 若要在每个 Azure 堆栈开发工具包配置 NAT，请在提升的 Windows PowerShell ISE 中运行脚本。 在脚本中，将值添加到*外部 BGPNAT 地址*和*内部 IP 地址*占位符：

   ```powershell
   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

4. 上重复此过程 POC2。

## <a name="test-the-connection"></a>测试连接
现在，建立站点到站点连接时，你应验证就可以流量流向通过它。 若要验证，登录到你在这两种 Azure 堆栈开发工具包环境中创建的虚拟机之一。 然后，执行 ping 操作在其他环境中创建的虚拟机。 

若要确保发送的流量通过站点到站点连接，确保 ping 远程子网，不 VIP 上的虚拟机的直接 IP (DIP) 地址。 若要执行此操作，找到连接的另一端上的 DIP 地址。 保存以供将来使用的地址。

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>登录到租户中 POC1 VM
1. 登录到 Azure 堆栈物理计算机并 POC1，，然后使用租户帐户登录到用户门户。
2. 在左侧的导航栏中，选择**计算**。
3. 在虚拟机列表中，查找**VM01** ，以前，创建，然后选择它。
4. 在虚拟机的边栏选项卡，单击**连接**，然后打开 VM01.rdp 文件。
   
     ![连接按钮](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. 使用在创建虚拟机时配置的帐户登录。
6. 打开提升**Windows PowerShell**窗口。
7. 输入**ipconfig /all**。
8. 在输出中，查找**IPv4 地址**，然后将保存以供将来使用的地址。 这是将从 POC2 ping 的地址。 在示例环境中，该地址为 **10.0.10.4**，但用户环境中的该地址可能有所不同。 它不应超过**10.0.10.0/24**你之前创建的子网。
9. 若要创建允许要在响应 ping 的虚拟机的防火墙规则，运行以下 PowerShell 命令：

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>登录到租户中 POC2 VM
1. 登录到 Azure 堆栈物理计算机并 POC2，，然后使用租户帐户登录到用户门户。
2. 在左侧的导航栏中，单击**计算**。
3. 从虚拟机列表中，找到**VM02** ，以前，创建，然后选择它。
4. 在虚拟机边栏选项卡上，单击“连接”。
5. 使用在创建虚拟机时配置的帐户登录。
6. 打开提升**Windows PowerShell**窗口。
7. 输入**ipconfig /all**。
8. 你应看到范围内的 IPv4 地址**10.0.20.0/24**。 在示例环境中，该地址是**10.0.20.4**，但你的地址可能会不同。
9. 若要创建允许要在响应 ping 的虚拟机的防火墙规则，运行以下 PowerShell 命令：

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. 从 POC2 上的虚拟机，进行 ping 操作上 POC1，通过隧道的虚拟机。 若要执行此操作，你 ping 记录从 VM01 的 DIP。
   在示例环境中，这是**10.0.10.4**，但请确保你的实验室中记下的地址执行 ping 操作。 你应看到如下所示的结果：
   
    ![成功 ping](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. 从远程虚拟机的回复表明成功的测试 ！ 你可以关闭虚拟机窗口。 若要测试你的连接，你可以尝试其他类型的类似文件复制的数据传输。

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>通过网关连接查看数据传输统计信息
如果你想要知道多少数据通过站点到站点连接，此信息位于**连接**边栏选项卡。 此测试也是另一种方法验证刚刚发送 ping 实际经历 VPN 连接。

1. 而你要登录到 POC2 中的租户虚拟机，使用你的租户帐户登录到用户门户。
2. 转到**的所有资源**，然后选择**POC2 POC1**连接。 **连接**显示。
4. 上**连接**边栏选项卡、 的统计信息**中的数据**和**输出数据量**显示。 在以下屏幕截图中，较大的数字归因于另外的文件传输。 你应看到那里一些非零值。
   
    ![数据输入和输出](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
