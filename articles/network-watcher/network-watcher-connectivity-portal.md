---
title: "使用 Azure 网络观察程序排查连接问题 - Azure 门户 | Microsoft Docs"
description: "了解如何通过 Azure 门户使用 Azure 网络观察程序的排查连接问题功能。"
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: jdial
ms.openlocfilehash: 8d3a537523cce3457c18c7563e885a3f7348326f
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>通过 Azure 门户使用 Azure 网络观察程序排查连接问题

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [CLI 2.0](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

了解如何使用排查连接问题来验证是否可以建立从虚拟机到给定终结点的直接 TCP 连接。

## <a name="before-you-begin"></a>开始之前

本文假定你拥有以下资源：

* 要排查连接问题的区域中的网络观察程序实例。
* 用以排查连接问题的虚拟机。

> [!IMPORTANT]
> 排查连接问题需要虚拟机扩展 `AzureNetworkWatcherExtension`。 有关在 Windows VM 上安装扩展的信息，请访问[适用于 Windows 的 Azure 网络观察程序代理虚拟机扩展](../virtual-machines/windows/extensions-nwa.md)；有关 Linux VM 的信息，请访问[适用于 Linux 的 Azure 网络观察程序代理虚拟机扩展](../virtual-machines/linux/extensions-nwa.md)。

## <a name="check-connectivity-to-a-virtual-machine"></a>检查与虚拟机的连接

此示例通过端口 80 检查与目标虚拟机的连接。

导航到网络观察程序并单击“排查连接问题”。 选择虚拟机以检查其连接性。 在“目标”部分，选择“选择虚拟机”，并选择正确的虚拟机和端口来进行测试。

单击“检查”后，将检查指定的端口上的虚拟机间的连接性。 在此示例中，目标虚拟机不可访问，并显示了一个跃点列表。

![查看虚拟机的连接性结果][1]

## <a name="check-remote-endpoint-connectivity"></a>检查远程终结点连接性

若要检查远程终结点的连接性和延迟性，请在“目标”区域中选择“手动指定”单选按钮，输入 URL 和端口并单击“检查”。  此步骤适用于网站等远程终结点及存储终结点。

![查看网站的连接性结果][2]

## <a name="next-steps"></a>后续步骤

查看[创建警报触发的数据包捕获](network-watcher-alert-triggered-packet-capture.md)，了解如何利用虚拟机警报自动执行数据包捕获

访问[查看“IP 流验证”](network-watcher-check-ip-flow-verify-portal.md)，了解是否允许某些流量传入和传出 VM

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png