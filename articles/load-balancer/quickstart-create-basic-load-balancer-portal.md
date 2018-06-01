---
title: 快速入门：使用 Azure 门户创建公共基本负载均衡器 | Microsoft Docs
description: 本快速入门介绍如何使用 Azure 门户创建公共基本负载均衡器。
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 49fa4cf9b24c432b0956f930a1429e1cdf827f1b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
ms.locfileid: "34304872"
---
# <a name="quickstart-create-a-public-basic-load-balancer-by-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建公共基本负载均衡器

负载均衡将传入请求分布到多个虚拟机 (VM)，从而提供更高级别的可用性和可伸缩性。 可以使用 Azure 门户创建负载均衡器，以便对虚拟机进行负载均衡。 本快速入门介绍如何创建网络资源、后端服务器，以及“基本”定价层的负载均衡器。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

执行本快速入门中的所有任务都需要登录到 [Azure 门户](http://portal.azure.com)。

## <a name="create-a-basic-load-balancer"></a>创建基本负载均衡器

在本部分，我们将使用门户创建公共基本负载均衡器。 使用门户创建公共 IP 和负载均衡器资源时，会将公共 IP 地址配置为负载均衡器的前端。 前端的名称为 **LoadBalancerFrontend**。

1. 在门户的左上方，选择“创建资源” > “网络” > “负载均衡器”。
2. 在“创建负载均衡器”窗格中输入以下值：
   - **myLoadBalancer**：负载均衡器的名称
   - **公共**：负载均衡器的类型 
   - **myPublicIP**：必须创建的公共 IP，其 SKU 设置为“基本”，“分配”设置为“动态”。
   - **myResourceGroupLB**：新资源组的名称
3. 选择**创建**。
   
![创建负载均衡器](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)


## <a name="create-back-end-servers"></a>创建后端服务器

在本部分，我们创建一个虚拟网络，并为基本负载均衡器的后端池创建两个虚拟机。 然后，在虚拟机上安装 Internet Information Services (IIS) 以帮助测试负载均衡器。

### <a name="create-a-virtual-network"></a>创建虚拟网络
1. 在门户的左上方，选择“新建” > “网络” > “虚拟网络”。
2. 在“创建虚拟网络”窗格中输入以下值，然后选择“创建”：
   - **myVnet**：虚拟网络的名称
   - **myResourceGroupLB**：现有资源组的名称
   - **myBackendSubnet**：子网名称

   ![创建虚拟网络](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>创建虚拟机

1. 在门户的左上方，选择“新建” > “计算” > “Windows Server 2016 Datacenter”。 
2. 输入虚拟机的以下值，然后选择“确定”：
   - **myVM1**：虚拟机的名称。        
   - **azureuser**：管理员用户名。    
   - **myResourceGroupLB**：资源组。 （在“资源组”下，依次选择“使用现有项”、“myResourceGroupLB”。）   
3. 选择“DS1_V2”作为虚拟机的大小，然后单击“选择”。
4. 为 VM 设置输入以下值：
   - **myAvailabilitySet**：新建的可用性集的名称。
   - **myVNet**：虚拟网络的名称。 （确保已选中。）
   - **myBackendSubnet**：子网名称。 （确保已选中。）
   - **myVM1-ip**：公共 IP 地址。
   - **myNetworkSecurityGroup**：必须创建的新网络安全组（NSG，一种防火墙）的名称。
5. 选择“禁用”以禁用启动诊断。
6. 选择“确定”，检查摘要页上的设置，然后选择“创建”。
7. 执行步骤 1 到 6，使用以下设置创建名为 **VM2** 的另一个 VM：
   - **myAvailabilityset**：可用性集。
   - **myVnet**：虚拟网络。
   - **myBackendSubnet**：子网。
   - **myNetworkSecurityGroup**：网络安全组。 

### <a name="create-nsg-rules"></a>创建 NSG 规则

在本部分，我们将创建 NSG 规则以允许使用 HTTP 和 RDP 的入站连接。

1. 在左侧菜单中选择“所有资源”。 在资源列表中，选择“myResourceGroupLB”资源组中的“myNetworkSecurityGroup”。
2. 在“设置”下，依次选择“入站安全规则”、“添加”。
3. 为名为 **myHTTPRule** 的入站安全规则输入以下值，以允许使用端口 80 的入站 HTTP 连接： 然后选择“确定”。
   - 在“源”中输入“服务标记”
   - 在“源服务标记”中输入 **Internet**
   - 在“目标端口范围”中输入 **80**
   - 在“协议”中输入 **TCP**
   - 在“操作”中输入“允许”
   - **100** - **优先级**
   - 在“名称”中输入 **myHTTPRule**
   - 在“说明”中输入“允许 HTTP”
 
   ![创建 NSG 规则](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
4. 重复步骤 2 到 3 创建名为 **myRDPRule** 的另一规则，以允许通过端口 3389 的入站 RDP 连接。 使用以下值：
   - 在“源”中输入“服务标记”
   - 在“源服务标记”中输入 **Internet**
   - 在“目标端口范围”中输入 **3389**
   - 在“协议”中输入 **TCP**
   - 在“操作”中输入“允许”
   - **200** - **优先级**
   - 在“名称”中输入 **myRDPRule**
   - 在“说明”中输入“允许 RDP”

   

### <a name="install-iis"></a>安装 IIS

1. 在左侧菜单中选择“所有资源”。 在资源列表中，选择“myResourceGroupLB”资源组中的“myVM1”。
2. 在“概述”页上选择“连接”，以通过 RDP 连接到 VM。
3. 使用用户名 **azureuser** 和密码 **Azure123456!** 登录到 VM。
4. 在服务器桌面上浏览到“Windows 管理工具” > “服务器管理器”。
5. 在服务器管理器中选择“管理”，然后选择“添加角色和功能”。
   ![添加服务器管理器角色](./media/load-balancer-get-started-internet-portal/servermanager.png)
6. 在“添加角色和功能向导”中使用以下值：
   - 在“选择安装类型”页上，选择“基于角色或基于功能的安装”。
   - 在“选择目标服务器”页上，选择“myVM1”。
   - 在“选择服务器角色”页上，选择“Web 服务器(IIS)”。
   - 遵照说明完成向导中的剩余步骤。 
7. 对于虚拟机 **myVM2**，请重复步骤 1 到 6。

## <a name="create-resources-for-the-basic-load-balancer"></a>为基本负载均衡器创建资源

在本部分，我们将为后端地址池和运行状况探测配置负载均衡器设置。 另外，指定负载均衡器和 NAT 规则。


### <a name="create-a-back-end-address-pool"></a>创建后端地址池

若要向 VM 分配流量，后端地址池需包含连接到负载均衡器的虚拟 NIC 的 IP 地址。 创建包括 **VM1** 和 **VM2** 的后端地址池 **myBackendPool**。

1. 在左侧菜单中选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。
2. 在“设置”下，依次选择“后端池”、“添加”。
3. 在“添加后端池”页上执行以下操作，然后选择“确定”：
   - 对于“名称”，请输入 **myBackEndPool**。
   - 对于“关联到”，请在下拉菜单中选择“可用性集”。
   - 对于“可用性集”，请选择“myAvailabilitySet”。
   - 选择“添加目标网络 IP 配置”，将所创建的每个虚拟机（**myVM1** 和 **myVM2**）添加到后端池。

   ![添加到后端地址池](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

3. 确保负载均衡器的后端池设置同时显示了 VM **VM1** 和 **VM2**。

### <a name="create-a-health-probe"></a>创建运行状况探测器

若要让基本负载均衡器监视应用的状态，请使用运行状况探测。 运行状况探测器基于其对运行状况检查的响应，从负载均衡器中动态添加或删除 VM。 创建名为 **myHealthProbe** 的运行状况探测来监视 VM 的运行状况。

1. 在左侧菜单中选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。
2. 在“设置”下，依次选择“运行状况探测”、“添加”。
3. 请使用以下值，然后选择“确定”：
   - **myHealthProbe**：运行状况探测的名称
   - **HTTP**：协议类型
   - **80**：端口号
   - **15**：两次探测尝试之间的**时间间隔**，以秒为单位
   - **2**：**不正常阈值**，即，将 VM 视为不正常之前必须发生的连续探测失败次数

   ![添加探测器](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则

使用负载均衡器规则来定义将流量分配给 VM 的方式。 定义传入流量的前端 IP 配置和后端 IP 池以接收流量，同时定义所需源和目标端口。 

创建名为 **myLoadBalancerRuleWeb** 的负载均衡器规则，用于侦听前端 **LoadBalancerFrontEnd** 中的端口 80。 此外，该规则也使用端口 80 将经过负载均衡的网络流量发送到后端地址池 **myBackEndPool**。 

1. 在左侧菜单中选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。
2. 在“设置”下，依次选择“负载均衡规则”、“添加”。
3. 请使用以下值，然后选择“确定”：
   - **myHTTPRule**：负载均衡器规则的名称
   - **TCP**：协议类型
   - **80**：端口号
   - **80**：后端端口
   - **myBackendPool**：后端池的名称
   - **myHealthProbe**：运行状况探测的名称
    
   ![添加负载均衡器规则](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>测试负载均衡器
1. 在“概述”屏幕上找到负载均衡器的公共 IP 地址。 依次选择“所有资源”、“myPublicIP”。

2. 复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。 IIS Web 服务器的默认页会显示在浏览器中。

   ![IIS Web 服务器](./media/load-balancer-get-started-internet-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、负载均衡器以及所有相关资源，可将其删除。 选择包含负载均衡器的资源组，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们创建了资源组、网络资源和后端服务器。 然后使用这些资源创建基本负载均衡器。 若要了解有关 Azure 负载均衡器的详细信息，请继续学习 Azure 负载均衡器教程。

> [!div class="nextstepaction"]
> [Azure 负载均衡器教程](tutorial-load-balancer-basic-internal-portal.md)
