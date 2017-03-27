---
title: "在不同 Azure Stack PoC 环境中的两个虚拟网络之间创建站点到站点 VPN 连接 | Microsoft 文档"
description: "指导云管理员在 TP2 中的两个单节点 POC 环境之间创建站点到站点 VPN 连接的分步过程。"
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
ms.date: 02/08/2017
ms.author: scottnap
translationtype: Human Translation
ms.sourcegitcommit: 5104c7996de9dc0597e65be31c28a9aaa1243a90
ms.openlocfilehash: d324290caf5b5a085a2daf67e541c295dffda732


---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-poc-environments"></a>在不同 Azure Stack PoC 环境中的两个虚拟网络之间创建站点到站点 VPN 连接
## <a name="overview"></a>概述
本文介绍如何在两个单独的 Azure Stack 概念证明 (POC) 环境中的两个虚拟网络之间创建站点到站点 VPN 连接。 在配置连接时，用户会了解到 Azure Stack 中 VPN 网关的工作方式。

> [!NOTE]
> 具体说来，本文档适用于 Azure Stack TP2 POC。
> 
> 

### <a name="connection-diagram"></a>连接关系图
下图为完成后的配置示意图：

![](media/azure-stack-create-vpn-connection-one-node-tp2/image1.png)

### <a name="before-you-begin"></a>开始之前
若要完成此配置，请确保在开始之前准备好以下项目：

* 两台符合 [Azure Stack 部署先决条件](azure-stack-deploy.md)定义的 Azure Stack POC 硬件要求以及该文档定义的其他先决条件的服务器。
* Azure Stack Technical Preview 2 部署包。

## <a name="deploy-the-poc-environments"></a>部署 POC 环境
需部署两个 Azure Stack POC 环境才能完成此配置。

* 部署每个 POC 时，均可按[部署 Azure Stack POC](azure-stack-run-powershell-script.md) 一文中详述的部署说明操作。
  本文档中的单个 POC 环境通常是指 *POC1* 和 *POC2*。

## <a name="configure-quotas-for-compute-network-and-storage"></a>为计算、网络和存储配置配额
首先，需要为计算、网络和存储配置*配额*。 可以将这些服务与*计划* 关联，然后与租户可以订阅的*产品/服务* 关联。

> [!NOTE]
> 需针对每个 Azure Stack POC 环境执行这些步骤。
> 
> 

从 TP1 开始，为服务创建配额的操作已变。 如需在 TP2 中创建配额的步骤，可访问 <http://aka.ms/mas-create-quotas>。 就本练习来说，可以接受所有配额设置的默认值。

## <a name="create-a-plan-and-offer"></a>创建计划和产品/服务
[计划](azure-stack-key-features.md)是对一个或多个服务的分组。 作为提供商，你可以创建提供给租户的计划。 反过来，租户可以订阅你的产品/服务，以便使用其所包括的计划和服务。

> [!NOTE]
> 需针对每个 Azure Stack POC 环境执行这些步骤。
> 
> 

1. 首先创建一个计划。 为此，可按[创建计划](azure-stack-create-plan.md)这篇在线文章中的步骤进行操作。
2. 按照[在 Azure Stack 中创建产品/服务](azure-stack-create-offer.md)中描述的步骤创建产品/服务。
3. 以租户管理员身份登录到门户并[订阅所创建的产品/服务](azure-stack-subscribe-plan-provision-vm.md)。

## <a name="create-the-network-resources-in-poc-1"></a>在 POC 1 中创建网络资源
现在将实际创建进行配置设置所需的资源。 以下步骤说明了将要执行的操作。 这些说明介绍如何使用门户创建资源，但可以使用 PowerShell 完成相同的操作。

