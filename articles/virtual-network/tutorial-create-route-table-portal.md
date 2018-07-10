---
title: 路由网络流量 - 教程 - Azure 门户 | Microsoft Docs
description: 本教程介绍如何使用 Azure 门户通过路由表路由网络流量。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 81478ace72a538f4970e114cd704fd64ceb94aa6
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344881"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>教程：使用 Azure 门户通过路由表路由网络流量

默认情况下，Azure 自动在虚拟网络中的所有子网之间路由流量。 可以创建自己的路由来覆盖 Azure 的默认路由。 创建自定义路由的功能非常有用，例如，可以通过网络虚拟设备 (NVA) 在子网之间路由流量。 本教程介绍如何执行以下操作：

> [!div class="checklist"]
> * 创建路由表
> * 创建路由
> * 创建包含多个子网的虚拟网络
> * 将路由表关联到子网
> * 创建用于流量路由的 NVA
> * 将虚拟机 (VM) 部署到不同子网
> * 通过 NVA 将从一个子网的流量路由到另一个子网

如果你愿意，可以使用 [Azure CLI](tutorial-create-route-table-cli.md) 或 [Azure PowerShell](tutorial-create-route-table-powershell.md) 完成本教程中的步骤。

如果没有 Azure 订阅，请在开始之前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="log-in-to-azure"></a>登录 Azure 

通过 http://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-route-table"></a>创建路由表

