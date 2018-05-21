---
title: 教程：跨可用性区域的负载均衡器 VM - Azure 门户 | Microsoft Docs
description: 本指南演示了如何使用 Azure 门户创建包含区域冗余前端的标准负载均衡器，以便对跨可用性区域的 VM 进行负载均衡
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines across availability zones in a region, so that the customers can still access the web service if a datacenter is unavailable.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/20/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 9ff0b53f6c6f10a2e97bd3158f874fa5cfe33bb6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-load-balance-vms-across-availability-zones-with-a-standard-load-balancer-using-the-azure-portal"></a>教程：在 Azure 门户中使用标准负载均衡器对跨可用性区域的 VM 进行负载均衡

负载均衡通过将传入请求分布到多个虚拟机来提供更高级别的可用性。 本教程分步展示了创建在各个可用性区域之间对 VM 进行负载均衡的公用标准负载均衡器。 这有助于在整个数据中心发生故障或服务中断（这种情况很少见）时保护应用和数据。 使用区域冗余时，一个或多个可用性区域可能会发生故障，而数据路径可以幸存，但前提是该区域中有一个局部区域保持正常。 学习如何：

> [!div class="checklist"]
> * 创建标准负载均衡器
> * 创建网络安全组来定义传入流量规则
> * 创建跨多个区域的区域冗余 VM 并附加到负载均衡器
> * 创建负载均衡器运行状况探测
> * 创建负载均衡器流量规则
> * 创建基本 IIS 站点
> * 查看运行中的负载均衡器

有关对标准负载均衡器使用可用性区域的详细信息，请参阅[标准负载均衡器和可用性区域](load-balancer-standard-availability-zones.md)。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="sign-in-to-azure"></a>登录 Azure

