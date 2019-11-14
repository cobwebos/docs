---
title: 快速入门：创建公共基本负载均衡器-Azure 门户
titleSuffix: Azure Load Balancer
description: 本快速入门介绍如何使用 Azure 门户创建公共基本负载均衡器。
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 3cbb4271909cf739dc3ce13712e388f2fc8e20a5
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048702"
---
# <a name="quickstart-create-a-basic-load-balancer-by-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建基本负载均衡器

负载均衡将传入请求分布到虚拟机 (VM)，以便提供更高级别的可用性和可伸缩性。 可以使用 Azure 门户创建负载均衡器，并在 VM 之间对流量进行均衡。 本快速入门介绍如何创建并配置负载均衡器、后端服务器以及“基本”定价层的网络资源。

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

若要执行本快速入门中的任务，请登录 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-basic-load-balancer"></a>创建基本负载均衡器

首先，使用门户创建公共基本负载均衡器。 创建的名称和公共 IP 地址自动配置为负载均衡器的前端。

1. 在屏幕的左上方，单击“创建资源” > “网络” > “负载均衡器”。
2. 在“创建负载均衡器”页的“基本”选项卡中输入或选择以下信息，接受其余的默认设置，然后选择“查看 + 创建”：

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | 订阅               | 选择订阅。    |    
    | 资源组         | 选择“新建”并在文本框中键入 MyResourceGroupLB。|
    | 名称                   | *myLoadBalancer*                                   |
    | 区域         | 选择“西欧”。                                        |
    | 类型          | 选择“公共”。                                        |
    | SKU           | 选择“基本”。                          |
    | 公共 IP 地址 | 选择“新建”。 |
    | 公共 IP 地址名称              | *MyPublicIP*   |
    | 分配| 静态|

3. 在“查看 + 创建”选项卡中，单击“创建”。   


## <a name="create-back-end-servers"></a>创建后端服务器

接下来，请创建一个虚拟网络，并为基本负载均衡器的后端池创建两个虚拟机。 

### <a name="create-a-virtual-network"></a>创建虚拟网络

1. 在门户的左上方，选择“创建资源” > “网络” > “虚拟网络”。
   
1. 在“创建虚拟网络”窗格中键入或选择以下值：
   
   - **名称**：键入 *MyVnet*。
   - **ResourceGroup**：在“选择现有”下拉列表中选择“MyResourceGroupLB”。 
   - “子网” > “名称”：键入 *MyBackendSubnet*。
   