![](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="log-in-as-a-tenant"></a>以租户身份登录
服务管理员可以租户身份登录，以便对其租户可能使用的计划、产品/服务和订阅进行测试。 请在登录之前[创建租户帐户](azure-stack-add-new-user-aad.md)（如何还没有）。

### <a name="create-the-virtual-network--vm-subnet"></a>创建虚拟网络和 VM 子网
1. 使用租户帐户登录。
2. 在 Azure 门户中，单击“新建”。
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)
3. 从“应用商店”菜单中选择“网络”。
4. 单击菜单上的“虚拟网络”项。
5. 在资源说明边栏选项卡底部附近单击“创建”。 根据此表将以下值输入相应字段中。
   
   | **字段** | **值** |
   | --- | --- |
   | Name |vnet-01 |
   | 地址空间 |10.0.10.0/23 |
   | 子网名称 |subnet-01 |
   | 子网地址范围 |10.0.10.0/24 |
6. 此时会看到此前创建的订阅填充到“订阅”字段中。
7. 对于“资源组”，可以创建新的资源组，也可以选择“使用现有资源组”（如果已经有了一个）。
8. 验证默认位置。
9. 单击“创建” 。

### <a name="create-the-gateway-subnet"></a>创建网关子网
1. 从仪表板打开刚创建的虚拟网络资源 (Vnet-01)。
2. 在“设置”边栏选项卡中，选择“子网”。
3. 单击“网关子网”，将网关子网添加到虚拟网络。
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)
4. 默认情况下，子网的名称设置为 **GatewaySubnet**。
   网关子网很特殊，必须使用该特定名称才能正常运行。
5. 在“地址范围”字段中，键入 **10.0.11.0/24**。
6. 单击“创建”创建网关子网。

### <a name="create-the-virtual-network-gateway"></a>创建虚拟网关
1. 在 Azure 门户中，单击“新建”。
   
2. 从“应用商店”菜单中选择“网络”。
3. 从网络资源列表中选择“虚拟网关”。
4. 查看说明，然后单击“创建”。
5. 在“名称”字段中，键入 **GW1**。
6. 单击“虚拟网络”项，选择一个虚拟网络。
   从列表中选择 **Vnet-01**。
7. 单击“公共 IP 地址”菜单项。 当“选择公共 IP 地址”边栏选项卡打开时，单击“新建”。
8. 在“名称”字段中键入 **GW1-PiP**，然后单击“确定”。
9. 默认情况下，应已选择“VPN”作为“网关类型”。 保留该设置。
10. 默认情况下，应已选择“基于路由”作为“VPN 类型”。
    保留该设置。
11. 验证“订阅”和“位置”是否正确。 可以根据需要将资源固定到仪表板。 单击“创建” 。

### <a name="create-the-local-network-gateway"></a>创建本地网关
在此 Azure Stack 评估部署中实现*本地网关* 稍微不同于实际 Azure 部署中的情况。

就像在 Azure 中一样，你已经有了本地网关的概念。 而在 Azure 部署中，本地网关代表一个本地（租户处）物理设备，用于连接到 Azure 中的虚拟网关。 但在此 Azure Stack 评估部署中，连接的两端都是虚拟网关！

从更通用的角度来看，存在本地网关资源通常意味着在连接的另一端存在远程网关。 考虑到 POC 的设计方式，在提供另一 POC 的 NAT VM 上的外部网络适配器 IP 地址时，需将其作为本地网关 的公共 IP 地址。 然后需在 NAT VM 上创建 NAT 映射，确保两端连接正确。


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>获取 NAT VM 的外部适配器的 IP 地址
1. 登录到 POC2 的 Azure Stack 物理机。
2. 按 [Windows 键] + R 打开“运行”菜单，键入 **mstsc** 并按 Enter。
3. 在“计算机”字段中键入名称 **MAS-BGPNAT01**，然后单击“连接”。 ****
4. 单击“开始”菜单，右键单击“Windows PowerShell”并选择“以管理员身份运行”。
5. 键入 **ipconfig /all**。
6. 找到连接到本地网络的以太网适配器，记下绑定到该适配器的 IPv4 地址。 在我们的示例环境中，此地址为 **10.16.167.195**，但你的地址会有所不同。
7. 记下此地址。 此地址将用作在 POC1 中创建的本地网关资源的公共 IP 地址。

