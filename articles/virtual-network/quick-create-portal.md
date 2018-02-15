---
title: "在 Azure 中创建虚拟网络 - 门户 | Microsoft Docs"
description: "快速了解如何使用 Azure 门户创建虚拟网络。 虚拟网络能让不同类型的 Azure 资源互相私下通信。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 264dc38383b9adad70325f7fb7802b1dcf2da1c0
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>使用 Azure 门户创建虚拟网络

本文将介绍如何创建虚拟网络。 创建好虚拟网络后，在虚拟网络中部署两个虚拟机，并让它们互相私下通信。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="log-in-to-azure"></a>登录 Azure 

通过 http://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-virtual-network"></a>创建虚拟网络

1. 单击 Azure 门户左上角的“+ 新建”。

2. 选择“网络”，然后选择“虚拟网络”。

3. 如下图所示，为“名称”输入“myVirtualNetwork”，为“资源组”输入“myResourceGroup”，选择“位置”和“订阅”，保留默认设置，然后单击“创建”。 

    ![输入虚拟网络的基本信息](./media/quick-create-portal/virtual-network.png)

    在 CIDR 表示法中指定“地址空间”。 虚拟网络包含零个或多个子网。 10.0.0.0/24 的默认子网“地址范围”使用虚拟网络的全部地址范围，因此无法使用默认地址空间和范围在虚拟网络中创建其他子网。 指定的地址范围包括 IP 地址 10.0.0.0-10.0.0.254。 可用的地址仅限 10.0.0.4-10.0.0.254，因为 Azure 保留了每个子网中的前四个地址 (0-3) 和最后一个地址。 可用的 IP 地址分配给部署在虚拟网络中的资源。

## <a name="create-virtual-machines"></a>创建虚拟机

虚拟网络能让不同类型的 Azure 资源互相私下通信。 虚拟机是其中一种能部署到虚拟网络的资源类型。 在虚拟网络中创建两个虚拟机，并在稍后步骤中通过它们来验证和了解虚拟机在虚拟网络中互相通信的原理。

1. 单击 Azure 门户左上角的“新建”按钮。

2. 选择“计算”，然后选择“Windows Server 2016 Datacenter”。

3. 输入下方图片中显示的虚拟机信息。 所输入的“用户名”和“密码”将在稍后步骤中用于登录到虚拟机。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。 选择“订阅”，选择使用现有“myResourceGroup”资源组，并确保所选“位置”与创建虚拟网络的位置相同。 完成后，单击“确定”。

    ![输入虚拟机的基本信息](./media/quick-create-portal/virtual-machine-basics.png)

4. 选择虚拟机的大小，然后单击“选择”。 若要查看更多的大小，请选择“全部查看”或更改“支持的磁盘类型”筛选器。 显示的大小可能与以下示例不同： 

    ![选择虚拟机的大小](./media/quick-create-portal/virtual-machine-size.png)

5. 在“设置”中，应已为“虚拟网络”选择“myVirtualNetwork”，如果还没有，请单击“虚拟网络”并选择“myVirtualNetwork”。 对于“子网”，选择保留默认值，然后单击“确定”。

    ![选择虚拟网络](./media/quick-create-portal/virtual-machine-network-settings.png)

6. 在“摘要”页上，单击“创建”以开始虚拟机部署。 

7. 创建虚拟机需花费数分钟。 创建完成后，虚拟机将被固定至 Azure 门户仪表板，虚拟机摘要将自动开启。 单击“网络”。

    ![虚拟机网络信息](./media/quick-create-portal/virtual-machine-networking.png)

    可以看到“专用 IP”地址为 10.0.0.4。 在步骤 5 中，从“设置”中选择“myVirtualNetwork”虚拟网络，并为“子网”选择名为“默认值”的子网。 在[创建虚拟网络](#create-a-virtual-network)时，将子网的“地址范围”设置为默认值 10.0.0.0/24。 Azure 的 DHCP 服务器将所选子网的首个可用地址分配给虚拟机。 由于 Azure 保留每个子网的前四个地址 (0-3)，因此 10.0.0.4 是适用于该子网的第一个可用 IP 地址。

    分配的公共 IP 地址与分配给虚拟机的地址有所不同。 默认情况下，Azure 将向各虚拟机分配公共的、通过 Internet 进行连接的 IP 地址。 从[分配给各 Azure 区域的地址池](https://www.microsoft.com/download/details.aspx?id=41653)将公共 IP 地址分配给虚拟机。 尽管 Azure 了解分配给虚拟机的公共 IP 地址是哪个，但在虚拟机上运行的操作系统却不知道分配给它的公共 IP 地址是哪个。

8. 再次完成步骤 1-7，但在步骤 3 中，将虚拟机命名为“myVm2”。 

9. 创建虚拟机后，如步骤 7 中那样单击“网络”。 可以看到“专用 IP”地址为 10.0.0.5。 由于 Azure 先前已将子网中的首个可用地址 10.0.0.4 分配给虚拟机“myVm1”，因此，它会将子网中的第二个可用地址 10.0.0.5 分配给虚拟机“myVm2”。

## <a name="connect-to-a-virtual-machine"></a>连接到虚拟机

1. 远程连接到虚拟机“myVm1”。 在 Azure 门户的顶部，输入“myVm1”。 当“myVm1”出现在搜索结果中时，请单击它。 单击“连接”按钮。

    ![虚拟机概述](./media/quick-create-portal/virtual-machine-overview.png)


2. 单击“连接”按钮后将创建一个远程桌面协议 (.rdp) 文件，该文件会被下载到你的计算机。  
3. 打开下载的 rdp 文件。 出现提示时，请单击“连接”。 输入在创建虚拟机时指定的用户名和密码，然后单击“确定”。 你可能会在登录过程中收到证书警告。 单击“是”或“继续”继续进行连接。

## <a name="validate-communication"></a>验证通信

尝试 ping Windows 虚拟机失败，因为默认情况下 Windows 防火墙不允许 ping。 要允许 ping myVm1，请从命令提示符中输入以下命令：

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

要验证与 myVm2 的通信，请在虚拟机 myVm1 的命令提示符中输入以下命令。 提供创建虚拟机时使用的凭据，然后完成连接：

```
mstsc /v:myVm2
```

由于两个虚拟机都分配有来自默认子网的私有 IP 地址，且远程桌面通过 Windows 防火墙开启，因此远程桌面连接成功。 可以通过主机名连接至 myVm2，因为 Azure 将自动为虚拟网络中的所有主机提供 DNS 名称解析。 在命令提示符中，从 myVm2 ping myVm1。

```
ping myvm1
```

ping 操作成功，因为在上一步中已经允许该命令通过虚拟机 myVm1 上的 Windows 防火墙。 要确认到 Internet 的出站通信，请输入以下命令：

```
ping bing.com
```

从 bing.com 收到四个答复。默认情况下，虚拟网络中的任意虚拟机都可以与 Internet 进行出站通信。

## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，可将资源组及其相关内容一并删除。 在 Azure 门户的顶部，输入“myResourceGroup”。 当“myResourceGroup”出现在搜索结果中时，请单击它。 单击“删除” 。

## <a name="next-steps"></a>后续步骤

本文将使用一个子网和两个虚拟机来部署默认虚拟网络。 要了解如何使用多个子网创建自定义虚拟网络并执行基本管理任务，请继续参阅创建和管理自定义虚拟网络的教程。


> [!div class="nextstepaction"]
> [创建和管理自定义虚拟网络](virtual-networks-create-vnet-arm-pportal.md#portal)