1. 选择“创建”。

   ![创建虚拟网络](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>创建虚拟机

1. 在门户左上角，选择“创建资源” **“计算”** “Windows Server 2016 Datacenter”。 >  >  
   
1. 在“创建虚拟机”中，在“基本信息”选项卡中键入或选择以下值：
   - “订阅” > “资源组”：在下拉列表中选择“MyResourceGroupLB”。
   - “实例详细信息” > “虚拟机名称”：键入 *MyVM1*。
   - “实例详细信息” > “可用性选项”： 
     1. 在下拉列表中选择“可用性集”。 
     2. 选择“新建”，键入 *MyAvailabilitySet*，然后选择“确定”。
  
1. 选择“网络”选项卡，或者选择“下一步: 磁盘”，然后选择“下一步: 网络”。 
   
   确保选中以下项：
   - **虚拟网络**：**MyVnet**
   - **子网**：**MyBackendSubnet**
   - **公共 IP**：**MyVM1-ip**
   
   若要创建新的网络安全组（简称 NSG，一种防火墙），请在“网络安全组”下选择“高级”。 
   1. 在“配置网络安全组”字段中，选择“新建”。 
   1. 键入 *MyNetworkSecurityGroup*，然后选择“确定”。 
   
1. 选择“管理”选项卡，或者选择“下一步” > “管理”。 在“监视”下，将“启动诊断”设置为“关闭”。
   
1. 选择“查看 + 创建”。
   
1. 检查设置，然后选择“创建”。 

1. 按步骤创建另一个 VM，其名称为 *MyVM2*，其“公共 IP”地址为 **MyVM2-ip**，所有其他设置与 MyVM1 相同。 

### <a name="create-nsg-rules-for-the-vms"></a>为 VM 创建 NSG 规则

在本部分，请为 VM 创建网络安全组 (NSG) 规则，允许入站 Internet (HTTP) 和远程桌面 (RDP) 连接。

1. 选择左侧菜单上的“所有资源”。 在资源列表中，选择“MyResourceGroupLB”资源组中的“MyNetworkSecurityGroup”。
   
1. 在“设置”下，依次选择“入站安全规则”、“添加”。
   
1. 在“添加入站安全规则”对话框中，键入或选择以下内容作为 HTTP 规则：
   
   - **源**：选择“服务标记”。  
   - **源服务标记**：选择“Internet”。 
   - **目标端口范围**：键入 *80*。
   - **协议**：选择“TCP”。 
   - **操作**：选择“允许”。  
   - **优先级**：键入 *100*。 
   - **名称**：键入 *MyHTTPRule*。 
   - **说明**：键入“允许 HTTP”。 
   
1. 选择“添加”。 
   
   ![创建 NSG 规则](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
   
1. 重复入站 RDP 规则的步骤，使用下述不同的值：
   - **目标端口范围**：键入 *3389*。
   - **优先级**：键入 *200*。 
   - **名称**：键入 *MyRDPRule*。 
   - **说明**：键入“允许 RDP”。 

## <a name="create-resources-for-the-load-balancer"></a>为负载均衡器创建资源

在本部分中，将为后端地址池、运行状况探测和负载均衡器规则配置负载均衡器设置。

### <a name="create-a-backend-address-pool"></a>创建后端地址池

若要向 VM 分配流量，请让负载均衡器使用后端地址池。 后端地址池包含连接到负载均衡器的虚拟网络接口 (NIC) 的 IP 地址。 

**若要创建包含 VM1 和 VM2 的后端地址池，请执行以下操作：**

1. 在左侧菜单中选择“所有资源”，然后在资源列表中选择“MyLoadBalancer”。
   
1. 在“设置”下，依次选择“后端池”、“添加”。
   
1. 在“添加后端池”页上，键入或选择以下值：
   
   - **名称**：键入 *MyBackEndPool*。
   - **关联到**：在下拉列表中选择“可用性集”。
   - **可用性集**：选择“MyAvailabilitySet”。
   
1. 选择“添加目标网络 IP 配置”。 
   1. 将创建的每个虚拟机（**MyVM1** 和 **MyVM2**）添加到后端池。
   2. 添加每台计算机以后，请在下拉列表中选择其**网络 IP 配置**。 
   
1. 选择“确定”。
   
   ![添加后端地址池](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)
   
1. 在“后端池”页上展开 **MyBackendPool**，确保 **VM1** 和 **VM2** 都已列出。

### <a name="create-a-health-probe"></a>创建运行状况探测器

若要允许负载均衡器监视 VM 状态，请使用运行状况探测。 运行状况探测器基于其对运行状况检查的响应，从负载均衡器中动态添加或删除 VM。 

**若要创建运行状况探测来监视 VM 的运行状况，请执行以下操作：**

1. 在左侧菜单中选择“所有资源”，然后在资源列表中选择“MyLoadBalancer”。
   
1. 在“设置”下，依次选择“运行状况探测”、“添加”。
   
1. 在“添加运行状况探测”页上，键入或选择以下值：
   
   - **名称**：键入 *MyHealthProbe*。
   - **协议**：在下拉列表中选择“HTTP”。 
   - **端口**：键入 *80*。 
   - **路径**：接受 */* 作为默认 URI。 可以将此值替换为任何其他的 URI。 
   - **时间间隔**：键入 *15*。 时间间隔是两次探测尝试之间的秒数。
   - **不正常阈值**：键入 *2*。 此值是将 VM 视为不正常之前发生的连续探测失败次数。
   
1. 选择“确定”。
   
   ![添加探测](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则定义如何将流量分配给 VM。 此规则定义传入流量的前端 IP 配置、用于接收流量的后端 IP 池，以及所需的源和目标端口。 

名为 **MyLoadBalancerRule** 的负载均衡器规则在前端 **LoadBalancerFrontEnd** 中侦听端口 80。 该规则也在端口 80 上将网络流量发送到后端地址池 **MyBackEndPool**。 

**若要创建负载均衡器规则，请执行以下操作：**


1. 在左侧菜单中选择“所有资源”，然后在资源列表中选择“MyLoadBalancer”。
   
1. 在“设置”下，依次选择“负载均衡规则”、“添加”。
   
1. 在“添加负载均衡规则”页上，键入或选择以下值：
   
   - **名称**：键入 *MyLoadBalancerRule*。
   - **前端 IP 地址**：键入 *LoadBalancerFrontend*。
   - **协议**：选择“TCP”。
   - **端口**：键入 *80*。
   - **后端端口**：键入 *80*。
   - **后端池**：选择“MyBackendPool”。
   - **运行状况探测**：选择“MyHealthProbe”。 
   
1. 选择“确定”。
   
   ![添加负载均衡器规则](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>测试负载均衡器

需使用公共 IP 地址在 VM 上测试负载均衡器。 

在门户中的 **MyLoadBalancer** 的“概览”页上，在“公共 IP 地址”下找到其公共 IP 地址。 将鼠标悬停在地址上，选择“复制”图标对其进行复制。 

### <a name="install-iis-on-the-vms"></a>在 VM 上安装 IIS

在虚拟机上安装 Internet Information Services (IIS) 以帮助测试负载均衡器。

**若要通过远程桌面 (RDP) 连接到 VM，请执行以下操作：**

1. 在门户的左侧菜单中选择“所有资源”。 在资源列表中，选择“MyResourceGroupLB”资源组中的“MyVM1”。
   
1. 在“概览”页上选择“连接”，然后选择“下载 RDP 文件”。 
   
1. 打开下载的 RDP 文件，然后选择“连接”。
   
1. 在“Windows 安全性”屏幕上选择“更多选择”，然后选择“使用其他帐户”。 
   
   输入用户名和密码，然后选择“确定”。
   
1. 使用“是”对任何证书请求进行响应。 
   
   VM 桌面会在新窗口中打开。 
   
**安装 IIS**

1. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后在资源列表中选择位于“myResourceGroupSLB”资源组中的“myVM1”。
2. 在“概述”页上选择“连接”，以通过 RDP 连接到 VM。
5. 使用在创建此 VM 过程中提供的凭据登录到 VM。 此时会通过虚拟机 *myVM1* 启动远程桌面会话。
6. 在服务器桌面上，导航到“Windows 管理工具”>“Windows PowerShell”。
7. 在 PowerShell 窗口中，运行以下命令安装 IIS 服务器，删除默认 iisstart.htm 文件，然后添加显示 VM 名称的新 iisstart.htm 文件：

   ```azurepowershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
    remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. 关闭与 *myVM1* 之间的 RDP 会话。
7. 重复步骤 1 到 6，在 *myVM2* 上安装 IIS 和更新后的 iisstart.htm 文件。
   
1. 对虚拟机 **MyVM2** 重复这些步骤，唯一例外是将目标服务器设置为 **MyVM2**。

### <a name="test-the-load-balancer"></a>测试负载均衡器

打开浏览器并将负载均衡器的公共 IP 地址粘贴到浏览器的地址栏中。 IIS Web 服务器默认页会显示在浏览器中。

![IIS Web 服务器](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

若要查看负载均衡器如何在运行应用的两台 VM 之间分配流量，可以强制刷新 Web 浏览器。
## <a name="clean-up-resources"></a>清理资源

若要在不再需要的情况下删除负载均衡器和所有相关的资源，请打开 **MyResourceGroupLB** 资源组，然后选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个基本层负载均衡器。 你创建并配置了资源组、网络资源、后端服务器、运行状况探测，以及适用于负载均衡器的规则。 你在 VM 上安装了 IIS，并使用它来测试负载均衡器。 

若要了解有关 Azure 负载均衡器的详细信息，请继续学习相关教程。

> [!div class="nextstepaction"]
> [Azure 负载均衡器教程](tutorial-load-balancer-basic-internal-portal.md)
