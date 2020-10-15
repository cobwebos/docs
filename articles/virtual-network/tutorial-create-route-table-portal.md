---
title: 路由网络流量 - 教程 - Azure 门户
titlesuffix: Azure Virtual Network
description: 本教程介绍如何使用 Azure 门户通过路由表路由网络流量。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: d630a41f9b83a852605ffad2a85ad6dd14bbac73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86079643"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>教程：使用 Azure 门户通过路由表路由网络流量

默认情况下，Azure 会在虚拟网络中的所有子网之间路由流量。 可以创建自己的路由来覆盖 Azure 的默认路由。 自定义路由非常有用，例如，它可以让你通过网络虚拟设备 (NVA) 在子网之间路由流量。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建用于流量路由的 NVA
> * 创建路由表
> * 创建路由
> * 将路由表关联到子网
> * 将虚拟机 (VM) 部署到不同子网
> * 通过 NVA 将从一个子网的流量路由到另一个子网

本教程使用 [Azure 门户](https://portal.azure.com)。 你也可使用 [Azure CLI](tutorial-create-route-table-cli.md) 或 [Azure PowerShell](tutorial-create-route-table-powershell.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-nva"></a>创建 NVA

网络虚拟设备 (NVA) 是帮助提供网络功能（例如路由和防火墙优化）的虚拟机。 本教程假设使用 **Windows Server 2016 Datacenter**。 如果需要，可以选择不同的操作系统。

1. 在 [Azure 门户](https://portal.azure.com)菜单或“主页”中，选择“创建资源”。 

1. 选择“安全性” > “Windows Server 2016 Datacenter”。 

    ![Windows Server 2016 Datacenter，创建 VM，Azure 门户](./media/tutorial-create-route-table-portal/vm-ws2016-datacenter.png)

1. 在“创建虚拟机”页中的“基本信息”下，输入或选择以下信息： 

    | 部分 | 设置 | 操作 |
    | ------- | ------- | ----- |
    | **项目详细信息** | 订阅 | 选择订阅。 |
    | | 资源组 | 选择“新建”，输入 *myResourceGroup*，然后选择“确定” 。 |
    | **实例详细信息** | 虚拟机名称 | 输入 *myVmNva*。 |
    | | 区域 | 选择“(美国)美国东部”。 |
    | | 可用性选项 | 选择“无需基础结构冗余”。 |
    | | 映像 | 选择“Windows Server 2016 Datacenter”。 |
    | | 大小 | 保留默认值“标准 DS1 v2”。 |
    | **管理员帐户** | 用户名 | 输入所选用户名。 |
    | | 密码 | 输入所选的密码，该密码必须至少包含 12 个字符，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。 |
    | | 确认密码 | 再次输入密码。 |
    | **入站端口规则** | 公共入站端口 | 选择“无”。 |
    | **节省资金** | 已有 Windows Server 许可证? | 选取“否”。 |

    ![“基本信息”，创建虚拟机，Azure 门户](./media/tutorial-create-route-table-portal/basics-create-virtual-machine.png)

    然后选择页面底部的“下一步:磁盘 >”。

1. 在“磁盘”下选择符合需求的设置，然后选择“下一步: 网络 >”。

1. 在“网络”下：

    1. 对于“虚拟网络”，请选择“新建”。 
    
    1. 在“创建虚拟网络”对话框中的“名称”下，输入 *myVirtualNetwork*。 

    1. 在“地址空间”中，将现有的地址范围替换为 *10.0.0.0/16*。

    1. 在“子网”中，选择“删除”图标删除现有子网，然后输入“子网名称”和“地址范围”的以下组合。    输入有效的名称和范围后，该子网下面会出现一个新的空行。

        | 子网名称 | 地址范围 |
        | ----------- | ------------- |
        | *公共* | *10.0.0.0/24* |
        | 专用 | *10.0.1.0/24* |
        | *外围网络* | *10.0.2.0/24* |

    1. 选择“确定”退出对话框。

    1. 在“子网”中选择“外围网络(10.0.2.0/24)”。 

    1. 在“公共 IP”中选择“无”，因为此 VM 不会通过 Internet 进行连接。 

    1. **选择“下一步:** 管理 >”。

1. 在“管理”下：

    1. 在“诊断存储帐户”中选择“新建”。 
    
    1. 在“创建存储帐户”对话框中，输入或选择以下信息：

        | 设置 | 值 |
        | ------- | ----- |
        | 名称 | *mynvastorageaccount* |
        | 帐户类型 | “存储(常规用途 v1)” |
        | 性能 | **Standard** |
        | 复制 | **本地冗余存储 (LRS)** |
    
    1. 选择“确定”退出对话框。

    1. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将验证配置。

1. 看到“验证通过”消息时，选择“创建” 。

    创建 VM 需要几分钟时间。 等到 Azure 创建完 VM 为止。 “部署正在进行”页会显示部署详细信息。

1. VM 准备就绪后，选择“转到资源”。

## <a name="create-a-route-table"></a>创建路由表

1. 在 [Azure 门户](https://portal.azure.com)菜单或“主页”中，选择“创建资源”。 

2. 在搜索框中，输入“路由表”。 当“路由表”出现在搜索结果中时，请选择它。

3. 在“路由表”页中，选择“创建”。

4. 在“创建路由表”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | *myRouteTablePublic* |
    | 订阅 | 订阅 |
    | 资源组 | **myResourceGroup** |
    | 位置 | **（美国）美国东部** |
    | 虚拟网络网关路由传播 | **已启用** |

    ![创建路由表，Azure 门户](./media/tutorial-create-route-table-portal/create-route-table.png)

5. 选择“创建”。

## <a name="create-a-route"></a>创建路由

1. 转到 [Azure 门户](https://portal.azure.com)来管理路由表。 搜索并选择“路由表”。

1. 选择路由表的名称 (**myRouteTablePublic**)。

1. 选择“路由” > “添加”。 

    ![创建路由，路由表，Azure 门户](./media/tutorial-create-route-table-portal/add-route.png)

1. 在“添加路由”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 路由名称 | *ToPrivateSubnet* |
    | 地址前缀 | *10.0.1.0/24*（前面创建的“专用”子网的地址范围） |
    | 下一跃点类型 | **虚拟设备** |
    | 下一跃点地址 | *10.0.2.4*（“外围网络”子网地址范围内的某个地址） |

1. 选择“确定”。

## <a name="associate-a-route-table-to-a-subnet"></a>将路由表关联到子网

1. 转到 [Azure 门户](https://portal.azure.com)来管理虚拟网络。 搜索并选择“虚拟网络”。

1. 选择虚拟网络的名称 (**myVirtualNetwork**)。

1. 在虚拟网络的菜单栏中选择“子网”。

1. 在虚拟网络的子网列表中选择“公共”。

1. 在“路由表”中选择创建的路由表 (**myRouteTablePublic**)，然后选择“保存”以将路由表关联到“公共”子网。 

    ![关联路由表，子网列表，虚拟网络，Azure 门户](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="turn-on-ip-forwarding"></a>启用 IP 转发

接下来，为新的 NVA 虚拟机 *myVmNva* 启用 IP 转发。 当 Azure 向 *myVmNva* 发送网络流量时，如果流量发往不同的 IP 地址，则 IP 转发会将流量发送到正确的位置。

1. 转到 [Azure 门户](https://portal.azure.com)来管理 VM。 搜索并选择“虚拟机”。

1. 选择 VM 的名称 (**myVmNva**)。

1. 在 NVA 虚拟机的菜单栏中选择“网络”。

1. 选择“myvmnva123”。 这是 Azure 为 VM 创建的网络接口。 Azure 将添加数字来确保接口的名称保持唯一。

    ![网络，网络虚拟设备 (NVA) 虚拟机 (VM)，Azure 门户](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. 在网络接口菜单栏中选择“IP 配置”。

1. 在“IP 配置”页中，将“IP 转发”设置为“已启用”，然后选择“保存”。   

    ![启用 IP 转发，IP 配置，网络接口，网络虚拟设备 (NVA) 虚拟机 (VM)，Azure 门户](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>创建公共和专用虚拟机

在虚拟网络中创建公共 VM 和专用 VM。 稍后，我们将使用这些 VM 来查看 Azure 如何通过 NVA 将“公共”子网流量路由到“专用”子网。 

若要创建公共 VM 和专用 VM，请遵循前面所述的[创建 NVA](#create-an-nva) 的步骤。 无需等待部署完成，也不需要转到 VM 资源。 使用的大多数设置与前面所述相同，但下述设置除外。

在选择“创建”来创建公共或专用 VM 之前，请转到以下两个子部分（[公共 VM](#public-vm) 和[专用 VM](#private-vm)），其中列出了必须修改的值。 在 Azure 部署完这两个 VM 之后，你可以转到下一部分（[通过 NVA 路由流量](#route-traffic-through-an-nva)）。

### <a name="public-vm"></a>公共 VM

| 选项卡 | 设置 | 值 |
| --- | ------- | ----- |
| 基础 | 资源组 | **myResourceGroup** |
| | 虚拟机名称 | *myVmPublic* |
| | 公共入站端口 | “允许选定的端口” |
| | 选择入站端口 | **RDP** |
| 网络 | 虚拟网络 | **myVirtualNetwork** |
| | 子网 | “公共(10.0.0.0/24)” |
| | 公共 IP 地址 | 默认值 |
| 管理 | 诊断存储帐户 | **mynvastorageaccount** |

### <a name="private-vm"></a>专用 VM

| 选项卡 | 设置 | 值 |
| --- | ------- | ----- |
| 基础 | 资源组 | **myResourceGroup** |
| | 虚拟机名称 | *myVmPrivate* |
| | 公共入站端口 | “允许选定的端口” |
| | 选择入站端口 | **RDP** |
| 网络 | 虚拟网络 | **myVirtualNetwork** |
| | 子网 | “专用(10.0.1.0/24)” |
| | 公共 IP 地址 | 默认值 |
| 管理 | 诊断存储帐户 | **mynvastorageaccount** |

## <a name="route-traffic-through-an-nva"></a>通过 NVA 路由流量

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>通过远程桌面登录到 myVmPrivate

1. 转到 [Azure 门户](https://portal.azure.com)来管理专用 VM。 搜索并选择“虚拟机”。

1. 选择专用 VM 的名称 (**myVmPrivate**)。

1. 选择 VM 菜单栏中“连接”，以便与专用 VM 建立远程桌面连接。

1. 在“使用 RDP 连接”页中，选择“下载 RDP 文件”。  Azure 会创建远程桌面协议 ( *.rdp*) 文件，并将其下载到计算机。

1. 打开下载的 *.rdp* 文件。 出现提示时，选择“连接”。 选择“更多选项” > “使用其他帐户”，然后输入创建专用 VM 时指定的用户名和密码。 

1. 选择“确定”。

1. 如果在登录过程中看到证书警告，请选择“是”以连接到 VM。

### <a name="enable-icmp-through-the-windows-firewall"></a>允许 ICMP 通过 Windows 防火墙

在稍后的步骤中，我们将使用跟踪路由工具来测试路由。 跟踪路由使用 Internet 控制消息协议 (ICMP)，而 Windows 防火墙默认拒绝该协议。 启用通过 Windows 防火墙的 ICMP。

1. 在 *myVmPrivate* 的远程桌面中，打开 PowerShell。

1. 输入以下命令：

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    本教程将使用跟踪路由来测试路由。 对于生产环境，我们不建议允许通过 Windows 防火墙的 ICMP。

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>在 myVmNva 中启用 IP 转发

我们已使用 Azure 为 VM 的网络接口[启用了 IP 转发](#turn-on-ip-forwarding)。 VM 的操作系统也必须转发网络流量。 使用以下命令为 *myVmNva* VM 的操作系统启用 IP 转发。

1. 在 *myVmPrivate* VM 中的命令提示符下，打开远程桌面并连接到 *myVmNva* VM：

    ```cmd
    mstsc /v:myvmnva
    ```

1. 在 *myVmNva* VM 上的 PowerShell 中输入以下命令，以启用 IP 转发：

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. 重启 *myVmNva* VM：在任务栏中，选择“开始” > “电源”，然后选择“其他(计划内)” > “继续”。   

    这也会断开远程桌面会话的连接。

1. 重启 *myVmNva* VM 后，与 *myVmPublic* VM 建立远程桌面会话。 在仍与 *myVmPrivate* VM 保持连接的情况下，打开命令提示符并运行以下命令：

    ```cmd
    mstsc /v:myVmPublic
    ```
1. 在 *myVmPublic* 的远程桌面中打开 PowerShell。

1. 输入以下命令，启用通过 Windows 防火墙的 ICMP：

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>测试网络流量的路由

首先，让我们测试从 *myVmPublic* VM 到 *myVmPrivate* VM 的网络流量路由。

1. 在 *myVmPublic* VM 上的 PowerShell 中，输入以下命令：

    ```powershell
    tracert myVmPrivate
    ```

    响应类似于以下示例：

    ```powershell
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4

    Trace complete.
    ```

    可以看到，第一个跃点连接到 10.0.2.4，即 NVA 的专用 IP 地址。 第二个跃点为路由到 *myVmPrivate* VM 的专用 IP 地址：10.0.1.4。 前面我们已将路由添加到 *myRouteTablePublic* 路由表，并已将它关联到“公共”子网。 因此，Azure 通过 NVA 发送了流量，而不是直接将流量发送到“专用”子网。

1. 关闭与 *myVmPublic* VM 建立的远程桌面会话，这样，就会与 *myVmPrivate* VM 保持连接。

1. 在 *myVmPrivate* VM 中的命令提示符下，输入以下命令：

    ```cmd
    tracert myVmPublic
    ```

    此命令测试从 *myVmPrivate* VM 到 *myVmPublic* VM 的网络流量路由。 响应类似于以下示例：

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    可以看到，Azure 直接将流量从 *myVmPrivate* VM 路由到了 *myVmPublic* VM。 默认情况下，Azure 直接在子网之间路由流量。

1. 关闭与 *myVmPrivate* VM 建立的远程桌面会话。

## <a name="clean-up-resources"></a>清理资源

不再需要 myResourceGroup 时，请删除该资源组及其包含的所有资源：

1. 转到 [Azure 门户](https://portal.azure.com)来管理资源组。 搜索并选择“资源组”。

1. 选择资源组的名称 (**myResourceGroup**)。

1. 选择“删除资源组”。

1. 在确认对话框中，在“键入资源组名称”中输入 *myResourceGroup*，然后选择“删除”。  Azure 将删除 *myResourceGroup* 以及绑定到该资源组的所有资源，包括路由表、存储帐户、虚拟网络、VM、网络接口和公共 IP 地址。

## <a name="next-steps"></a>后续步骤

在本教程中，你创建了一个路由表并将其关联到了某个子网。 还创建了一个简单 NVA，用于将流量从公共子网路由到专用子网。 现在，可以从 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking)部署不同的预配置 NVA，其中提供了许多有用的网络功能。 若要了解有关路由的详细信息，请参阅[路由概述](virtual-networks-udr-overview.md)和[管理路由表](manage-route-table.md)。

尽管可以在一个虚拟网络中部署多个 Azure 资源，但 Azure 无法将某些 PaaS 服务的资源部署到虚拟网络。 可以限制对某些 Azure PaaS 服务的资源的访问，不过，只能对来自虚拟网络子网的流量实施这种限制。 若要了解如何限制对 Azure PaaS 资源的网络访问，请参阅下一篇教程。

> [!div class="nextstepaction"]
> [限制 PaaS 资源的网络访问](tutorial-restrict-network-access-to-resources.md)

> [!NOTE] 
> Azure 服务是要花钱的。 Azure 成本管理有助于你设置预算并配置警报，使支出保持在控制范围之内。 使用成本管理分析、管理和优化 Azure 成本。 要了解详细信息，请参阅[分析成本快速入门](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。