### <a name="create-the-local-network-gateway-resource"></a>创建本地网关资源
1. 登录到 POC1 的 Azure Stack 物理机。
2. 在“计算机”字段中键入名称 **MAS-CON01**，然后单击“连接”。
3. 在 Azure 门户中，单击“新建”。
   
4. 从“应用商店”菜单中选择“网络”。
5. 从资源列表中选择“本地网关”。
6. 在“名称”字段中，键入 **POC2-GW**。
7. 你此时还不知道另一网关的 IP 地址，但这没关系，因为可以在以后回来更改。 现在请在“IP 地址”字段中键入 **10.16.167.195**。
8. 在“地址空间”字段中键入要在 POC2 中创建的 Vnet 的地址空间。 此地址空间将是 **10.0.20.0/23**，因此请键入该值。
9. 验证“订阅”、“资源组”和“位置”是否均正确，然后单击“创建”。

### <a name="create-the-connection"></a>创建连接
1. 在 Azure 门户中，单击“新建”。
   
2. 从“应用商店”菜单中选择“网络”。
3. 从资源列表中选择“连接”。
4. 在“基本设置”边栏选项卡中，选择“站点到站点(IPSec)”作为“连接类型”。
5. 选择“订阅”、“资源组”和“位置”，然后单击“确定”。
6. 在“设置”边栏选项卡中，选择此前创建的“虚拟网关(GW1)”。
7. 选择此前创建的“本地网关(POC2-GW)”。
8. 在“连接名称”字段中键入 **POC1-POC2**。
9. 在“共享密钥(PSK)”字段中键入 **12345**，然后单击“确定”。

### <a name="create-a-vm"></a>创建 VM
若要通过 VPN 连接验证数据传输，需要 VM 在每个 POC 中发送和接收数据。 现在请在 POC1 中创建一个 VM，然后将其置于虚拟网络的 VM 子网上。

1. 在 Azure 门户中，单击“新建”。
   
2. 从“应用商店”菜单中选择“虚拟机”。
3. 在虚拟机映像列表中，选择“Windows Server 2012 R2 Datacenter”映像。
4. 在“基本信息”边栏选项卡的“名称”字段中，键入 **VM01**。
5. 键入有效的用户名和密码。 在创建 VM 后，将使用此帐户登录到该 VM。
6. 提供“订阅”、“资源组”和“位置”，然后单击“确定”。
7. 在“大小”边栏选项卡上，选择该实例的 VM 大小，然后单击“选择”。
8. 在“设置”边栏选项卡上，可以接受默认设置，但需确保所选虚拟网络为 **VNET-01** 且子网设置为 **10.0.10.0/24**。 单击 **“确定”**。
9. 查看“摘要”边栏选项卡上的设置，然后单击“确定”。

## <a name="create-the-network-resources-in-poc-2"></a>在 POC 2 中创建网络资源
### <a name="log-in-as-a-tenant"></a>以租户身份登录
服务管理员可以租户身份登录，以便对其租户可能使用的计划、产品/服务和订阅进行测试。 请在登录之前[创建租户帐户](azure-stack-add-new-user-aad.md)（如何还没有）。

### <a name="create-the-virtual-network-and-vm-subnet"></a>创建虚拟网络和 VM 子网
1. 使用租户帐户登录。
2. 在 Azure 门户中，单击“新建”。
   
3. 从“应用商店”菜单中选择“网络”。
4. 单击菜单上的“虚拟网络”。
5. 在资源说明边栏选项卡底部附近单击“创建”。 针对下表中列出的相应字段，键入以下值。
   
   | **字段** | **值** |
   | --- | --- |
   | 名称 |vnet-02 |
   | 地址空间 |10.0.20.0/23 |
   | 子网名称 |subnet-02 |
   | 子网地址范围 |10.0.20.0/24 |
