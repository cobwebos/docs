---
title: "路由网络流量 - Azure 门户 | Microsoft Docs"
description: "了解如何使用 Azure 门户通过路由表路由网络流量。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 4cc814e1fd3ee8979662695f9dec3dcce335dc2a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-portal"></a>使用 Azure 门户通过路由表路由网络流量

默认情况下，Azure 自动在虚拟网络中的所有子网之间路由流量。 可以创建自己的路由来覆盖 Azure 的默认路由。 创建自定义路由的功能非常有用，例如，可以通过防火墙在子网之间路由流量。 本文介绍如何执行以下操作：

> [!div class="checklist"]
> * 创建路由表
> * 创建路由
> * 将路由表关联到虚拟网络子网
> * 测试路由
> * 排查路由问题

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="log-in-to-azure"></a>登录 Azure 

通过 http://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-route-table"></a>创建路由表

默认情况下，Azure 在虚拟网络中的所有子网之间路由流量。 若要详细了解 Azure 的默认路由，请参阅[系统路由](virtual-networks-udr-overview.md)。 若要覆盖 Azure 的默认路由，可以创建包含路由的路由表，并将该路由表关联到虚拟网络子网。

1. 选择 Azure 门户左上角的“+ 创建资源”。
2. 依次选择“网络”、“路由表”。
3. 选择自己的**订阅**，选择或输入以下信息，然后选择“创建”：
 
    ![创建路由表](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>创建路由

一个路由表包含零个或多个路由。 

1. 在门户顶部的“搜索资源、服务和文档”框中，开始键入*myRouteTablePublic*。 当“myRouteTablePublic”出现在搜索结果中时，请选择它。
2. 在“设置”下选择“路由”，然后选择“+ 添加”，如下图所示：

    ![添加路由](./media/tutorial-create-route-table-portal/add-route.png) 
 
4. 在“添加路由”下，选择或输入以下信息，然后选择“确定”：
    - **路由名称**：*ToPrivateSubnet*
    - **地址前缀**：10.0.1.0/24
    - **下一跃点类型**：选择“虚拟设备”。
    - **下一跃点地址**：10.0.2.4

    该路由通过 IP 地址为 10.0.2.4 的网络虚拟设备来定向所有发往 10.0.1.0/24 地址前缀的流量。 后续步骤将会创建该网络虚拟设备以及具有指定地址前缀的子网。 该路由覆盖 Azure 的默认路由。默认路由在子网之间直接路由流量。 每个路由指定下一跃点类型。 下一跃点类型指示 Azure 如何路由流量。 在此示例中，下一跃点类型为 *VirtualAppliance*。 若要详细了解 Azure 中的所有可用下一跃点类型及其使用时机，请参阅[下一跃点类型](virtual-networks-udr-overview.md#custom-routes)。

## <a name="associate-a-route-table-to-a-subnet"></a>将路由表关联到子网

将路由表关联到子网之前，必须先创建虚拟网络和子网：

1. 选择 Azure 门户左上角的“+ 创建资源”。
2. 选择“网络”，然后选择“虚拟网络”。
3. 在“创建虚拟网络”下面，选择或输入以下信息，然后选择“创建”：
    
    - **名称**：*myVirtualNetwork*
    - **地址空间**：*10.0.0.0/16*
    - **订阅**：选择自己的订阅。
    - **资源组**：选择“使用现有项”和“myResourceGroup”。
    - **位置**：选择“美国东部”
    - **子网名称**：*Public*
    - **地址范围：***10.0.0.0/24*

4. 在门户顶部的“搜索资源、服务和文档”框中，开始键入“myVirtualNetwork”。 当“myVirtualNetwork”出现在搜索结果中时，将其选中。
5. 将两个附加的子网添加到虚拟网络。 在“设置”下面选择“子网”，然后选择“+ 子网”，如下图中所示：

    ![添加子网](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. 选择或输入以下信息，然后选择“确定”：
    - **名称**：Private
    - **地址空间**：*10.0.1.0/24*

7. 再次完成步骤 5 和 6，并提供以下信息：
    - **名称**：DMZ
    - **地址空间**：*10.0.2.0/24*

可将一个路由表关联到零个或多个子网。 子网可以没有或有一个与其相关联的路由表。 来自子网的出站流量将会根据 Azure 的默认路由，以及已添加到与子网关联的路由表中的任何自定义路由进行路由。 将 *myRouteTablePublic* 路由表关联到 *Public* 子网：

1. 完成上述步骤后，会显示“myVirtualNetwork - 子网”框。 在“设置”下面选择“子网”，然后选择“Public”。
2. 如下图所示，依次选择“路由表”、“MyRouteTablePublic”。

    ![关联路由表](./media/tutorial-create-route-table-portal/associate-route-table.png) 

3. 选择“保存”。

## <a name="test-routing"></a>测试路由

若要测试路由，需要创建一个虚拟机，充当用于路由上一步骤中创建的路由的网络虚拟设备。 创建网络虚拟设备后，可虚拟机部署到公共和专用子网。 然后通过网络虚拟设备将流量从公共子网路由到专用子网。

### <a name="create-a-network-virtual-appliance"></a>创建网络虚拟设备

在上一步骤中，我们创建了将网络虚拟设备指定为下一跃点类型的路由。 运行网络应用程序的虚拟机通常称为网络虚拟设备。 在生产环境中，部署的网络虚拟设备通常是预配置的虚拟机。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1) 中提供了多个网络虚拟设备。 本文将会创建一个基本的虚拟机。

1. 选择 Azure 门户左上角的“+ 创建资源”。
2. 选择“计算”，然后选择“Windows Server 2016 Datacenter”。 可以选择不同的操作系统，但剩余步骤假定你选择了“Windows Server 2016 Datacenter”。 
3. 对于“基本信息”，选择或输入以下信息，然后选择“确定”：
    - **名称**：*myVmNva*
    - **资源组**：选择“使用现有”，然后选择“myResourceGroup”。
    - **位置**：选择“美国东部”。

    稍后的步骤中将使用你输入的“用户名”和“密码”。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。 所选的“位置”和“订阅”必须与虚拟网络所在的位置和订阅相同。 不要求选择在其中创建了虚拟网络的同一资源组，但本教程中选择了同一资源组。
4. 在“选择大小”下选择 VM 大小。
5. 对于“设置”，选择或输入以下信息，然后选择“确定”：
    - **虚拟网络**：确保选择“myVirtualNetwork”。 如果没有，请选择“虚拟网络”，然后在“选择虚拟网络”下选择“myVirtualNetwork”。
    - **子网**：选择“子网”，然后在“选择子网”下面选择“DMZ”。
    - **公共 IP 地址**：选择“公共 IP 地址”，然后在“选择公共 IP 地址”下面选择“无”。 未向此虚拟机分配公共 IP 地址，因为无法从 Internet 连接到此虚拟机。
6. 在“摘要”中的“创建”下，选择“创建”以启动虚拟机部署。

创建虚拟机需花费几分钟的时间。 在 Azure 完成创建虚拟机并返回有关虚拟机的信息之前，请不要继续下一步。

Azure 在创建虚拟机时，还会在 *DMZ* 子网中创建一个[网络接口](virtual-network-network-interface.md)，并将该网络接口附加到虚拟机。 对于用于转发发往任何未分配到网络接口的 IP 地址的流量的每个 Azure 网络接口，必须启用 IP 转发。

1. 在框中，打开虚拟机已在创建后下,**设置**，选择**网络**，然后选择**myvmnva158** （网络接口 Azure为虚拟机具有不同数目之后创建**myvmnva**），如下图所示：

    ![虚拟机网络](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

    在外围网络子网中创建网络虚拟设备时，Azure 已自动创建网络接口，已将该网络接口附加到虚拟机，并且向网络接口分配了专用 IP 地址 *10.0.2.4*。 

2. 在“设置”下选择“IP 配置”，为“IP 转发”选择“已启用”，然后选择“保存”，如下图所示：

    ![启用 IP 转发](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

### <a name="create-virtual-machines"></a>创建虚拟机

在虚拟网络中创建两个虚拟机，以便可以在后续步骤中验证来自公共子网的流量是否通过网络虚拟设备路由到专用子网。

完成[创建网络虚拟设备](#create-a-network-virtual-appliance)的步骤 1-6。 使用步骤 3 和 5，除以下更改外中使用相同的设置：

|虚拟机   |名称      |子网      | 公共 IP 地址     |
|---------         |--------- | -----------|---------              |
|虚拟机 1 | myVmWeb  | 公共     | 接受门户中的默认值 |
|虚拟机 2 | myVmMgmt | 专用    | 接受门户中的默认值 |

当 Azure 创建 *myVmWeb* 虚拟机时，可以创建 *myVmMgmt* 虚拟机。 在 Azure 完成创建两个虚拟机之前，请不要继续后续步骤。

### <a name="route-traffic-through-a-network-virtual-appliance"></a>通过网络虚拟设备路由流量

1. 在门户顶部的“搜索”框中，开始键入“myVmMgmt”。 当“myVmMgmt”出现在搜索结果中时，将其选中。
2. 通过选择“连接”创建到 *myVmMgmt* 虚拟机的远程桌面连接，如下图中所示：

    ![连接到虚拟机](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. 若要连接到 VM，请打开已下载的 RDP 文件。 出现提示时，选择“连接”。
4. 输入在创建虚拟机时指定的用户名和密码（可能需要选择“更多选择”，然后选择“使用其他帐户”，以便指定在创建虚拟机时输入过的凭据），然后选择“确定”。
5. 你可能会在登录过程中收到证书警告。 选择“是”以继续进行连接。
6. 在稍后的步骤中，tracert.exe 命令用于测试路由。 Tracert 使用无法通过 Windows 防火墙的 ICMP。 在命令提示符处输入以下命令，允许 ICMP 通过 Windows 防火墙：

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    虽然本文中使用 tracert 测试路由，但在进行生产部署时，不建议允许 ICMP 通过 Windows 防火墙。
7. 在[启用 IP 转发](#enable-ip-forwarding)中已经在 Azure 中为虚拟机的网络接口启用了 IP 转发。 在虚拟机中，操作系统或虚拟机中运行的应用程序也必须能够转发网络流量。 在生产环境中部署网络虚拟设备时，该设备通常会筛选、记录数据或执行其他某种功能，然后再转发流量。 但在本文中，操作系统只会转发它收到的所有流量。 在 *myVmMgmt* 虚拟机中完成以下步骤，在 *myVmNva* 的操作系统中启用 IP 转发：

    在命令提示符下使用以下命令，通过远程桌面连接到 *myVmNva* 虚拟机：

    ``` 
    mstsc /v:myvmnva
    ```
    
    若要在操作系统中启用 IP 转发，请在 PowerShell 中输入以下命令：

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    重启虚拟机，这也会断开远程桌面会话连接。
8. 在仍与 *myVmMgmt* 虚拟机保持连接的情况下重启 *myVmNva* 虚拟机后，使用以下命令与 *myVmWeb* 虚拟机建立远程桌面会话：

    ``` 
    mstsc /v:myVmWeb
    ```
    
    在命令提示符处输入以下命令，允许 ICMP 通过 Windows 防火墙：

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

9. 若要测试从 *myVmWeb* 虚拟机到 *myVmMgmt* 虚拟机的网络流量路由，请在命令提示符下输入以下命令：

    ```
    tracert myvmmgmt
    ```

    响应类似于以下示例：
    
    ```
    Tracing route to myvmmgmt.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    可以看到，第一个跃点为 10.0.2.4，即网络虚拟设备的专用 IP 地址。 第二个跃点为 10.0.1.4，即 *myVmMgmt* 虚拟机的专用 IP 地址。 添加到 *myRouteTablePublic* 路由表并关联到公共子网的路由导致 Azure 通过 NVA 路由流量，而不是直接将流量路由到专用子网。
10.  关闭与 *myVmWeb* 虚拟机建立的远程桌面会话，这样，就会与 *myVmMgmt* 虚拟机保持连接。
11. 若要测试从 *myVmMgmt* 虚拟机到 *myVmWeb* 虚拟机的网络流量路由，请在命令提示符处输入以下命令：

    ```
    tracert myvmweb
    ```

    响应类似于以下示例：

    ```
    Tracing route to myvmweb.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    可以看到，流量直接从 *myVmMgmt* 虚拟机路由到了 *myVmWeb* 虚拟机。 默认情况下，Azure 直接在子网之间路由流量。
12. 关闭与 *myVmMgmt* 虚拟机建立的远程桌面会话。

## <a name="troubleshoot-routing"></a>排查路由问题

如前面的步骤所述，Azure 会应用默认路由，而你可以选择性地使用自己的路由覆盖默认路由。 有时，无法按预期方式路由流量。 可以使用 Azure 网络观察程序的[下一跃点](../network-watcher/network-watcher-check-next-hop-portal.md)功能确定流量在两个虚拟机之间的路由方式。 

1. 在门户顶部的“搜索”框中，开始键入“网络观察程序”。 搜索结果中出现“网络观察程序”后，将其选中。
2. 在“网络诊断工具”下选择“下一跃点”。
3. 若要测试从 *myVmWeb* (10.0.0.4) 到 *myVmMgmt* 虚拟机 (10.0.1.4) 的流量路由，请选择订阅，输入下图所示的信息（你的“网络接口”名称会略有不同），然后选择“下一跃点”：

    ![下一跃点](./media/tutorial-create-route-table-portal/next-hop.png)  

    “结果”输出中指出，从 *myVmWeb* 到 *myVmMgmt* 的流量的下一跃点 IP 地址为 10.0.2.4（*myVmNva* 虚拟机），下一跃点类型为 *VirtualAppliance*，导致路由的路由表为 *myRouteTablePublic*。

每个网络接口的有效路由是 Azure 默认路由与定义的任何路由的组合。 若要查看虚拟机中网络接口的所有有效路由，请完成以下步骤：

1. 在门户顶部的“搜索”框中，开始键入“myVmWeb”。 当“myVmWeb”出现在搜索结果中时，请选择它。
2. 在“设置”下选择“网络”，然后选择“myvmweb369”（Azure 为虚拟机创建的网络接口名称的 **myvmweb** 后面使用了一个不同的编号）。
3. 在“支持 + 故障排除”下选择“有效路由”，如下图所示：

    ![有效路由](./media/tutorial-create-route-table-portal/effective-routes.png) 

    查看 Azure 的默认路由，以及在 *myRouteTablePublic* 路由表中添加的路由。 若要详细了解 Azure 如何从路由列表中选择路由，请参阅 [Azure 如何选择路由](virtual-networks-udr-overview.md#how-azure-selects-a-route)。

## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，可将资源组及其包含的所有资源一并删除： 

1. 在门户顶部的“搜索”框中输入“myResourceGroup”。 当在搜索结果中看到“myResourceGroup”时，将其选中。
2. 选择“删除资源组”。
3. 对于“键入资源组名称:”，输入“myResourceGroup”，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

在本文中，我们创建了一个路由表并将其关联到了某个子网。 我们还创建了一个网络虚拟设备，用于将流量从公共子网路由到专用子网。 尽管可以在一个虚拟网络中部署多个 Azure 资源，但无法将某些 Azure PaaS 服务的资源部署到虚拟网络。 不过，仍可以限制为只允许来自某个虚拟网络子网的流量访问某些 Azure PaaS 服务的资源。 请继续学习下一篇教程，了解如何限制 Azure PaaS 资源的网络访问。

> [!div class="nextstepaction"]
> [限制 PaaS 资源的网络访问](virtual-network-service-endpoints-configure.md#azure-portal)
