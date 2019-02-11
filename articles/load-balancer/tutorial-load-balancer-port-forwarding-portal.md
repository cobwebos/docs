---
title: 教程：使用 Azure 门户在 Azure 负载均衡器中配置端口转发
titlesuffix: Azure Load Balancer
description: 本教程介绍了如何使用 Azure 负载均衡器配置端口转发来创建与 Azure 虚拟网络中的 VM 的连接。
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: As an IT administrator, I want to configure port forwarding in Azure Load Balancer to remotely connect to VMs in an Azure virtual network.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/18
ms.author: kumud
ms.custom: seodec18
ms.openlocfilehash: da41b33f3e5d24c0391c8486d9c0b372877eff21
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2019
ms.locfileid: "54232186"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-portal"></a>教程：使用门户在 Azure 负载均衡器中配置端口转发

端口转发使你可以使用 Azure 负载均衡器公共 IP 地址和端口号连接到 Azure 虚拟网络中的虚拟机 (VM)。 

在本教程中，你将在 Azure 负载均衡器上设置端口转发。 学习如何：

> [!div class="checklist"]
> * 创建公共标准负载均衡器来均衡 VM 上的网络流量。 
> * 使用网络安全组 (NSG) 规则创建虚拟网络和 VM。 
> * 将 VM 添加到负载均衡器后端地址池。
> * 创建负载均衡器运行状况探测和流量规则。
> * 创建负载均衡器入站 NAT 端口转发规则。
> * 在 VM 上安装和配置 IIS，以查看负载均衡和运行中的端口转发。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