6. 此时会看到此前创建的订阅填充到“订阅”字段中。
7. 对于“资源组”，可以创建新的资源组，也可以选择“使用现有资源组”（如果已经有了一个）。
8. 验证默认**位置**。 可以根据需要将虚拟网络固定到仪表板以方便访问。
9. 单击“创建” 。

### <a name="create-the-gateway-subnet"></a>创建网关子网
1. 从仪表板打开刚创建的虚拟网络资源 (**Vnet-02**)。
2. 在“设置”边栏选项卡中，选择“子网”。
3. 单击“网关子网”，将网关子网添加到虚拟网络。
   
4. 默认情况下，子网的名称设置为 **GatewaySubnet**。
   网关子网很特殊，必须使用该特定名称才能正常运行。
5. 在“地址范围”字段中，键入 **10.0.20.0/24**。
6. 单击“创建”创建网关子网。

### <a name="create-the-virtual-network-gateway"></a>创建虚拟网关
1. 在 Azure 门户中，单击“新建”。
   
2. 从“应用商店”菜单中选择“网络”。
3. 从网络资源列表中选择“虚拟网关”。
4. 查看说明，然后单击“创建”。
5. 在“名称”字段中，键入 **GW2**。
6. 单击“虚拟网络”选择一个虚拟网络。
   从列表中选择 **Vnet-02**。
7. 单击“公共 IP 地址”。 当“选择公共 IP 地址”边栏选项卡打开时，单击“新建”。
8. 在“名称”字段中键入 **GW2-PiP**，然后单击“确定”。
9. 默认情况下，应已选择“VPN”作为“网关类型”。 保留该设置。
10. 默认情况下，应已选择“基于路由”作为“VPN 类型”。
    保留该设置。
11. 验证“订阅”和“位置”是否正确。 可以根据需要将资源固定到仪表板。 单击“创建” 。

### <a name="create-the-local-network-gateway"></a>创建本地网关
#### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>获取 NAT VM 的外部适配器的 IP 地址
1. 登录到 POC1 的 Azure Stack 物理机。
2. 按住 [Windows 键] + R 打开“运行”菜单，键入 **mstsc** 并按 Enter。
3. 在“计算机”字段中键入名称 **MAS-BGPNAT01**，然后单击“连接”。 ****
4. 单击“开始”菜单，右键单击“Windows PowerShell”并选择“以管理员身份运行”。
5. 键入 **ipconfig /all**。
6. 找到连接到本地网络的以太网适配器，记下绑定到该适配器的 IPv4 地址。 在示例环境中，此地址为 **10.16.169.131**，但你的地址会不同。
7. 记下此地址。 此地址随后将用作在 POC1 中创建的本地网关资源的公共 IP 地址。

#### <a name="create-the-local-network-gateway-resource"></a>创建本地网关资源
1. 登录到 POC2 的 Azure Stack 物理机。
2. 在“计算机”字段中键入名称 **MAS-CON01**，然后单击“连接”。
3. 在 Azure 门户中，单击“新建”。
   
4. 从“应用商店”菜单中选择“网络”。
5. 从资源列表中选择“本地网关”。
6. 在“名称”字段中，键入 **POC1-GW**。
7. 现在需要针对 POC1 中的虚拟网关记录的公共 IP 地址。 请在“IP 地址”字段中键入 **10.16.169.131**。
8. 在“地址空间”字段中，键入 POC1 中 **Vnet-01** 的地址空间 - **10.0.0.0/16**。
9. 验证“订阅”、“资源组”和“位置”是否均正确，然后单击“创建”。

## <a name="create-the-connection"></a>创建连接
1. 在 Azure 门户中，单击“新建”。
   
2. 从“应用商店”菜单中选择“网络”。
3. 从资源列表中选择“连接”。
4. 在“基本设置”边栏选项卡中，选择“站点到站点(IPSec)”作为“连接类型”。
5. 选择“订阅”、“资源组”和“位置”，然后单击“确定”。
6. 在“设置”边栏选项卡中，选择此前创建的“虚拟网关(GW1)”。
7. 选择此前创建的“本地网关(POC1-GW)”。
8. 在“连接名称”字段中键入 **POC2-POC1**。
9. 在“共享密钥(PSK)”字段中键入 **12345**。 如果选择其他值，请记住，该值必须与在 POC1 上创建的共享密钥的值匹配。 单击 **“确定”**。

