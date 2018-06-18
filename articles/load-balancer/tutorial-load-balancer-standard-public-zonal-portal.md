---
title: 教程：区域中的负载均衡器 VM--Azure 门户 | Microsoft Docs
description: 本教程演示如何使用 Azure 门户创建包含区域前端的标准负载均衡器，以便对可用性区域中的 VM 进行负载均衡
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 580015b7f8b1f894c69ddec0f26daeb524932e4b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637287"
---
# <a name="tutorial-load-balance-vms-within-an-availability-zone-with-standard-load-balancer-by-using-the-azure-portal"></a>教程：通过 Azure 门户使用标准负载均衡器对可用性区域中的 VM 进行负载均衡

本教程通过 Azure 门户创建具有区域前端的公共 [Azure 标准负载均衡器实例](https://aka.ms/azureloadbalancerstandard)，该前端使用公共的 IP 标准地址。 在此方案中，请为前端和后端实例指定一个特定的区域，使数据路径和资源与特定的区域相符。 请了解如何执行以下功能：

> [!div class="checklist"]
> * 创建具有区域前端的标准负载均衡器实例。
> * 创建网络安全组来定义传入流量规则。
> * 创建区域虚拟机 (VM) 并将其附加到负载均衡器。
> * 创建负载均衡器运行状况探测。
> * 创建负载均衡器流量规则。
> * 创建基本的 Internet Information Services (IIS) 站点。
> * 查看运行中的负载均衡器。

有关对标准负载均衡器使用可用性区域的详细信息，请参阅[标准负载均衡器和可用性区域](load-balancer-standard-availability-zones.md)。

如果需要，可使用 [Azure CLI](load-balancer-standard-public-zonal-cli.md) 完成本教程。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [http://portal.azure.com](http://portal.azure.com) 中登录 Azure 门户。

## <a name="create-a-public-standard-load-balancer-instance"></a>创建公共标准负载均衡器实例

标准负载均衡器仅支持标准公共 IP 地址。 在创建负载均衡器过程中创建新的公共 IP 时，该负载均衡器会自动配置为标准 SKU 版本， 并自动实现区域冗余。

1. 在屏幕的左上方，选择“创建资源” > “网络” > “负载均衡器”。
2. 在“创建负载均衡器”页中，为负载均衡器输入以下值：
    - **myLoadBalancer**：负载均衡器的名称。
    - **公共**：负载均衡器的类型。
     - **myPublicIPZonal**：创建的新公共 IP 地址。 选择“选择公共 IP 地址”。 然后选择“新建”。 对于“名称”，请输入 **myPublicIP**。 SKU 默认为“标准”。 对于“可用性区域”，请选择“区域 1”。
    - **myResourceGroupZLB**：所创建的新资源组的名称。
    - **westeurope**：位置。
3. 选择“创建”以创建负载均衡器。
   
    ![使用 Azure 门户创建区域标准负载均衡器实例](./media/tutorial-load-balancer-standard-zonal-portal/create-load-balancer-zonal-frontend.png)


## <a name="create-backend-servers"></a>创建后端服务器

在本部分，请创建虚拟网络。 另请在该地区的同一区域（即区域 1）中创建两个要添加到负载均衡器后端池的虚拟机， 然后在虚拟机上安装 IIS，以便对区域冗余的负载均衡器进行测试。 如果一个 VM 发生故障，则同一区域中 VM 的运行状况探测将会失败， 而流量可继续由同一区域中的其他 VM 处理。

### <a name="create-a-virtual-network"></a>创建虚拟网络
1. 在屏幕的左上方，选择“创建资源” > “网络” > “虚拟网络”。  为虚拟网络输入以下值：
    - **myVnet**：虚拟网络的名称。
    - **myResourceGroupZLB**：现有资源组的名称。
    - **myBackendSubnet**：子网名称。
2. 选择“创建”以创建虚拟网络。

    ![创建虚拟网络](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-network.png)

## <a name="create-a-network-security-group"></a>创建网络安全组

1. 在屏幕的左上方，选择“创建资源”。 在搜索框中，输入“网络安全组”。 在网络安全组页中，选择“创建”。
2. 在“创建网络安全组”页中输入以下值：
    - **myNetworkSecurityGroup**：网络安全组的名称。
    - **myResourceGroupLBAZ**：现有资源组的名称。
   
    ![创建网络安全组](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>创建 NSG 规则

在本部分，请通过 Azure 门户创建 NSG 规则，以便允许使用 HTTP 和 Microsoft 远程桌面协议 (RDP) 的入站连接。

1. 在 Azure 门户的最左侧菜单中，选择“所有资源”。 然后搜索“myNetworkSecurityGroup”并将其选中。 它位于 **myResourceGroupZLB** 资源组中。
2. 在“设置”下，选择“入站安全规则”。 然后选择“添加”。
3. 为名为 **myHTTPRule** 的入站安全规则输入以下值，以允许使用端口 80 的入站 HTTP 连接：
    - 输入“服务标记”作为“源”。
    - 输入 **Internet** 作为“源服务标记”。
    - 输入 **80** 作为“目标端口范围”。
    - 输入 **TCP** 作为“协议”。
    - 输入“允许”作为“操作”。
    - 输入 **100** 作为“优先级”。
    - 输入 **myHTTPRule** 作为“名称”。
    - 输入“允许 HTTP”作为“说明”。
4. 选择“确定”。
 
 ![创建 NSG 规则](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)

5. 重复步骤 2 到 4，创建名为 **myRDPRule** 的另一规则。 该规则允许使用端口 3389 的入站 RDP 连接，输入的值如下：
    - 输入“服务标记”作为“源”。
    - 输入 **Internet** 作为“源服务标记”。
    - 输入 **3389** 作为“目标端口范围”。
    - 输入 **TCP** 作为“协议”。
    - 输入“允许”作为“操作”。
    - 输入 **200** 作为“优先级”。
    - 输入 **myRDPRule** 作为“名称”。
    - 输入“允许 RDP”作为“说明”。

    ![创建 RDP 规则](./media/tutorial-load-balancer-standard-zonal-portal/create-rdp-rule.png)

### <a name="create-virtual-machines"></a>创建虚拟机

1. 在屏幕左上角，选择“创建资源” > “计算” > “Windows Server 2016 Datacenter”。 输入虚拟机的以下值：
    - **myVM1**：虚拟机的名称。        
    - **azureuser**：管理员用户名。    
    - **myResourceGroupZLB**：**资源组**。 选择“使用现有”，然后选择“myResourceGroupZLB”
2. 选择“确定”。
3. 选择“DS1_V2”作为虚拟机的大小。 选择“选择”。
4. 为 VM 设置输入以下值：
    - **区域 1**：放置 VM 的可用性区域。
    -  **myVNet**。 确保选择它作为虚拟网络。
    - **myVM1PIP**：创建的标准公共 IP 地址。 选择“新建”。 然后，选择 **myVM1PIP** 作为名称类型。 选择 **1** 作为“区域”。 IP 地址 SKU 默认为“标准”。
    - **myBackendSubnet**。 确保选择它作为子网。
    - **myNetworkSecurityGroup**：现有网络安全组防火墙的名称。
5. 选择“禁用”以禁用启动诊断。
6. 选择“确定”。 在摘要页上查看设置。 然后选择“创建”。
7. 重复步骤 1 到 6，在区域 1 中创建名为 **myVM2** 的另一个 VM。 将 **myVnet** 设为虚拟网络。 将 **myVM2PIP** 设为标准公共 IP 地址。 将 **myBackendSubnet** 设为子网。 将 **myNetworkSecurityGroup** 设为网络安全组。

    ![创建虚拟机](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>在 VM 上安装 IIS

1. 在最左侧菜单中，选择“所有资源”。 然后从资源列表中选择“myVM1”。 它位于 **myResourceGroupZLB** 资源组中。
2. 在“概览”页上选择“连接”，通过 RDP 转到 VM。
3. 使用创建 VM 时指定的用户名和密码登录到 VM。 若要指定在创建 VM 时输入的凭据，可能需要选择“更多选择”， 然后选择“使用其他帐户”， 再选择“确定”。 你可能会在登录过程中收到证书警告。 选择“是”以继续进行连接。
4. 在服务器桌面上，导航到“Windows 管理工具” > “Windows PowerShell”。
6. 在 **PowerShell** 窗口中运行以下命令，以便安装 IIS 服务器。 这些命令还删除默认 iisstart.htm 文件，然后添加显示 VM 名称的新 iisstart.htm 文件：

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
7. 关闭与 **myVM1** 之间的 RDP 会话。
8. 重复步骤 1 到 7，在 **myVM2** 上安装 IIS。

## <a name="create-load-balancer-resources"></a>创建负载均衡器资源

在本部分，请为后端地址池和运行状况探测配置负载均衡器设置。 另请指定负载均衡器和网络地址转换规则。


### <a name="create-a-backend-address-pool"></a>创建后端地址池

若要向 VM 分配流量，后端地址池需包含连接到负载均衡器的虚拟网络接口卡的 IP 地址。 创建包括 **VM1** 和 **VM2** 的后端地址池 **myBackendPool**。

1. 在最左侧菜单中，选择“所有资源”。 然后从资源列表中选择“myLoadBalancer”。
2. 在“设置”下，选择“后端池”。 然后选择“添加”。
3. 在“添加后端池”页上执行以下操作：
    - 对于“名称”，请输入 **myBackEndPool** 作为后端池的名称。
    - 在下拉菜单中，请选择“myVNet”作为“虚拟网络”。 
    - 对于“虚拟机”和“IP 地址”，请添加 **myVM1** 和 **myVM2** 及其对应的公共 IP 地址。
4. 选择 **添加** 。
5. 通过检查确保负载均衡器后端池设置显示 **myVM1** 和 **myVM2** 这两个 VM。
 
    ![创建后端池](./media/tutorial-load-balancer-standard-zonal-portal/create-backend-pool.png) 

### <a name="create-a-health-probe"></a>创建运行状况探测器

使用运行状况探测器，以便负载均衡器能够监视应用的状态。 运行状况探测器基于其对运行状况检查的响应，从负载均衡器中动态添加或删除 VM。 创建运行状况探测 **myHealthProbe** 以监视 VM 的运行状况。

1. 在最左侧菜单中，选择“所有资源”。 然后从资源列表中选择“myLoadBalancer”。
2. 在“设置”下，选择“运行状况探测”。 然后选择“添加”。
3. 使用以下值创建运行状况探测：
    - **myHealthProbe**：运行状况探测的名称。
    - **HTTP**：协议类型。
    - **80**：端口号。
    - **15**：两次探测尝试之间的**时间间隔**（以秒为单位）。
    - **2**：将 VM 视为不正常所对应的**不正常阈值**或连续探测失败次数。
4. 选择“确定”。

   ![添加运行状况探测](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则定义如何将流量分配给 VM。 定义传入流量的前端 IP 配置和后端 IP 池以接收流量，同时定义所需的源端口和目标端口。 创建负载均衡器规则 **myLoadBalancerRuleWeb**，用于侦听前端 **FrontendLoadBalancer** 中的端口 80。 该规则也使用端口 80 将经过负载均衡的网络流量发送到后端地址池 **myBackEndPool**。 

1. 在最左侧菜单中，选择“所有资源”。 然后从资源列表中选择“myLoadBalancer”。
2. 在“设置”下，选择“负载均衡规则”。 然后选择“添加”。
3. 请使用以下值配置负载均衡规则：
    - **myHTTPRule**：负载均衡规则的名称。
    - **TCP**：协议类型。
    - **80**：端口号。
    - **80**：后端端口。
    - **myBackendPool**：后端池的名称。
    - **myHealthProbe**：运行状况探测的名称。
4. 选择“确定”。
    
    ![添加负载均衡规则](./media/tutorial-load-balancer-standard-zonal-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>测试负载均衡器
1. 在“概述”屏幕上找到负载均衡器的公共 IP 地址。 选择“所有资源”， 然后选择“myPublicIP”。 

2. 复制公共 IP 地址， 然后将其粘贴到浏览器的地址栏中。 包含 Web 服务器页名称的默认页会显示在浏览器上。

      ![IIS Web 服务器](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. 若要查看正在运行的负载均衡器，请强制停止显示的 VM。 刷新浏览器即刻查看浏览器上显示的其他服务器名称。

## <a name="clean-up-resources"></a>清理资源

若不再需要资源组、负载均衡器以及所有相关资源，请将其删除。 选择包含负载均衡器的资源组， 然后选择“删除”。

## <a name="next-steps"></a>后续步骤

- 详细了解[标准负载均衡器](load-balancer-standard-overview.md)。
- [跨可用性区域对 VM 进行负载均衡](tutorial-load-balancer-standard-public-zone-redundant-portal.md)。
