---
title: 教程：使用 Azure 防火墙管理器保护虚拟中心
description: 本教程介绍如何在 Azure 门户中使用 Azure 防火墙管理器保护虚拟中心。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 07/29/2020
ms.author: victorh
ms.openlocfilehash: 458ebe14e77c7b190a5c4cdd9b408396589d5d27
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420815"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-firewall-manager"></a>教程：使用 Azure 防火墙管理器保护虚拟中心

使用 Azure 防火墙管理器，可以创建安全虚拟中心来保护发往专用 IP 地址、Azure PaaS 和 Internet 的云网络流量。 到防火墙的流量路由是自动的，因此无需创建用户定义的路由 (UDR)。

![保护云网络](media/secure-cloud-network/secure-cloud-network.png)

防火墙管理器还支持中心虚拟网络体系结构。 有关安全虚拟中心和中心虚拟网络体系结构类型的比较，请参阅[有哪些 Azure 防火墙管理器体系结构选项？](vhubs-and-vnets.md)

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建分支虚拟网络
> * 创建安全虚拟中心
> * 连接中心和辐射型虚拟网络
> * 将流量路由到中心
> * 部署服务器
> * 创建防火墙策略并保护中心
> * 测试防火墙

## <a name="create-a-hub-and-spoke-architecture"></a>创建中心和辐射体系结构

首先，创建一个可放置服务器的辐射型虚拟网络。

### <a name="create-two-spoke-virtual-networks-and-subnets"></a>创建两个辐射型虚拟网络和子网

两个虚拟网络将各自具有工作负载服务器，并且都受防火墙保护。

1. 在 Azure 门户主页上，选择“创建资源”。
2. 在“网络”下，选择“虚拟网络”。
2. 对于“订阅”，请选择自己的订阅。
1. 对于“资源组”，选择“新建”，键入“fw-manager”作为名称，然后选择“确定”   。
2. 对于“名称”，请键入“Spoke-01”。
3. 对于“区域”，请选择“(US)美国东部”。
4. 在完成时选择“下一步:IP 地址”。
1. 对于“地址空间”，请键入“10.1.0.0/16” 。
3. 选择“添加子网”。
4. 键入“Workload-01-SN”。
5. 键入“10.1.1.0/24”作为“子网地址范围” 。
6. 选择 **添加** 。
1. 选择“查看 + 创建”  。
2. 选择“创建” 。

重复此过程以创建另一个类似的虚拟网络：

姓名：**Spoke-02**<br>
地址空间：**10.2.0.0/16**<br>
子网名称：**Workload-02-SN**<br>
子网地址范围：**10.2.1.0/24**

### <a name="create-the-secured-virtual-hub"></a>创建安全虚拟中心

使用防火墙管理器创建安全虚拟中心。

1. 在 Azure 门户主页上，选择“所有服务”。
2. 在搜索框中，键入“防火墙管理器”并选择“防火墙管理器”。
3. 在“防火墙管理器”页上，选择“查看安全虚拟中心”。
4. 在“防火墙管理器 | 安全虚拟中心”页上，选择“新建安全虚拟中心”。
5. 对于“资源组”，请选择“fw-manager” 。
7. 对于“区域”，请选择“美国东部”。
1. 对于“安全虚拟中心名称”，键入“Hub-01”。
2. 对于“中心地址空间”，请键入“10.0.0.0/16”。
3. 对于新的 vWAN 名称，请键入“Vwan-01”。
4. 使“包含 VPN 网关以启用受信任的安全合作伙伴”复选框处于清除状态。
5. 在完成时选择“下一步:Azure 防火墙”。
6. 接受默认的“Azure 防火墙”的“启用”设置，然后选择“下一步:受信任的安全合作伙伴”。
7. 接受默认的“受信任的安全合作伙伴”的“禁用”设置，然后选择“下一步:查看 + 创建”。
8. 选择“创建”  。 这将耗时大约 30 分钟进行部署。

现在你可以获取防火墙的公共 IP 地址。

1. 部署完成后，在 Azure 门户中选择“所有服务”。
1. 键入“防火墙管理器”并选择“防火墙管理器” 。
2. 选择“安全虚拟中心”。
3. 选择“hub-01”。
7. 选择“公共 IP 配置”。
8. 记下公共 IP 地址以供稍后使用。

### <a name="connect-the-hub-and-spoke-virtual-networks"></a>连接中心和辐射型虚拟网络

现在，你可以将中心和辐射型虚拟网络对等互连。

