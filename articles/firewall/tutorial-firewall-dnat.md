---
title: 使用 Azure 门户通过 Azure 防火墙 DNAT 筛选入站流量
description: 本教程介绍如何使用 Azure 门户部署和配置 Azure 防火墙 DNAT。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 766ad04251fbe404d43734115e41e23ae0a4be28
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982024"
---
# <a name="tutorial-filter-inbound-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>教程：使用 Azure 门户通过 Azure 防火墙 DNAT 筛选入站流量

可以对 Azure 防火墙目标网络地址转换 (DNAT) 进行配置，以便转换和筛选到子网的入站流量。 Azure 防火墙没有入站规则和出站规则的概念。 有应用程序规则和网络规则，它们适用于进入防火墙的任何流量。 首先应用网络规则，然后应用应用程序规则，规则将终止。

>[!NOTE]
>防火墙 DNAT 功能目前仅在 Azure PowerShell 和 REST 中可用。

例如，如果匹配网络规则，则不会按应用程序规则评估数据包。 如果没有网络规则匹配项，并且数据包协议是 HTTP/HTTPS，则不会按应用程序规则评估数据包。 如果仍未找到匹配项，则会根据[基础结构规则集合](infrastructure-fqdns.md)评估数据包。 如果仍然没有匹配项，则默认情况下会拒绝该数据包。

配置 DNAT 时，NAT 规则收集操作设置为“目标网络地址转换(DNAT)”。 防火墙公共 IP 和端口转换为专用 IP 地址和端口。 然后按正常情况应用规则，首先应用网络规则，然后应用应用程序规则。 例如，可以将网络规则配置为在 TCP 端口 3389 上允许远程桌面流量。 先进行地址转换，然后使用转换的地址应用网络和应用程序规则。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置测试网络环境
> * 部署防火墙
> * 创建默认路由
> * 配置 DNAT 规则
> * 配置网络规则
> * 测试防火墙

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

请为本教程创建两个对等互连的 VNet：
- **VN-Hub** - 防火墙在此 VNet 中。
- **VN-Spoke** - 工作负荷服务器在此 VNet 中。

