---
title: 区域中的负载均衡器 VM - Azure 门户 | Microsoft Docs
description: 使用 Azure 门户创建包含区域前端的标准负载均衡器，以便对可用性区域中的 VM 进行负载均衡
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/18/2018
ms.author: kumud
ms.openlocfilehash: 41a33436cb0d2c4c2bbfef4888bb704c62e2b91e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="load-balance-vms-within-an-availability-zone-with-a-standard-load-balancer-using-the-azure-portal"></a>在 Azure 门户中使用标准负载均衡器对可用性区域中的 VM 进行负载均衡

本文逐步讲解如何在 Azure 门户中使用公共 IP 标准地址创建具有区域性前端的公共[负载均衡器标准](https://aka.ms/azureloadbalancerstandard)。 在此场景中，我们将为前端和后端实例指定一个特定的区域，使数据路径和资源与特定的区域相符。
有关对标准负载均衡器使用可用性区域的详细信息，请参阅[标准负载均衡器和可用性区域](load-balancer-standard-availability-zones.md)。

如果需要，也可以使用 [Azure CLI](load-balancer-standard-public-zonal-cli.md) 完成本教程中的步骤。

## <a name="log-in-to-azure"></a>登录 Azure

通过 [http://portal.azure.com](http://portal.azure.com) 登录到 Azure 门户。

## <a name="create-a-public-standard-load-balancer"></a>创建公共标准负载均衡器

标准负载均衡器仅支持标准公共 IP 地址。 在创建负载均衡器过程中创建新的公共 IP 时，该负载均衡器会自动配置为标准 SKU 版本，并自动实现区域冗余。

1. 在屏幕的左上方，单击“创建资源” > “网络” > “负载均衡器”。
2. 在“创建负载均衡器”页中，输入负载均衡器的以下值：
    - *myLoadBalancer* - 负载均衡器的名称。
    - **公共** - 负载均衡器的类型。
     - *myPublicIPZonal* - 创建的新公共 IP 地址。 为此，请依次单击“选择公共 IP 地址”、“新建”。 键入 *myPublicIP* 作为名称，SKU 默认为“标准”，为“可用性区域”选择“区域 1”。
    - *myResourceGroupZLB* - 所创建的新资源组的名称。
    - **westeurope** - 位置。
3. 单击“创建”以创建负载均衡器。
   
    ![使用 Azure 门户创建区域负载均衡器标准](./media/tutorial-load-balancer-standard-zonal-portal/create-load-balancer-zonal-frontend.png)


## <a name="create-backend-servers"></a>创建后端服务器

在本部分，我们将创建一个虚拟网络，在同一区域（即区域 1）中创建要添加到负载均衡器后端池的两个虚拟机，然后在虚拟机上安装 IIS，以帮助便对区域冗余的负载均衡器进行测试。 因此，如果一个 VM 发生故障，则同一区域中 VM 的运行状况探测将会失败，而流量可继续由同一区域中的 VM 提供服务。

### <a name="create-a-virtual-network"></a>创建虚拟网络
1. 在屏幕的左上方，单击“创建资源” > “网络” > “虚拟网络”，然后输入虚拟网络的以下值：
    - *myVNet* - 虚拟网络的名称。
    - *myResourceGroupZLB* - 现有资源组的名称。
    - *myBackendSubnet* - 子网名称。
2. 单击“创建”以创建虚拟网络。

    ![创建虚拟网络](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-network.png)

## <a name="create-a-network-security-group"></a>创建网络安全组

1. 在屏幕的左上方单击“创建资源”，在搜索框中键入“网络安全组”，然后在网络安全组页上单击“创建”。
2. 在“创建网络安全组”页中输入以下值：
    - *myNetworkSecurityGroup* - 网络安全组的名称。
    - *myResourceGroupLBAZ* - 现有资源组的名称。
   
    ![创建虚拟网络](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>创建 NSG 规则

在本部分，我们将创建 NSG 规则以允许使用 HTTP、RDP 和 Azure 门户建立入站连接。

1. 在 Azure 门户的左侧菜单中单击“所有资源”，然后在 **myResourceGroupZLB** 资源组中搜索 **myNetworkSecurityGroup** 并单击它。
2. 在“设置”下单击“入站安全规则”，然后单击“添加”。
3. 为名为 *myHTTPRule* 的入站安全规则输入以下值，以允许来自端口 80 的入站 HTTP 连接：
    - 服务标记 - **源**。
    - *Internet* - **源服务标记**
    - *80* - **目标端口范围**
    - *TCP* - **协议**
    - 允许 - **操作**
    - *100* - **优先级**
    - *myHTTPRule* - **名称**
    - *允许 HTTP* - **说明**
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

    ![创建 RDP 规则](./media/tutorial-load-balancer-standard-zonal-portal/create-rdp-rule.png)

### <a name="create-virtual-machines"></a>创建虚拟机

1. 在屏幕的左上方，单击“创建资源” > “计算” > “Windows Server 2016 Datacenter”，然后输入虚拟机的以下值：
    - *myVM1* - 虚拟机的名称。        
    - *azureuser* - 管理员用户名。    
    - *myResourceGroupZLB* - 对于“资源组”，请选择“使用现有”，然后选择“myResourceGroupZLB”。
2. 单击“确定”。
3. 选择“DS1_V2”作为虚拟机的大小，然后单击“选择”。
4. 为 VM 设置输入以下值：
    - *区域 1* - 放置 VM 的可用性区域。
    -  *myVNet* - 确保选择它作为虚拟网络。
    - *myVM1PIP* - 创建的标准公共 IP 地址。 单击“新建”，键入 *myVM1PIP* 作为名称，为“区域”选择“1”。 IP 地址 SKU 默认为“标准”。
    - *myBackendSubnet* - 确保选择它作为子网。
    - *myNetworkSecurityGroup* - 现有网络安全组（防火墙）的名称。
5. 单击“禁用”以禁用启动诊断。
6. 创建“确定”，检查“摘要”页上的设置，然后单击“创建”。
7. 执行步骤 1-6，在区域 1 中创建名为 *myVM2* 的第二个 VM，并使用 *myVnet* 作为虚拟网络名称，使用 *myVM2PIP* 作为标准公共 IP 地址，使用 *myBackendSubnet* 作为子网，使用 **myNetworkSecurityGroup* 作为网络安全组。

    ![创建 RDP 规则](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>在 VM 上安装 IIS

1. 在左侧菜单中单击“所有资源”，在资源列表中，单击位于 *myResourceGroupZLB* 资源组中的“myVM1”。
2. 在“概览”页上单击“连接”，以便通过 RDP 连接到 VM 中。
3. 使用在创建 VM 时指定的用户名和密码登录到 VM（可能需要选择“更多选项”，然后选择“使用其他帐户”指定创建 VM 时输入的凭据），然后选择“确定”。 你可能会在登录过程中收到证书警告。 选择“是”以继续进行连接。
4. 在服务器桌面上，导航到“Windows 管理工具”>“Windows PowerShell”。
6. 在 PowerShell 窗口中，运行以下命令安装 IIS 服务器，删除 default.htm 文件，然后添加显示 VM 名称的新 default.htm 文件：

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello from" + $env:computername)
   ```
8. 关闭与 *myVM1* 之间的 RDP 会话
9. 重复步骤 1 到 8，在 *myVM2* 上安装 IIS。

## <a name="create-load-balancer-resources"></a>创建负载均衡器资源

在此部分，请为后端地址池和运行状况探测配置负载均衡器设置，并指定负载均衡器和 NAT 规则。


### <a name="create-a-backend-address-pool"></a>创建后端地址池

若要向 VM 分发流量，后端地址池需包含连接到负载均衡器的虚拟 NIC 的 IP 地址。 创建包括 *VM1* 和 *VM2* 的后端地址池 *myBackendPool*。

1. 在左侧菜单中单击“所有资源”，在资源列表中单击“myLoadBalancer”。
2. 在“设置”下单击“后端池”，然后单击“添加”。
3. 在“添加后端池”页上执行以下操作：
    - 对于“名称”，请键入 *myBackEndPool* 作为后端池的名称。
    - 在下拉菜单中，针对“虚拟网络”单击“myVNet”。
    - 对于“虚拟机”和“IP 地址”，请添加 *myVM1* 和 *myVM2* 及其对应的公共 IP 地址。
4. 单击 **“添加”**。
5. 通过检查确保负载均衡器后端池设置显示 **myVM1** 和 **myVM2** 这两个 VM。
 
    ![创建后端池](./media/tutorial-load-balancer-standard-zonal-portal/create-backend-pool.png) 

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
    
    ![添加负载均衡规则](./media/tutorial-load-balancer-standard-zonal-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>测试负载均衡器
1. 在“概览”屏幕上找到负载均衡器的公共 IP 地址。 单击“所有资源”，然后单击“myPublicIP”。 

2. 复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。 包含 Web 服务器页名称的默认页会显示在浏览器上。

      ![IIS Web 服务器](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. 若要查看负载均衡器的运行情况，可以强制停止显示的 VM，并刷新浏览器，以查看浏览器上显示的其他服务器名称。

## <a name="clean-up-resources"></a>清理资源

若不再需要资源组、负载均衡器以及所有相关资源，请将其删除。 为此，请选择包含负载均衡器的资源组，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

- 详细了解[标准负载均衡器](load-balancer-standard-overview.md)。
- [跨可用性区域对 VM 进行负载均衡](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