1. 选择“fw-manager”资源组，然后选择“Vwan-01”虚拟 WAN。
2. 在“连接”下，选择“虚拟网络连接”。
3. 选择“添加连接”。
4. 对于“连接名称”，键入“hub-spoke-01”。
5. 对于“中心”，选择“Hub-01”。
6. 对于“资源组”，请选择“fw-manager” 。
7. 对于“虚拟网络”，选择“Spoke-01”。
8. 选择“创建”。

重复此步骤以连接“Spoke-02”虚拟网络：连接名称 -“hub-spoke-02” 

### <a name="configure-the-hub-and-spoke-routing"></a>配置中心路由和辐射路由

从 Azure 门户打开 Cloud Shell 并运行以下 Azure PowerShell，以配置所需的中心路由和辐射路由。 对等互连辐射/分支连接必须将传播设置为“无”。 这会阻止辐射之间的任意位置到任意位置通信，而是使用默认路由将流量路由到防火墙。

```azurepowershell
$noneRouteTable = Get-AzVHubRouteTable -ResourceGroupName fw-manager `
                  -HubName hub-01 -Name noneRouteTable
$vnetConns = Get-AzVirtualHubVnetConnection -ResourceGroupName fw-manager `
             -ParentResourceName hub-01

$vnetConn = $vnetConns[0]
$vnetConn.RoutingConfiguration.PropagatedRouteTables.Ids = @($noneRouteTable)
$vnetConn.RoutingConfiguration.PropagatedRouteTables.Labels = @("none")
Update-AzVirtualHubVnetConnection -ResourceGroupName fw-manager `
   -ParentResourceName hub-01 -Name $vnetConn.Name `
   -RoutingConfiguration $vnetConn.RoutingConfiguration

$vnetConn = $vnetConns[1]
$vnetConn.RoutingConfiguration.PropagatedRouteTables.Ids = @($noneRouteTable)
$vnetConn.RoutingConfiguration.PropagatedRouteTables.Labels = @("none")
Update-AzVirtualHubVnetConnection -ResourceGroupName fw-manager `
   -ParentResourceName hub-01 -Name $vnetConn.Name -RoutingConfiguration $vnetConn.RoutingConfiguration