## <a name="create-a-vm"></a>创建 VM
现在请在 POC1 中创建一个 VM，然后将其置于虚拟网络的 VM 子网上。

1. 在 Azure 门户中，单击“新建”。
   
2. 从“应用商店”菜单中选择“虚拟机”。
3. 在虚拟机映像列表中，选择“Windows Server 2012 R2 Datacenter”映像。
4. 在“基本信息”边栏选项卡的“名称”字段中，键入 **VM02**。
5. 键入有效的用户名和密码。 在创建 VM 后，将使用此帐户登录到该 VM。
6. 提供“订阅”、“资源组”和“位置”，然后单击“确定”。
7. 在“大小”边栏选项卡上，选择该实例的 VM 大小，然后单击“选择”。
8. 在“设置”边栏选项卡上，可以接受默认设置，但需确保所选虚拟网络为 **VNET-02** 且子网设置为 **20.0.0.0/24**。 单击 **“确定”**。
9. 查看“摘要”边栏选项卡上的设置，然后单击“确定”。

## <a name="configure-the-nat-vm-in-each-poc-for-gateway-traversal"></a>在每个 POC 中针对网关遍历配置 NAT VM
由于 POC 设计为自包含型，独立于已部署物理主机所在的网络，因此网关连接到的“外部”VIP 网络实际上并非位于外部，而是隐藏在负责网络地址转换 (NAT) 的路由器之后。 该路由器实际上是 Windows Server VM (**MAS-BGPNAT01**)，在 POC 基础结构中运行路由和远程访问服务 (RRAS) 角色。 必须对 MAS-BGPNAT01 VM 上的 NAT 进行配置，允许站点到站点 VPN 连接在两端进行连接。 为此，必须针对 VPN 连接所需的端口创建静态 NAT 映射，将 BGPNAT VM 上的外部接口映射到边缘网关池的 VIP。

> [!NOTE]
> 仅 POC 环境必须使用此配置。
> 
> 

### <a name="configure-nat"></a>配置 NAT
两个 POC 环境都需要执行这些步骤。

1. 登录到 POC1 的 Azure Stack 物理机。
2. 按住 [Windows 键] + R 打开“运行”菜单，然后键入 **mstsc** 并按 **Enter**。
3. 在“计算机”字段中键入名称 **MAS-BGPNAT01**，然后单击“连接”。
4. 单击“开始”菜单，右键单击“Windows PowerShell”并选择“以管理员身份运行”。
5. 键入 **ipconfig /all**。
6. 找到连接到本地网络的以太网适配器，记下绑定到该适配器的 IPv4 地址。 在示例环境中，此地址为 **10.16.169.131**（在下图中以红色圆圈标记），但你的地址会不同。
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image16.png)
7. 键入以下 PowerShell 命令，为进行 IKE 身份验证所需的端口指定外部 NAT 地址。 请记住，需根据环境更改 IP 地址。
   
       Add-NetNatExternalAddress -NatName BGPNAT -IPAddress 10.16.169.131 -PortStart 499 -PortEnd 501
8. 接下来，请创建静态 NAT 映射，将外部地址映射到网关公共 IP 地址，针对 IPSEC 隧道的阶段 1 映射 ISAKMP 端口 500。
   
        Add-NetNatStaticMapping -NatName BGPNAT -Protocol UDP -ExternalIPAddress 10.16.169.131 -InternalIPAddress 192.168.102.1 -ExternalPort 500 -InternalPort 500
> [!NOTE] 
> 此处的 `-InternalAddress` 参数是此前创建的虚拟网关的公共 IP 地址。  若要找到该 IP 地址，请查看“虚拟网关”边栏选项卡的属性，找出公共 IP 地址的值。       