对于本教程中的所有步骤，请通过 [https://portal.azure.com](https://portal.azure.com) 登录 Azure 门户。

## <a name="create-a-standard-load-balancer"></a>创建标准负载均衡器

首先，创建公共标准负载均衡器，以便均衡 VM 上的流量负载。 标准负载均衡器仅支持标准公共 IP 地址。 创建标准负载均衡器时，还要创建配置为负载均衡器前端且默认情况下命名为“LoadBalancerFrontEnd”的新的标准公用 IP 地址。 

1. 在门户的左上方，选择“创建资源” > “网络” > “负载均衡器”。
   
1. 在“创建负载均衡器”窗格中，键入或选择以下值：
   
   - **名称**：键入 *MyLoadBalancer*。
   - **类型**：选择“公共”。 
   - **SKU**：选择“标准”。
   - **公共 IP 地址**：选择“新建”并在字段中键入“MyPublicIP”。
   - **配置公共 IP 地址** > **可用性区域**：选择“区域冗余”。
   - **ResourceGroup**：选择“新建”，输入“MyResourceGroupLB”，然后选择“确定”。 
   - **位置**：选择“西欧”。 
     
     >[!NOTE]
     >确保在支持可用性区域的位置为其创建负载均衡器和所有资源。 有关详细信息，请参阅[支持可用性区域的区域](../availability-zones/az-overview.md#regions-that-support-availability-zones)。 
   
1. 选择“创建”。
   
![创建负载均衡器](./media/tutorial-load-balancer-port-forwarding-portal/1-load-balancer.png)

## <a name="create-and-configure-back-end-servers"></a>创建并配置后端服务器

使用两台虚拟机创建虚拟网络，并将 VM 添加到负载均衡器的后端池。 

### <a name="create-a-virtual-network"></a>创建虚拟网络

1. 在门户的左上方，选择“创建资源” > “网络” > “虚拟网络”。
   
1. 在“创建虚拟网络”窗格中键入或选择以下值：
   
   - **名称**：键入“MyVNet”。
   - **ResourceGroup**：下拉“选择现有项”列表并选择“MyResourceGroupLB”。 
   - “子网” > “名称”：键入“MyBackendSubnet”。
   
1. 选择“创建”。

   ![创建虚拟网络](./media/tutorial-load-balancer-port-forwarding-portal/2-load-balancer-virtual-network.png)

### <a name="create-vms-and-add-them-to-the-load-balancer-back-end-pool"></a>创建 VM 并将其添加到负载均衡器后端池

1. 在门户左上角，选择“创建资源” > “计算” > “Windows Server 2016 Datacenter”。 
   
1. 在“创建虚拟机”中，在“基本信息”选项卡中键入或选择以下值：
   - **订阅** > **资源组**：下拉并选择“MyResourceGroupLB”。
   - **虚拟机名称**：键入“MyVM1”。
   - **区域**：选择“西欧”。 
   - **用户名**：键入“azureuser”。
   - **密码**：键入“Azure1234567”。 
     在“确认密码”字段中，重新键入该密码。
   
1. 选择“网络”选项卡，或选择“下一步:磁盘”，然后选择“下一步:网络”**。 
   
   确保选中以下项：
   - **虚拟网络**：**MyVNet**
   - **子网**：**MyBackendSubnet**
   
1. 在“公共 IP”下，选择“新建”，在“创建公共 IP 地址”页面上选择“标准”，然后选择“确定”。 
   
1. 在“网络安全组”下选择“高级”，以创建新的网络安全组（简称 NSG，一种防火墙）。 
   1. 在“配置网络安全组”字段中，选择“新建”。 
   1. 键入 *MyNetworkSecurityGroup*，然后选择“确定”。 
   
   >[!NOTE]
   >请注意，默认情况下，NSG 已有打开端口 3389（远程桌面 (RDP) 端口）的传入规则。
   
1. 将 VM 添加到创建的负载均衡器后端池：
   
   1. 在“负载均衡” > “将此虚拟机置于现有负载均衡解决方案之后？”下，选择“确定”。 
   1. 对于“负载均衡选项”，下拉并选择“Azure 负载均衡器”。 
   1. 对于“选择负载均衡器”，下拉并选择“MyLoadBalancer”。 
   1. 在“选择后端池”下，选择“新建”，然后输入“MyBackendPool”，再选择“创建”。 
   
   ![创建虚拟网络](./media/tutorial-load-balancer-port-forwarding-portal/create-vm-networking.png)
   
1. 选择“管理”选项卡，或者选择“下一步” > “管理”。 在“监视”下，将“启动诊断”设置为“关闭”。
   
1. 选择“查看 + 创建”。
   
1. 检查设置，验证成功后，选择“创建”。 

1. 按步骤创建另一个 VM，其名称为 *MyVM2*，所有其他设置与 MyVM1 相同。 
   
   对于“网络安全组”，选择“高级”后，下拉并选择已创建的“MyNetworkSecurityGroup”。 
   
   在“选择后端池”，确保选中“MyBackendPool”。 

### <a name="create-an-nsg-rule-for-the-vms"></a>为 VM 创建 NSG 规则

为 VM 创建网络安全组 (NSG) 规则，以允许入站 Internet (HTTP) 连接。

>[!NOTE]
>默认情况下，NSG 已有打开端口 3389（远程桌面 (RDP) 端口）的规则。

1. 在左侧菜单中选择“所有资源”。 在资源列表中，选择“MyResourceGroupLB”资源组中的“MyNetworkSecurityGroup”。
   
1. 在“设置”下，依次选择“入站安全规则”、“添加”。
   
1. 在“添加入站安全规则”对话框中，键入或选择以下内容：
   
   - **源**：选择“服务标记”。  
   - **源服务标记**：选择“Internet”。 
   - **目标端口范围**：键入 80。
   - **协议**：选择“TCP”。 
   - **操作**：选择“允许”。  
   - **优先级**：键入 100。 
   - **名称**：键入“MyHTTPRule”。 
   - **说明**：键入“允许 HTTP”。 
   
1. 选择 **添加** 。 
   
   ![创建 NSG 规则](./media/tutorial-load-balancer-port-forwarding-portal/8-load-balancer-nsg-rules.png)
   
## <a name="create-load-balancer-resources"></a>创建负载均衡器资源

在本节，检查负载均衡器后端池，并配置负载均衡器运行状况探测和流量规则。

### <a name="view-the-back-end-address-pool"></a>查看后端地址池

为了将流量分发到 VM，负载均衡器使用后端地址池，其中包含连接到负载均衡器的虚拟网络接口 (NIC) 的 IP 地址。 

创建负载均衡器后端池，并在创建 VM 后将 VM 添加到其中。 还可以创建后端池，并从负载均衡器“后端池”页面添加或删除 VM。 

1. 在左侧菜单中选择“所有资源”，然后在资源列表中选择“MyLoadBalancer”。
   
1. 在“设置”下，选择“后端池”。
   
1. 在“后端池”页上展开 **MyBackendPool**，确保 **VM1** 和 **VM2** 都已列出。

1. 选择“MyBackendPool”。 
   
   在“MyBackendPool”页面上的“虚拟机”和“IP 地址”下，可在池中添加或删除可用的 VM。

可以通过在“后端池”页面上选择“添加”来创建新的后端池。

### <a name="create-a-health-probe"></a>创建运行状况探测器

若要允许负载均衡器监视 VM 状态，请使用运行状况探测。 运行状况探测器基于其对运行状况检查的响应，从负载均衡器中动态添加或删除 VM。 

1. 在左侧菜单中选择“所有资源”，然后在资源列表中选择“MyLoadBalancer”。
   
1. 在“设置”下，依次选择“运行状况探测”、“添加”。
   
1. 在“添加运行状况探测”页上，键入或选择以下值：
   
   - **名称**：键入 *MyHealthProbe*。
   - **协议**：下拉并选择“HTTP”。 
   - **端口**：键入 *80*。 
   - **路径**：接受 */* 作为默认 URI。 可以将此值替换为任何其他的 URI。 
   - **时间间隔**：键入 *15*。 时间间隔是两次探测尝试之间的秒数。
   - **不正常阈值**：键入 *2*。 此值是将 VM 视为不正常之前发生的连续探测失败次数。
   
1. 选择“确定”。
   
   ![添加探测](./media/tutorial-load-balancer-port-forwarding-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则定义如何将流量分配给 VM。 此规则定义传入流量的前端 IP 配置、用于接收流量的后端 IP 池，以及所需的源和目标端口。 

名为 **MyLoadBalancerRule** 的负载均衡器规则在前端 **LoadBalancerFrontEnd** 中侦听端口 80。 该规则也在端口 80 上将网络流量发送到后端地址池 **MyBackendPool**。 

1. 在左侧菜单中选择“所有资源”，然后在资源列表中选择“MyLoadBalancer”。
   
1. 在“设置”下，依次选择“负载均衡规则”、“添加”。
   
1. 在“添加负载均衡规则”页上，键入或选择以下值：
   
   - **名称**：键入“MyLoadBalancerRule”。
   - **协议**：选择“TCP”。
   - **端口**：键入 *80*。
   - **后端端口**：键入 *80*。
   - **后端池**：选择“MyBackendPool”。
   - **运行状况探测**：选择“MyHealthProbe”。 
   
1. 选择“确定”。
   
  ![添加负载均衡器规则](./media/tutorial-load-balancer-port-forwarding-portal/5-load-balancing-rules.png)

## <a name="create-an-inbound-nat-port-forwarding-rule"></a>创建入站 NAT 端口转发规则

创建负载均衡器入站网络地址转换 (NAT) 规则，以将流量从前端 IP 地址的特定端口转发到后端 VM 的特定端口。

1. 在左侧菜单中选择“所有资源”，然后在资源列表中选择“MyLoadBalancer”。
   
1. 在“设置”下，依次选择“入站 NAT 规则”和“添加”。 
   
1. 在“添加入站 NAT 规则”页上，键入或选择以下值：
   
   - **名称**：键入“MyNATRuleVM1”。
   - **端口**：键入 4221。
   - **目标虚拟机**：从下拉列表中选择“MyVM1”。
   - **端口映射**：选择“自定义”。
   - **目标端口**：键入 3389。
   
1. 选择“确定”。
   
1. 重复步骤，使用“端口”添加名为 MyNATRuleVM2 的入站 NAT 规则：4222 和**目标虚拟机**：MyVM2。

## <a name="test-the-load-balancer"></a>测试负载均衡器

在本节中，将在后端服务器上安装 Internet Information Services (IIS)，并自定义默认网页以显示计算机名称。 然后，将使用负载均衡器的公共 IP 地址测试负载均衡器。 

每个后端 VM 提供不同版本的默认 IIS 网页，因此可以看到负载均衡器在两个 VM 之间分发请求。

### <a name="connect-to-the-vms-with-rdp"></a>通过 RDP 连接到 VM

使用远程桌面 (RDP) 连接到每个 VM。 

1. 在门户的左侧菜单中选择“所有资源”。 在资源列表中，选择“MyResourceGroupLB”资源组中的每个 VM。
   
1. 在“概览”页上选择“连接”，然后选择“下载 RDP 文件”。 
   
1. 打开下载的 RDP 文件，然后选择“连接”。
   
1. 在“Windows 安全性”屏幕上选择“更多选择”，然后选择“使用其他帐户”。 
   
   输入用户名 *azureuser* 和密码 *Azure1234567*，然后选择“确定”。
   
1. 使用“是”对任何证书请求进行响应。 
   
   VM 桌面会在新窗口中打开。 

### <a name="install-iis-and-replace-the-default-iis-web-page"></a>安装 IIS 并替换默认 IIS 网页 

使用 PowerShell 安装 IIS，并将默认 IIS 网页替换为显示 VM 名称的页面。

1. 在 MyVM1 和 MyVM2 上，通过“开始”菜单启动 **Windows PowerShell**。 

2. 运行以下命令，以便安装 IIS 并替换默认的 IIS 网页：
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    
   ```
   
1. 选择“断开连接”，关闭与 MyVM1 和 MyVM2 的 RDP 连接。 请勿关闭 VM。

### <a name="test-load-balancing"></a>测试负载均衡

1. 在门户的“MyLoadBalancer”的“概览”页上，复制“公共 IP 地址”下的公共 IP 地址。 将鼠标悬停在地址上，选择“复制”图标对其进行复制。 在此示例中，它是 40.67.218.235。 
   
1. 将负载均衡器的公共 IP 地址 (40.67.218.235) 粘贴或键入到浏览器的地址栏中。 
   
   自定义的 IIS Web 服务器默认页会显示在浏览器中。 会显示 **Hello World from MyVM1** 或 **Hello World from MyVM2** 消息。
   
   ![新建 IIS 默认页](./media/tutorial-load-balancer-port-forwarding-portal/9-load-balancer-test.png) 
   
1. 刷新浏览器，可以看到负载均衡器在 VM 之间分配流量。 当负载均衡器将请求分配到每个后端 VM 时，有时会显示 **MyVM1** 页，有时会显示 **MyVM2** 页。
   
   >[!NOTE]
   >可能需要在两次尝试之间清除浏览器缓存或打开新的浏览器窗口。

## <a name="test-port-forwarding"></a>测试端口转发

通过端口转发，可以使用负载均衡器的 IP 地址和 NAT 规则中定义的前端端口值将桌面远程发送到后端 VM。 

1. 在门户的“MyLoadBalancer”的“概览”页上，复制公共 IP 地址。 将鼠标悬停在地址上，选择“复制”图标对其进行复制。 在此示例中，它是 40.67.218.235。 
   
1. 打开命令提示符，运用负载均衡器的公共 IP 地址和在 VM 的 NAT 规则中定义的前端端口，使用下面的命令用 MyVM2 创建远程桌面会话。 
   
   ```
   mstsc /v:40.67.218.235:4222
   ```
  
1. 打开下载的 RDP 文件，然后选择“连接”。
   
1. 在“Windows 安全性”屏幕上选择“更多选择”，然后选择“使用其他帐户”。 
   
   输入用户名 *azureuser* 和密码 *Azure1234567*，然后选择“确定”。
   
1. 使用“是”对任何证书请求进行响应。 
   
   MyVM2 桌面会在新窗口中打开。 

RDP 连接成功，因为入站 NAT 规则“MyNATRuleVM2”将流量从负载均衡器的前端端口 4222 定向到 MyVM2 的端口 3389（RDP 端口）。

## <a name="clean-up-resources"></a>清理资源

若要在不再需要的情况下删除负载均衡器和所有相关的资源，请打开 **MyResourceGroupLB** 资源组，然后选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

在本教程中，你创建了一个标准公共负载均衡器。 你创建并配置了网络资源、后端服务器、运行状况探测，以及适用于负载均衡器的规则。 你在后端 VM 上安装了 IIS，并使用了负载均衡器的公共 IP 地址来测试负载均衡器。 你设置并测试了端口转发，即从负载均衡器上的指定端口转发到后端 VM 上的端口。 

要了解有关 Azure 负载均衡器的详细信息，请继续学习其他负载均衡器教程。

> [!div class="nextstepaction"]
> [Azure 负载均衡器教程](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