```

## <a name="deploy-the-servers"></a>部署服务器

1. 在 Azure 门户中，选择“创建资源”。
2. 在“常用”列表中选择“Windows Server 2016 Datacenter” 。
3. 输入虚拟机的以下值：

   |设置  |值  |
   |---------|---------|
   |资源组     |**fw-manager**|
   |虚拟机名称     |**Srv-workload-01**|
   |区域     |**(美国)美国东部**|
   |管理员用户名     |键入用户名|
   |密码     |键入密码|

4. 在“入站端口规则”下，对于“公共入站端口”，选择“无”  。
6. 接受其他默认值，然后选择“下一步:**磁盘”** 。
7. 接受磁盘默认值，然后选择“下一步:网络”。
8. 选择“Spoke-01”作为虚拟网络，并选择“Workload-01-SN”作为子网 。
9. 对于“公共 IP”，请选择“无”。
11. 接受其他默认值，然后选择“下一步:**管理”** 。
12. 选择“关闭”以禁用启动诊断。 接受其他默认值，然后选择“查看 + 创建”。
13. 检查摘要页上的设置，然后选择“创建”。

使用下表中的信息配置名为 Srv-Workload-02 的另一台虚拟机。 剩余的配置与 Srv-workload-01 虚拟机相同。

|设置  |值  |
|---------|---------|
|虚拟网络|**Spoke-02**|
|子网|**Workload-02-SN**|

部署服务器后，选择服务器资源，并记下“网络”中每个服务器的专用 IP 地址。

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>创建防火墙策略并保护中心

防火墙策略定义规则集合，以在一个或多个安全虚拟中心上定向流量。 你将创建防火墙策略，然后保护你的中心。

1. 从防火墙管理器中，选择“查看 Azure 防火墙策略”。
2. 选择“创建 Azure 防火墙策略”。
3. 在“策略详细信息”下，针对“名称”键入“Policy-01”并针对“区域”选择“美国东部”。
4. 在完成时选择“下一步:DNS 设置(预览)”。
1. 在完成时选择“下一步:规则”。
2. 在“规则”选项卡上，选择“添加规则集合”。
3. 在“添加规则集合”页上，键入“App-RC-01”作为“名称”。
4. 对于“规则集合类型”，选择“应用程序”。
5. 对于“优先级”，请键入 **100**。
6. 确保“规则集合操作”设置为“允许”。
7. 对于规则的“名称”，键入“Allow-msft”。
8. 对于“源类型”，请选择“IP 地址” 。
9. 对于“源”，请键入 \* 。
10. 对于“协议”，键入“http,https”。
11. 确保“目标类型”是“FQDN” 。
12. 对于“目标”，键入“.microsoft.com” **\*** 。
13. 选择 **添加** 。

添加 DNAT 规则，以便可以将远程桌面连接到 Srv-Workload-01 虚拟机。

1. 选择“添加规则集合”。
2. 对于“名称”，请键入“DNAT-rdp” 。
3. 对于“规则集合类型”，请选择“DNAT” 。
4. 对于“优先级”，请键入 **100**。
5. 对于规则的“名称”，键入“Allow-rdp”。
6. 对于“源类型”，请选择“IP 地址” 。
7. 对于“源”，请键入 \* 。
8. 对于“协议”，请选择“TCP”。 
9. 对于“目标端口”，请键入 **3389**。
10. 对于“目标类型”，请选择“IP 地址”。 
11. 对于“目标”，键入之前记下的防火墙公共 IP 地址。
12. 对于“转换的地址”，键入之前记下的 Srv-Workload-01 的专用 IP 地址 。
13. 对于“已翻译的端口”  ，键入 **3389**。
14. 选择 **添加** 。

添加网络规则，以便可以将远程桌面从 Srv-Workload-01 连接到 Srv-Workload-02 。

1. 选择“添加规则集合”。
2. 对于“名称”，键入“vnet-rdp” 。
3. 对于“规则集合类型”，请选择“网络”。
4. 对于“优先级”，请键入 **100**。
5. 对于规则的“名称”，键入“Allow-vnet”。
6. 对于“源类型”，请选择“IP 地址” 。
7. 对于“源”，请键入 \* 。
8. 对于“协议”，请选择“TCP”。 
9. 对于“目标端口”，请键入 **3389**。
9. 对于“目标类型”，请选择“IP 地址”。 
10. 对于“目标”，键入之前记下的 Srv-Workload-02 专用 IP 地址 。
11. 选择 **添加** 。
1. 在完成时选择“下一步:**威胁智能”** 。
2. 在完成时选择“下一步:中心”。
3. 在“中心”选项卡上，选择“关联虚拟中心”。
4. 选择“Hub-01”，然后选择“添加”。
5. 选择“查看 + 创建”。
6. 选择“创建”。

这可能需要 5 分钟或更长时间才能完成。

## <a name="route-traffic-to-your-hub"></a>将流量路由到中心

现在必须确保通过防火墙路由网络流量。

1. 从防火墙管理器中，选择“安全虚拟中心”。
2. 选择“Hub-01”。
3. 在“设置”下，选择“安全配置” 。
4. 在“Internet 流量”下，选择“Azure 防火墙” 。
5. 在“专用流量”下，选择“通过 Azure 防火墙发送” 。
10. 验证“hub-spoke”连接是否显示“Internet 流量”的状态为“安全”。
11. 选择“保存”。


## <a name="test-your-firewall"></a>测试防火墙

若要测试防火墙规则，需使用防火墙公共 IP 地址（该地址已 NAT 到 Srv-Workload-01）连接远程桌面。 在这里，你将使用浏览器测试应用程序规则并将远程桌面连接到 Srv-Workload-02 来测试网络规则。

### <a name="test-the-application-rule"></a>测试应用程序规则

现在，测试防火墙以确认它可按预期工作。

1. 将远程桌面连接到防火墙公共 IP 地址，然后进行登录。

3. 打开 Internet Explorer 并浏览到 https://www.microsoft.com 。
4. 出现 Internet Explorer 安全警报时，请选择“确定” > “关闭”。

   应会看到 Microsoft 主页。

5. 浏览到 https://www.google.com 。

   防火墙应会阻止你访问。

现已验证防火墙应用程序规则可正常工作：

* 可以浏览到一个允许的 FQDN，但不能浏览到其他任何 FQDN。

### <a name="test-the-network-rule"></a>测试网络规则

现在测试网络规则。

- 打开 Srv-Workload-02 专用 IP 地址的远程桌面。

   远程桌面应连接到 Srv-Workload-02。

现已验证防火墙网络规则可正常工作：
* 你可以将远程桌面连接到另一个虚拟网络中的服务器。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解受信任的安全合作伙伴](trusted-security-partners.md)