9. 最后，必须配置 NAT 遍历，使用端口 4500 基于 NAT 设备成功建立完整的 IPEC 隧道。
   
        Add-NetNatStaticMapping -NatName BGPNAT -Protocol UDP -ExternalIPAddress 10.16.169.131 -InternalIPAddress 192.168.102.1 -ExternalPort 4500 -InternalPort 4500
> [!NOTE] 
> 此处的 `-InternalAddress` 参数是此前创建的虚拟网关的公共 IP 地址。  若要找到该 IP 地址，请查看“虚拟网关”边栏选项卡的属性，找出公共 IP 地址的值。       

10. 在 POC2 中重复步骤 1-9。

## <a name="test-the-connection"></a>测试连接
建立站点到站点连接以后，应验证其流量是否正常。 此任务很简单，只需登录到在任一 POC 环境中创建的一个 VM，然后 ping 在另一环境中创建的 VM 即可。 若要确保流量通过站点到站点连接，必须 ping 远程子网上 VM 的直接 IP (DIP) 地址，而不是 VIP 地址。 为此，需找到并记下连接另一端的地址。

### <a name="log-in-to-the-tenant-vm-in-poc1"></a>登录到 POC1 中的租户 VM
1. 登录到 POC1 的 Azure Stack 物理机，然后使用租户帐户登录到门户。
2. 单击左侧导航栏中的“虚拟机”。
3. 在 VM 列表中找到并单击此前创建的“VM01”。
4. 在虚拟机边栏选项卡上，单击“连接”。
   
     ![](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. 在 VM 中打开命令提示符，然后键入 **ipconfig /all**。
6. 找到并记下输出中的“IPv4 地址”。 该地址是要从 POC2 ping 的地址。 在示例环境中，该地址为 **10.0.10.4**，但用户环境中的该地址可能有所不同。 不过，该地址应在此前创建的 **10.0.10.0/24** 子网范围内。

### <a name="log-in-to-the-tenant-vm-in-poc2"></a>登录到 POC2 中的租户 VM
1. 登录到 POC2 的 Azure Stack 物理机，然后使用租户帐户登录到门户。
2. 单击左侧导航栏中的“虚拟机”。
3. 在 VM 列表中找到并单击此前创建的“VM02”。
4. 在虚拟机边栏选项卡上，单击“连接”。
   
5. 在 VM 中打开命令提示符，然后键入 **ipconfig /all**。
6. 用户会看到 IPv4 地址位于 10.0.20.0/24 范围内。 在示例环境中，该地址为 10.0.20.4，但用户的该地址可能有所不同。
7. 现在需从 POC2 中的 VM 通过隧道 ping POC1 中的 VM。 为此，请 ping 从 VM01 中记录的 DIP。
   在示例环境中，该地址为 10.0.10.4，但用户在执行操作时，请确保 ping 其实验室中记下的地址。 用户会看到如下所示的结果：
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
8. 获得远程 VM 的答复表示测试成功！ 此时可关闭“VM 连接”窗口， 也可尝试执行一些其他的数据传输操作（例如文件复制），对连接进行测试。

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>通过网关连接查看数据传输统计信息
若要了解通过站点到站点连接传送的数据量，可在“连接”边栏选项卡中查看该信息。 也可利用此测试来验证刚发送的 ping 是否确实通过了 VPN 连接。

1. 在仍登录 POC2 中的租户 VM 的情况下，使用租户帐户登录到“Microsoft Azure Stack POC 门户”。
2. 单击“浏览”菜单项，然后选择“连接”。
3. 单击列表中的“POC2-POC1”连接。
4. 在“连接”边栏选项卡上，可以看到“传入数据”和“传出数据”的统计信息。 在以下屏幕截图中，可以看到一些数字较大，超出了只进行 ping 操作会得到的结果。 这是因为还进行了一些额外的文件传输操作。 应该会看到其中有一些非零值。
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)




<!--HONumber=Feb17_HO2-->