1. 选择 Azure 门户左上角的“+ 创建资源”。
2. 依次选择“网络”、“路由表”。
3. 输入或选择以下信息，接受剩下的默认设置，然后选择“创建”：

    |设置|值|
    |---|---|
    |名称|myRouteTablePublic|
    |订阅| 选择订阅。|
    |资源组 | 选择“新建”，并输入 myResourceGroup|
    |位置|美国东部|
 
    ![创建路由表](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>创建路由

1. 在门户顶部的“搜索资源、服务和文档”框中，开始键入*myRouteTablePublic*。 当“myRouteTablePublic”出现在搜索结果中时，请选择它。
2. 在“设置”下选择“路由”，然后选择“+ 添加”，如下图所示：

    ![添加路由](./media/tutorial-create-route-table-portal/add-route.png) 
 
3. 在“添加路由”下输入或选择以下信息，接受剩下的默认设置，然后选择“创建”：

    |设置|值|
    |---|---|
    |路由名称|ToPrivateSubnet|
    |地址前缀| 10.0.1.0/24|
    |下一跃点类型 | 选择“虚拟设备”。|
    |下一跃点地址| 10.0.2.4|

## <a name="associate-a-route-table-to-a-subnet"></a>将路由表关联到子网

将路由表关联到子网之前，必须先创建虚拟网络和子网，然后才能将路由表关联到子网：

1. 选择 Azure 门户左上角的“+ 创建资源”。
2. 选择“网络”，然后选择“虚拟网络”。
3. 在“创建虚拟网络”下输入或选择以下信息，接受剩下的默认设置，然后选择“创建”：

    |设置|值|
    |---|---|
    |名称|myVirtualNetwork|
    |地址空间| 10.0.0.0/16|
    |订阅 | 选择订阅。|
    |资源组|选择“使用现有”，然后选择“myResourceGroup”。|
    |位置|选择“美国东部”|
    |子网名称|公共|
    |地址范围|10.0.0.0/24|
    
4. 在门户顶部的“搜索资源、服务和文档”框中，开始键入“myVirtualNetwork”。 当“myVirtualNetwork”出现在搜索结果中时，将其选中。
5. 在“设置”下面选择“子网”，然后选择“+ 子网”，如下图中所示：

    ![添加子网](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. 选择或输入以下信息，然后选择“确定”：

    |设置|值|
    |---|---|
    |名称|专用|
    |地址空间| 10.0.1.0/24|

7. 再次完成步骤 5 和 6，并提供以下信息：

    |设置|值|
    |---|---|
    |名称|外围网络|
    |地址空间| 10.0.2.0/24|

8. 完成上述步骤后，会显示“myVirtualNetwork - 子网”框。 在“设置”下面选择“子网”，然后选择“Public”。
9. 如下图所示，依次选择“路由表”、“MyRouteTablePublic”、“保存”：

    ![关联路由表](./media/tutorial-create-route-table-portal/associate-route-table.png) 

## <a name="create-an-nva"></a>创建 NVA

NVA 是执行网络功能（如路由、防火墙或 WAN 优化）的 VM。

1. 选择 Azure 门户左上角的“+ 创建资源”。
2. 选择“计算”，然后选择“Windows Server 2016 Datacenter”。 可以选择不同的操作系统，但剩余步骤假定你选择了“Windows Server 2016 Datacenter”。 
3. 对于“基本信息”，选择或输入以下信息，然后选择“确定”：

    |设置|值|
    |---|---|
    |名称|myVmNva|
    |用户名|输入所选用户名。|
    |密码|输入所选密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    |资源组| 选择“使用现有”，然后选择“myResourceGroup”。|
    |位置|选择“美国东部”。|
4. 在“选择大小”下选择 VM 大小。
5. 对于“设置”，选择或输入以下信息，然后选择“确定”：

    |设置|值|
    |---|---|
    |虚拟网络|myVirtualNetwork - 选择“虚拟网络”，然后在“选择虚拟网络”下选择“myVirtualNetwork”。|
    |子网|选择“子网”，然后在“选择子网”下面选择“外围网络”。 |
    |公共 IP 地址| 选择“公共 IP 地址”，然后在“选择公共 IP 地址”下面选择“无”。 未向此 VM 分配公共 IP 地址，因为无法从 Internet 连接到此 VM。
6. 在“摘要”中的“创建”下，选择“创建”以启动 VM 部署。

    创建 VM 需要几分钟时间。 在 Azure 完成创建 VM 并返回有关 VM 的信息之前，请不要继续下一步。

7. 在创建 VM 后打开的框中的“设置”下，选择“网络”，然后选择“myvmnva158”（Azure为 VM 创建的网络接口在 **myvmnva** 后面使用不同的数字），如下图所示：

    ![VM 网络](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

8. 要使网络接口能够转发发送给它的、而不是发往其自身 IP 地址的网络流量，必须为该网络接口启用 IP 转发。 在“设置”下选择“IP 配置”，为“IP 转发”选择“已启用”，然后选择“保存”，如下图所示：

    ![启用 IP 转发](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

## <a name="create-virtual-machines"></a>创建虚拟机

在虚拟网络中创建两个 VM，以便可以在后续步骤中验证来自公共子网的流量是否通过 NVA 路由到专用子网。 完成[创建 NVA](#create-a-network-virtual-appliance) 的步骤 1-6。 使用步骤 3 和 5，除以下更改外中使用相同的设置：

|虚拟机名称      |子网      | 公共 IP 地址     |
|--------- | -----------|---------              |
| myVmPublic  | 公共     | 接受门户中的默认值 |
| myVmPrivate | 专用    | 接受门户中的默认值 |

在 Azure 创建 *myVmPublic* VM 时，你可以创建 *myVmPrivate* VM。 在 Azure 完成创建两个 VM 之前，请不要继续后续步骤。

## <a name="route-traffic-through-an-nva"></a>通过 NVA 路由流量

1. 在门户顶部的“搜索”框中，开始键入“myVmPrivate”。 当“myVmPrivate”VM 出现在搜索结果中时，请选择它。
2. 通过选择“连接”创建到 *myVmPrivate* VM 的远程桌面连接，如下图所示：

    ![连接到 VM ](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. 若要连接到 VM，请打开已下载的 RDP 文件。 出现提示时，选择“连接”。
4. 输入在创建 VM 时指定的用户名和密码（可能需要选择“更多选择”，然后选择“使用其他帐户”，以便指定在创建 VM 时输入的凭据），然后选择“确定”。
5. 你可能会在登录过程中收到证书警告。 选择“是”以继续进行连接。
6. 在稍后的步骤中，trace route 工具用于测试路由。 Trace route 使用 Internet 控制消息协议 (ICMP)，而 Windows 防火墙会拒绝该协议。 在 *myVmPrivate* VM 上通过 PowerShell 输入以下命令，允许 ICMP 通过 Windows 防火墙：

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    虽然本教程中使用 trace route 测试路由，但在进行生产部署时，不建议允许 ICMP 通过 Windows 防火墙。
7. 在[启用 IP 转发](#enable-ip-forwarding)中已经在 Azure 中为 VM 的网络接口启用了 IP 转发。 在 VM 中，VM 中运行的操作系统或应用程序也必须能够转发网络流量。 在 *myVmNva* VM 的操作系统中启用 IP 转发：

    在 *myVmPrivate* VM 中的命令提示符下，通过远程桌面连接到 *myVmNva* VM：

    ``` 
    mstsc /v:myvmnva
    ```
    
    若要在操作系统中启用 IP 转发，请通过 *myVmNva* VM 在 PowerShell 中输入以下命令：

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    重启 *myVmNva* VM，这也会断开远程桌面会话的连接。
8. 在仍与 *myVmPrivate* VM 保持连接的情况下重启 *myVmNva* VM 后，与 *myVmPublic* VM 建立远程桌面会话：

    ``` 
    mstsc /v:myVmPublic
    ```
    
    在 *myVmPublic* VM 上，通过 PowerShell 输入以下命令来允许 ICMP 通过 Windows 防火墙：

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

9. 若要测试从 *myVmPublic* VM 发往 *myVmPrivate* VM 的网络流量的路由，请在 *myVmPublic* VM 上通过 PowerShell 输入以下命令：

    ```
    tracert myVmPrivate
    ```

    响应类似于以下示例：
    
    ```
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    可以看到，第一个跃点为 10.0.2.4，即 NVA 的专用 IP 地址。 第二个跃点为 10.0.1.4，即 *myVmPrivate* VM 的专用 IP 地址。 添加到 *myRouteTablePublic* 路由表并关联到公共子网的路由导致 Azure 通过 NVA 路由流量，而不是直接将流量路由到专用子网。
10.  关闭与 *myVmPublic* VM 建立的远程桌面会话，这样，就会与 *myVmPrivate* VM 保持连接。
11. 若要测试从 *myVmPrivate* VM 发往 *myVmPublic* VM 的网络流量的路由，请在 *myVmPrivate* VM 上通过命令提示符下输入以下命令：

    ```
    tracert myVmPublic
    ```

    响应类似于以下示例：

    ```
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    可以看到流量从 *myVmPrivate* VM 直接路由到 *myVmPublic* VM。 默认情况下，Azure 直接在子网之间路由流量。
12. 关闭与 *myVmPrivate* VM 建立的远程桌面会话。

## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，可将资源组及其包含的所有资源一并删除： 

1. 在门户顶部的“搜索”框中输入“myResourceGroup”。 当在搜索结果中看到“myResourceGroup”时，将其选中。
2. 选择“删除资源组”。
3. 对于“键入资源组名称:”，输入“myResourceGroup”，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

在本教程中，你创建了一个路由表并将其关联到了某个子网。 还创建了一个简单 NVA，用于将流量从公共子网路由到专用子网。 从 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking)部署各种执行网络功能（例如防火墙和 WAN 优化）的预配置 NVA。 若要了解有关路由的详细信息，请参阅[路由概述](virtual-networks-udr-overview.md)和[管理路由表](manage-route-table.md)。


尽管可以在一个虚拟网络中部署多个 Azure 资源，但无法将某些 Azure PaaS 服务的资源部署到虚拟网络。 不过，仍可以限制为只允许来自某个虚拟网络子网的流量访问某些 Azure PaaS 服务的资源。 若要了解如何限制 Azure PaaS 资源的网络访问，请继续学习下一篇教程。

> [!div class="nextstepaction"]
> [限制 PaaS 资源的网络访问](tutorial-restrict-network-access-to-resources.md)
