---
title: 教程：创建内部负载均衡器 - Azure 门户
titleSuffix: Azure Load Balancer
description: 本教程介绍如何使用 Azure 门户创建内部标准负载均衡器。
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internal traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2020
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 918a7700df6b5be3ebca7949875127e42f8d3a91
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "75780692"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-standard-load-balancer-in-the-azure-portal"></a>教程：在 Azure 门户中使用标准负载均衡器对内部流量负载进行均衡

负载均衡将传入请求分布到虚拟机 (VM)，以便提供更高级别的可用性和可伸缩性。 可以使用 Azure 门户创建标准负载均衡器，并在 VM 之间对内部流量进行均衡。 本教程介绍如何在标准定价层创建和配置内部负载均衡器、后端服务器和网络资源。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

如果你愿意，可以使用 [Azure CLI](load-balancer-get-started-ilb-arm-cli.md) 或 [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) 而不是门户来完成这些步骤。

若要完成本教程中的这些步骤，请通过 [https://portal.azure.com](https://portal.azure.com) 登录 Azure 门户。

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>创建 VNet、后端服务器和测试 VM

首先，创建虚拟网络 (VNet)。 在 VNet 中，创建两个 VM，用于标准负载均衡器的后端池，再创建第三个 VM，用于测试负载均衡器。 

### <a name="create-a-virtual-network"></a>创建虚拟网络

1. 在门户的左上方，选择“创建资源”   > “网络”   > “虚拟网络”  。
   
1. 在“创建虚拟网络”窗格中键入或选择以下值： 
   
   - **Name**：键入“MyVNet”  。
   - **ResourceGroup**：选择“新建”，输入 **MyResourceGroupLB**，然后选择“确定”   。 
   - **子网** > **名称**：键入“MyBackendSubnet”  。
   
1. 选择“创建”  。

   ![创建虚拟网络](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>创建虚拟机

1. 在门户左上角，选择“创建资源” > “计算” > “Windows Server 2016 Datacenter”。    
   
1. 在“创建虚拟机”中，在“基本信息”选项卡中键入或选择以下值：  
   - **订阅** > **资源组**：下拉并选择“MyResourceGroupLB”。 
   - **实例详细信息** > **虚拟机名称**：键入 **MyVM1**。
   - “实例详细信息” > “区域”：   选择“美国东部 2”。 
  
   
1. 选择“网络”  选项卡，或选择“下一步: **磁盘”，然后选择“下一步:**  网络”。 
   
   确保选中以下项：
   - **虚拟网络**：**MyVNet**
   - **子网**：**MyBackendSubnet**
   - **NIC 网络安全组**：选择“基本”。 
   - **公共 IP** > 选择“新建”  并输入以下值，然后选择“确定”  ：
       - **Name**：**MyVM1-IP**
       - **SKU**：选择“标准” 
   - **公共入站端口**：选择“允许所选端口”  。
   - **选择入站端口**：下拉并选择“RDP (3389)” 

   
   
1. 选择“管理”选项卡，或者选择“下一步”   >   “管理”。  在“监视”  下，将“启动诊断”  设置为“关闭”。 
   
1. 选择“查看 + 创建”  。
   
1. 检查设置，然后选择“创建”。  

1. 按步骤创建另一个 VM，其名称为 **MyVM2**，所有其他设置与 MyVM1 相同。 

1. 按步骤创建第三个 VM，请名称为 **MyTestVM**。 

## <a name="create-a-standard-load-balancer"></a>创建标准负载均衡器

使用门户创建标准内部负载均衡器。 创建的名称和 IP 地址自动配置为负载均衡器的前端。

1. 在门户的左上方，选择“创建资源” > “网络” > “负载均衡器”。   
   
2. 在“创建负载均衡器”页的“基本”选项卡中输入或选择以下信息，接受其余的默认设置，然后选择“查看 + 创建”    ：

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | 订阅               | 选择订阅。    |    
    | 资源组         | 选择“新建”并在文本框中键入 MyResourceGroupLB   。|
    | 名称                   | *myLoadBalancer*                                   |
    | 区域         | 选择“美国东部 2”。                                         |
    | 类型          | 选择“内部”。                                         |
    | SKU           | 选择“标准”  。                          |
    | 虚拟网络           | 选择“MyVNet”  。                          |    
    | IP 地址分配              | 选择“静态”  。   |
    | 专用 IP 地址|键入一个位于虚拟网络和子网地址空间中的地址，例如 *10.3.0.7*。  |

3. 在“查看 + 创建”选项卡中，单击“创建”   。 
   

## <a name="create-standard-load-balancer-resources"></a>创建标准负载均衡器资源

在本部分中，请为后端地址池和运行状况探测配置负载均衡器设置，并指定负载均衡器规则。

### <a name="create-a-back-end-address-pool"></a>创建后端地址池

若要向 VM 分配流量，请让负载均衡器使用后端地址池。 后端地址池包含连接到负载均衡器的虚拟网络接口 (NIC) 的 IP 地址。 

**若要创建包含 VM1 和 VM2 的后端地址池，请执行以下操作：**

1. 在左侧菜单中选择“所有资源”，然后在资源列表中选择“MyLoadBalancer”。  
   
1. 在“设置”下，依次选择“后端池”、“添加”。   
   
1. 在“添加后端池”  页上，键入或选择以下值：
   
   - **Name**：键入 **MyBackendPool**。
   
1. 在“虚拟机”  下。 
   1. 将 **MyVM1** 和 **MyVM2** 添加到后端池。
   2. 添加每台计算机以后，请在下拉列表中选择其**网络 IP 配置**。 
     
1. 选择 **添加** 。
   
   ![添加后端地址池](./media/tutorial-load-balancer-standard-internal-portal/3-load-balancer-backend-02.png)
   
1. 在“后端池”页上  展开 **MyBackendPool**，确保 **VM1** 和 **VM2** 都已列出。

### <a name="create-a-health-probe"></a>创建运行状况探测器

若要允许负载均衡器监视 VM 状态，请使用运行状况探测。 运行状况探测器基于其对运行状况检查的响应，从负载均衡器中动态添加或删除 VM。 

**若要创建运行状况探测来监视 VM 的运行状况，请执行以下操作：**

1. 在左侧菜单中选择“所有资源”，然后在资源列表中选择“MyLoadBalancer”。  
   
1. 在“设置”下，依次选择“运行状况探测”、“添加”。   
   
1. 在“添加运行状况探测”  页上，键入或选择以下值：
   
   - **Name**：键入 **MyHealthProbe**。
   - **协议**：下拉并选择“HTTP”  。 
   - **端口**：键入 **80**。 
   - **路径**：接受 **/** 作为默认 URI。 可以将此值替换为任何其他的 URI。 
   - **时间间隔**：键入 **15**。 时间间隔是两次探测尝试之间的秒数。
   - **不正常阈值**：键入 **2**。 此值是将 VM 视为不正常之前发生的连续探测失败次数。
   
1. 选择“确定”  。
   
   ![添加探测](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则定义如何将流量分配给 VM。 此规则定义传入流量的前端 IP 配置、用于接收流量的后端 IP 池，以及所需的源和目标端口。 

名为 **MyLoadBalancerRule** 的负载均衡器规则在前端 **LoadBalancerFrontEnd** 中侦听端口 80。 该规则也在端口 80 上将网络流量发送到后端地址池 **MyBackendPool**。 

**若要创建负载均衡器规则，请执行以下操作：**

1. 在左侧菜单中选择“所有资源”，然后在资源列表中选择“MyLoadBalancer”。  
   
1. 在“设置”下，依次选择“负载均衡规则”、“添加”。   
   
1. 在“添加负载均衡规则”  页上，键入或选择以下值（如果还没有这些值）：
   
   - **Name**：键入 **MyLoadBalancerRule**。
   - **前端 IP 地址：** 键入 **LoadBalancerFrontEnd**（如果不存在）。
   - **协议**：选择“TCP”  。
   - **端口**：键入 **80**。
   - **后端端口**：键入 **80**。
   - **后端池**：选择“MyBackendPool”  。
   - **运行状况探测**：选择“MyHealthProbe”  。 
   
1. 选择“确定”  。
   
   ![添加负载均衡器规则](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>测试负载均衡器

在后端服务器上安装 Internet Information Services (IIS)，然后使用 MyTestVM 通过其专用 IP 地址测试负载均衡器。 每个后端 VM 提供不同版本的默认 IIS 网页，因此可以看到负载均衡器在两个 VM 之间分发请求。

在门户中的  **MyLoadBalancer** 的“概览”页上，在“专用 IP 地址”下找到其 IP 地址。  将鼠标悬停在地址上，选择“复制”图标对其进行复制。  在此示例中，它是 **10.3.0.7**。 

### <a name="connect-to-the-vms-with-rdp"></a>通过 RDP 连接到 VM

首先，通过远程桌面 (RDP) 连接到所有三个 VM。 

>[!NOTE]
>默认情况下，这些 VM 已经打开 **RDP**（远程桌面）端口，因此可以进行远程桌面访问。 

**若要通过远程桌面 (RDP) 连接到 VM，请执行以下操作：**

1. 在门户的左侧菜单中选择“所有资源”  。 在资源列表中，选择“MyResourceGroupLB”资源组中的每个 VM。 
   
1. 在“概览”页上选择“连接”，然后选择“下载 RDP 文件”。    
   
1. 打开下载的 RDP 文件，然后选择“连接”。 
   
1. 在“Windows 安全性”屏幕上选择“更多选择”   ，然后选择“使用其他帐户”。 
   
   输入用户名和密码，然后选择“确定”。 
   
1. 使用“是”对任何证书请求进行响应。  
   
   VM 桌面会在新窗口中打开。 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>安装 IIS 并替换后端 VM 上的默认 IIS 页。

在每个后端服务器上，使用 PowerShell 安装 IIS，并将默认的 IIS 网页替换为自定义的页面。

>[!NOTE]
>也可在**服务器管理器**中使用**添加角色和功能向导**来安装 IIS。 

**若要使用 PowerShell 来安装 IIS 并更新默认的网页，请执行以下操作：**

1. 在 MyVM1 和 MyVM2 上，通过“开始”菜单启动 **Windows PowerShell**。  

2. 运行以下命令，以便安装 IIS 并替换默认的 IIS 网页：
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
      remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add custom htm file
      Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. 选择“断开连接”，关闭与 MyVM1 和 MyVM2 的 RDP 连接。  请勿关闭 VM。

### <a name="test-the-load-balancer"></a>测试负载均衡器

1. 在 MyTestVM 上打开 **Internet Explorer**，在出现配置请求时使用“确定”进行响应。  
   
1. 将负载均衡器的专用 IP 地址 (*10.3.0.7*) 粘贴或键入到浏览器的地址栏中。 
   
   自定义的 IIS Web 服务器默认页会显示在浏览器中。 会显示 **Hello World from MyVM1** 或 **Hello World from MyVM2** 消息。
   
1. 刷新浏览器，可以看到负载均衡器在 VM 之间分配流量。 在进行不同的尝试时，可能还需要清除浏览器缓存。

   当负载均衡器将请求分配到每个后端 VM 时，有时会显示 **MyVM1** 页，有时会显示 **MyVM2** 页。 

   ![新建 IIS 默认页](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>清理资源

若要在不再需要的情况下删除负载均衡器和所有相关的资源，请打开 **MyResourceGroupLB** 资源组，然后选择“删除资源组”。 

## <a name="next-steps"></a>后续步骤

在本教程中，你创建了一个标准内部负载均衡器。 你创建并配置了网络资源、后端服务器、运行状况探测，以及适用于负载均衡器的规则。 你在后端 VM 上安装了 IIS，并在浏览器中使用了一个测试 VM 来测试负载均衡器。 

接下来，请了解如何跨可用性区域对 VM 进行负载均衡。

> [!div class="nextstepaction"]
> [跨可用性区域对 VM 进行负载均衡](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