## <a name="create-a-resource-group"></a>创建资源组
1. 在 [http://portal.azure.com](http://portal.azure.com) 中登录 Azure 门户。
1. 在 Azure 门户主页上，依次单击“资源组”、“添加”。
2. 对于“资源组名称”，请键入 **RG-DNAT-Test**。
3. 对于“订阅”，请选择自己的订阅。
4. 对于“资源组位置”，请选择一个位置。 创建的所有后续资源必须位于同一位置。
5. 单击“创建”。

## <a name="set-up-the-network-environment"></a>设置网络环境
首先创建 VNet，然后将其对等互连。

### <a name="create-the-hub-vnet"></a>创建中心 VNet
1. 在 Azure 门户主页中，单击“所有服务”。
2. 在“网络”下，单击“虚拟网络”。
3. 单击 **“添加”**。
4. 对于“名称”，请键入“VN-Hub”。
5. 对于“地址空间”，请键入 **10.0.0.0/16**。
7. 对于“订阅”，请选择自己的订阅。
8. 对于“资源组”，请选择“使用现有”，然后选择“RG-DNAT-Test”。
9. 对于“位置”，请选择前面使用的同一位置。
10. 在“子网”下，为“名称”键入 **AzureFirewallSubnet**。

     防火墙将位于此子网中，子网名称**必须**是 AzureFirewallSubnet。
     > [!NOTE]
     > AzureFirewallSubnet 子网的最小大小为 /25。
11. 对于“地址范围”，请键入 **10.0.1.0/24**。
12. 使用其他默认设置，然后单击“创建”。

### <a name="create-a-spoke-vnet"></a>创建辐射 VNet

1. 在 Azure 门户主页中，单击“所有服务”。
2. 在“网络”下，单击“虚拟网络”。
3. 单击 **“添加”**。
4. 对于“名称”，请键入 **VN-Spoke**。
5. 对于“地址空间”，请键入 **192.168.0.0/16**。
7. 对于“订阅”，请选择自己的订阅。
8. 对于“资源组”，请选择“使用现有”，然后选择“RG-DNAT-Test”。
9. 对于“位置”，请选择前面使用的同一位置。
10. 在“子网”下，为“名称”键入 **SN-Workload**。

    服务器会在此子网中。
1. 对于“地址范围”，请键入 **192.168.1.0/24**。
2. 使用其他默认设置，然后单击“创建”。

### <a name="peer-the-vnets"></a>将 VNet 对等互连

现在，请将这两个 VNet 对等互连。

#### <a name="hub-to-spoke"></a>中心到分支

1. 单击“VN-Hub”虚拟网络。
2. 在“设置”下，单击“对等互连”。
3. 单击 **“添加”**。
4. 键入 **Peer-HubSpoke** 作为名称。
5. 选择 **VN-Spoke** 作为虚拟网络。
7. 单击“确定”。

#### <a name="spoke-to-hub"></a>分支到中心

1. 单击“VN-Spoke”虚拟网络。
2. 在“设置”下，单击“对等互连”。
3. 单击 **“添加”**。
4. 键入 **Peer-SpokeHub** 作为名称。
5. 选择 **VN-Hub** 作为虚拟网络。
6. 单击“允许转发的流量”。
7. 单击“确定”。

## <a name="create-a-virtual-machine"></a>创建虚拟机

创建一个工作负荷虚拟机，将其置于 **SN-Workload** 子网中。

1. 在 Azure 门户主页中，单击“所有服务”。
2. 在“计算”下，单击“虚拟机”。
3. 依次单击“添加”、“Windows Server”、“Windows Server 2016 Datacenter”、“创建”。

**基础知识**

1. 对于“名称”，请键入 **Srv-Workload**。
5. 键入用户名和密码。
6. 对于“订阅”，请选择自己的订阅。
7. 对于“资源组”，请单击“使用现有”，然后选择“RG-DNAT-Test”。
8. 对于“位置”，请选择前面使用的同一位置。
9. 单击“确定”。

**大小**

1. 为运行 Windows Server 的测试虚拟机选择适当的大小。 例如，**B2ms**（8 GB RAM，16 GB 存储空间）。
2. 单击“选择”。

**设置**

1. 在“网络”下，选择“VN-Spoke”作为“虚拟网络”。
2. 对于“子网”，请选择“SN-Workload”。
3. 单击“公共 IP 地址”，然后单击“无”。
4. 对于“选择公共入站端口”，请选择“无公共入站端口”。 
2. 保留其他默认设置，然后单击“确定”。

**摘要**

查看摘要，然后单击“创建”。 完成此设置可能需要几分钟时间。

部署完成后，请记下虚拟机的专用 IP 地址。 该地址会在稍后配置防火墙时使用。 单击虚拟机名称，然后在“设置”下单击“网络”，找到专用 IP 地址。


## <a name="deploy-the-firewall"></a>部署防火墙

1. 在门户主页中，单击“创建资源”。
2. 单击“网络”，然后在“特色”后面单击“查看所有”。
3. 依次单击“防火墙”、“创建”。 
4. 在“创建防火墙”页上，使用下表配置防火墙：
   
   |设置  |值  |
   |---------|---------|
   |名称     |FW-DNAT-test|
   |订阅     |\<用户的订阅\>|
   |资源组     |**使用现有**：RG-DNAT-Test |
   |位置     |选择前面使用的同一位置|
   |选择虚拟网络     |**使用现有**：VN-Hub|
   |公共 IP 地址     |**新建**。 公共 IP 地址必须为“标准 SKU”类型。|

2. 单击“查看 + 创建”。
3. 查看摘要，然后单击“创建”以创建防火墙。

   需要花费几分钟时间来完成部署。
4. 部署完成后，转到“RG-DNAT-Test”资源组，然后单击“FW-DNAT-test”防火墙。
6. 记下专用 IP 地址。 稍后在创建默认路由时需要用到此地址。


## <a name="create-a-default-route"></a>创建默认路由

对于“SN-Workload”子网，请配置要通过防火墙的出站默认路由。

1. 在 Azure 门户主页中，单击“所有服务”。
2. 在“网络”下，单击“路由表”。
3. 单击 **“添加”**。
4. 对于“名称”，请键入“RT-FWroute”。
5. 对于“订阅”，请选择自己的订阅。
6. 对于“资源组”，请选择“使用现有”，然后选择“RG-DNAT-Test”。
7. 对于“位置”，请选择前面使用的同一位置。
8. 单击“创建”。
9. 单击“刷新”，然后单击“RT-FWroute”路由表。
10. 依次单击“子网”、“关联”。
11. 单击“虚拟网络”，然后选择“VN-Spoke”。
12. 对于“子网”，请单击“SN-Workload”。
13. 单击“确定”。
14. 依次单击“路由”、“添加”。
15. 对于“路由名称”，请键入 **FW-DG**。
16. 对于“地址前缀”，请键入 **0.0.0.0/0**。
17. 对于“下一跃点类型”，请选择“虚拟设备”。

    Azure 防火墙实际上是一个托管服务，但虚拟设备可在此场合下正常工作。
1. 对于“下一跃点地址”，请键入前面记下的防火墙专用 IP 地址。
2. 单击“确定”。


## <a name="configure-a-dnat-rule"></a>配置 DNAT 规则

```azurepowershell-interactive
 $rgName  = "RG-DNAT-Test"
 $firewallName = "FW-DNAT-test"
 $publicip = type the Firewall public ip
 $newAddress = type the private IP address for the Srv-Workload virtual machine 
 
# Get Firewall
    $firewall = Get-AzureRmFirewall -ResourceGroupName $rgName -Name $firewallName
  # Create NAT rule
    $natRule = New-AzureRmFirewallNatRule -Name RL-01 -SourceAddress * -DestinationAddress $publicip -DestinationPort 3389 -Protocol TCP -TranslatedAddress $newAddress -TranslatedPort 3389
  # Create NAT rule collection
    $natRuleCollection = New-AzureRmFirewallNatRuleCollection -Name RC-DNAT-01 -Priority 200 -Rule $natRule
  # Add NAT Rule collection to firewall:
    $firewall.AddNatRuleCollection($natRuleCollection)
  # Save:
    $firewall | Set-AzureRmFirewall
```
## <a name="configure-a-network-rule"></a>配置网络规则

1. 打开“RG-DNAT-Test”，然后单击“FW-DNAT-test”防火墙。
1. 在“FW-DNAT-test”页的“设置”下，单击“规则”。
2. 单击“添加网络规则集合”。

使用下表配置规则，然后单击“添加”：


|参数  |值  |
|---------|---------|
|名称     |**RC-Net-01**|
|Priority     |**200**|
|操作     |**Allow**|

在“规则”下：

|参数  |设置  |
|---------|---------|
|名称     |**RL-RDP**|
|协议     |**TCP**|
|源地址     |*|
|目标地址     |**Srv-Workload** 专用 IP 地址|
|目标端口|**3389**|


## <a name="test-the-firewall"></a>测试防火墙

1. 将远程桌面连接到防火墙公共 IP 地址。 此时会连接到 **Srv-Workload** 虚拟机。
3. 关闭远程桌面。
4. 将 **RC-Net-01** 网络规则集合操作更改为“拒绝”。
5. 尝试再次连接到防火墙公共 IP 地址。 这一次它不会成功，因此存在“拒绝”规则。

## <a name="clean-up-resources"></a>清理资源

可以将防火墙资源保留到下一教程使用。不再需要时，请删除 **RG-DNAT-Test** 资源组，以删除与防火墙相关的所有资源。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 设置测试网络环境
> * 部署防火墙
> * 创建默认路由
> * 配置 DNAT 规则
> * 配置网络规则
> * 测试防火墙

接下来，可以监视 Azure 防火墙日志。

> [!div class="nextstepaction"]
> [教程：监视 Azure 防火墙日志](./tutorial-diagnostics.md)