在 [http://portal.azure.com](http://portal.azure.com) 中登录 Azure 门户。

## <a name="create-a-standard-load-balancer"></a>创建标准负载均衡器

标准负载均衡器仅支持标准公用 IP 地址。 在创建负载均衡器过程中创建新的公共 IP 时，该负载均衡器会自动配置为标准 SKU 版本，并自动实现区域冗余。

1. 在屏幕的左上方，单击“创建资源” > “网络” > “负载均衡器”。
2. 在“创建负载均衡器”页中，输入负载均衡器的以下值：
    - *myLoadBalancer* - 负载均衡器的名称。
    - **公共** - 负载均衡器的类型。
     - *myPublicIP* - 创建的新公用 IP 地址。 为此，请依次单击“选择公用 IP 地址”、“新建”。 键入 *myPublicIP* 作为名称，SKU 默认为 Standard，为“可用性区域”选择“区域冗余”。
    - *myResourceGroupLBAZ* - 创建的新资源组的名称。
    - **westeurope** - 位置。
3. 单击“创建”以创建负载均衡器。
   
    ![创建负载均衡器](./media/load-balancer-standard-public-availability-zones-portal/1a-load-balancer.png)


## <a name="create-backend-servers"></a>创建后端服务器

在本部分中，将创建一个虚拟网络，在地区中的不同区域中创建虚拟机，然后在虚拟机上安装 IIS，以便对区域冗余负载均衡器进行测试。 因此，如果某个区域发生故障，则同一区域中 VM 的运行状况探测将会失败，而流量可继续由其他区域中的 VM 提供服务。

### <a name="create-a-virtual-network"></a>创建虚拟网络
创建用于部署后端服务器的虚拟网络。

1. 在屏幕的左上方，单击“创建资源” > “网络” > “虚拟网络”，然后输入虚拟网络的以下值：
    - *myVNet* - 虚拟网络的名称。
    - *myResourceGroupLBAZ* - 现有资源组的名称。
    - *myBackendSubnet* - 子网名称。
2. 单击“创建”以创建虚拟网络。

    ![创建虚拟网络](./media/load-balancer-standard-public-availability-zones-portal/2-load-balancer-virtual-network.png)

## <a name="create-a-network-security-group"></a>创建网络安全组

创建网络安全组，以定义虚拟网络的入站连接。

1. 在屏幕的左上方单击“创建资源”，在搜索框中键入“网络安全组”，然后在网络安全组页上单击“创建”。
2. 在“创建网络安全组”页中输入以下值：
    - *myNetworkSecurityGroup* - 网络安全组的名称。
    - *myResourceGroupLBAZ* - 现有资源组的名称。
   
![创建虚拟网络](./media/load-balancer-standard-public-availability-zones-portal/create-nsg.png)

### <a name="create-network-security-group-rules"></a>创建网络安全组规则

在本部分中，将使用 Azure 门户创建网络安全组规则以允许使用 HTTP 和 RDP 建立入站连接。

1. 在 Azure 门户的左侧菜单中单击“所有资源”，然后在 **myResourceGroupLBAZ** 资源组中搜索 **myNetworkSecurityGroup** 并单击它。
2. 在“设置”下单击“入站安全规则”，然后单击“添加”。
3. 为名为 *myHTTPRule* 的入站安全规则输入以下值，以允许来自端口 80 的入站 HTTP 连接：
    - 服务标记 - **源**。
    - *Internet* - **源服务标记**
    - *80* - **目标端口范围**
    - *TCP* - **协议**
    - 允许 - **操作**
    - *100* - **优先级**
    - *myHTTPRule* - 负载均衡器规则的名称。
    - *Allow HTTP* - 负载均衡器器规则的说明。
4. 单击“确定”。
 
 ![创建虚拟网络](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)
5. 重复步骤 2 到 4，使用以下值创建名为 *myRDPRule* 的另一规则，以允许来自端口 3389 的入站 RDP 连接：
    - 服务标记 - **源**。
    - *Internet* - **源服务标记**
    - *3389* - **目标端口范围**
    - *TCP* - **协议**
    - 允许 - **操作**
    - *200* - **优先级**
    - *myRDPRule* - 名称
    - 允许 RDP - 说明

### <a name="create-virtual-machines"></a>创建虚拟机

在地区中的不同区域（区域 1、区域 2 和区域 3）中创建可以充当负载均衡器的后端服务器的虚拟机。

1. 在屏幕的左上方，单击“创建资源” > “计算” > “Windows Server 2016 Datacenter”，然后输入虚拟机的以下值：
    - *myVM1* - 虚拟机的名称。        
    - *azureuser* - 管理员用户名。    
    - *myResourceGroupLBAZ* - 对于“资源组”，请选择“使用现有”，然后选择“myResourceGroupLBAZ”。
2. 单击“确定”。
3. 选择“DS1_V2”作为虚拟机的大小，然后单击“选择”。
4. 为 VM 设置输入以下值：
    - *区域 1* - 放置 VM 的区域。
    -  *myVNet* - 确保选择它作为虚拟网络。
    - *myBackendSubnet* - 确保选择它作为子网。
    - *myNetworkSecurityGroup* - 网络安全组（防火墙）的名称。
5. 单击“禁用”以禁用启动诊断。
6. 创建“确定”，检查“摘要”页上的设置，然后单击“创建”。
  
 ![创建虚拟机](./media/load-balancer-standard-public-availability-zones-portal/create-vm-standard-ip.png)

7. 执行步骤 1-6，在区域 2 中创建名为 *VM2* 的第二个 VM，在区域 3 中创建第三个 VM，并使用 *myVnet* 作为虚拟网络名称，使用 *myBackendSubnet* 作为子网名称，使用 **myNetworkSecurityGroup* 作为网络安全组名称。

### <a name="install-iis-on-vms"></a>在 VM 上安装 IIS

1. 在左侧菜单中单击“所有资源”，在资源列表中，单击位于 *myResourceGroupLBAZ* 资源组中的“myVM1”。
2. 在“概览”页上单击“连接”，以便通过 RDP 连接到 VM 中。
3. 使用用户名 *azureuser* 登录到 VM。
4. 在服务器桌面上导航到“Windows 管理工具”>“服务器管理器”。
5. 在服务器管理器快速启动页中，单击“添加角色和功能”。

   ![添加到后端地址池 - ](./media/load-balancer-standard-public-availability-zones-portal/servermanager.png)    

1. 在“添加角色和功能向导”中使用以下值：
    - 在“选择安装类型”页中，单击“基于角色或基于功能的安装”。
    - 在“选择目标服务器”页中，单击“myVM1”。
    - 在“选择服务器角色”页中，单击“Web 服务器(IIS)”。
    - 遵照说明完成向导中的剩余步骤。
2. 关闭与虚拟机 *myVM1* 建立的 RDP 会话。
3. 重复步骤 1 到 7，在 VM *myVM2* 和 *myVM3* 上安装 IIS。

## <a name="create-load-balancer-resources"></a>创建负载均衡器资源

在本部分中，请为后端地址池和运行状况探测配置负载均衡器设置，并指定负载均衡器和 NAT 规则。


### <a name="create-a-backend-address-pool"></a>创建后端地址池

若要向 VM 分发流量，后端地址池需包含连接到负载均衡器的虚拟 NIC 的 IP 地址。 创建后端地址池 *myBackendPool* 来包括 *VM1*、*VM2* 和 *VM3*。

1. 单击左侧菜单中的“所有资源”，然后在资源列表中单击“myLoadBalancer”。
2. 在“设置”下单击“后端池”，然后单击“添加”。
3. 在“添加后端池”页上执行以下操作：
    - 对于“名称”，请键入 *myBackEndPool* 作为后端池的名称。
    - 在下拉菜单中，针对“虚拟网络”单击“myVNet”。
    - 在下拉菜单中，针对“虚拟机”单击“myVM1”。
    - 在下拉菜单中，针对“IP 地址”单击 myVM1 的 IP 地址。
4. 单击“添加新后端资源”，将每个虚拟机（*myVM2* 和 *myVM3*）添加到负载均衡器的后端池。
5. 单击 **“添加”**。

    ![添加到后端地址池 - ](./media/load-balancer-standard-public-availability-zones-portal/add-backend-pool.png)

3. 确保负载均衡器后端池设置显示所有三个 VM - **myVM1**、**myVM2** 和 **myVM3**。

### <a name="create-a-health-probe"></a>创建运行状况探测器

若要允许负载均衡器监视应用的状态，可以使用运行状况探测器。 运行状况探测器基于其对运行状况检查的响应，从负载均衡器中动态添加或删除 VM。 创建运行状况探测 *myHealthProbe* 以监视 VM 的运行状况。

1. 单击左侧菜单中的“所有资源”，然后在资源列表中单击“myLoadBalancer”。
2. 在“设置”下单击“运行状况探测”，然后单击“添加”。
3. 使用以下值创建运行状况探测：
    - *myHealthProbe* - 运行状况探测的名称。
    - **HTTP** - 协议类型。
    - *80* - 端口号。
    - *15* - 两次探测尝试之间的**时间间隔**（以秒为单位）。
    - *2* - 将 VM 视为不正常所对应的**不正常阈值**或连续探测失败次数。
4. 单击“确定”。

   ![添加探测器](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则用于定义将流量分配给 VM 的方式。 定义传入流量的前端 IP 配置和后端 IP 池以接收流量，同时定义所需源和目标端口。 创建负载均衡器规则 *myLoadBalancerRuleWeb*，以便侦听前端 *FrontendLoadBalancer* 中的端口 80，并将经过负载均衡的网络流量发送到也使用端口 80 的后端地址池 *myBackEndPool*。 

1. 单击左侧菜单中的“所有资源”，然后在资源列表中单击“myLoadBalancer”。
2. 在“设置”下单击“负载均衡规则”，然后单击“添加”。
3. 请使用以下值配置负载均衡规则：
    - *myHTTPRule* - 负载均衡规则的名称。
    - **TCP** - 协议类型。
    - *80* - 端口号。
    - *80* - 后端端口。
    - *myBackendPool* - 后端池的名称。
    - *myHealthProbe* - 运行状况探测的名称。
4. 单击“确定”。
    
    ![添加负载均衡规则](./media/load-balancer-standard-public-availability-zones-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>测试负载均衡器
1. 在“概览”屏幕上找到负载均衡器的公用 IP 地址。 单击“所有资源”，然后单击“myPublicIP”。

2. 复制该公用 IP 地址，并将其粘贴到浏览器的地址栏。 IIS Web 服务器的默认页会显示在浏览器上。

      ![IIS Web 服务器](./media/load-balancer-standard-public-availability-zones-portal/9-load-balancer-test.png)

若要查看负载均衡器如何在分布在各个区域中的 VM 之间分配流量，可以强制刷新 Web 浏览器。

## <a name="clean-up-resources"></a>清理资源

若不再需要资源组、负载均衡器以及所有相关资源，请将其删除。 为此，请选择包含负载均衡器的资源组，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

详细了解[标准负载均衡器](load-balancer-standard-overview.md)。
