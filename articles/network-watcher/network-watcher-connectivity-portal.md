---
title: "使用 Azure 网络观察程序检查连接性 - Azure 门户 | Microsoft 文档"
description: "本页说明如何使用 Azure 门户通过网络观察程序执行连接性检查"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 84774d0f40e06a819ca6de6cf0be68e17ba474e4
ms.contentlocale: zh-cn
ms.lasthandoff: 08/23/2017

---

# <a name="check-connectivity-with-azure-network-watcher-using-the-azure-portal"></a>使用 Azure 门户通过 Azure 网络观察程序检查连接性

> [!div class="op_single_selector"]
> - [门户](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [CLI 2.0](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

了解如何使用连接来验证是否可以建立从虚拟机到给定终结点的直接 TCP 连接。

## <a name="before-you-begin"></a>开始之前

本文假定你拥有以下资源：

* 要检查连接性的区域中的网络观察程序实例。

* 用于检查连接性的虚拟机。

> [!IMPORTANT]
> 连接性检查需要虚拟机扩展 `AzureNetworkWatcherExtension`。 有关在 Windows VM 上安装扩展的信息，请访问[适用于 Windows 的 Azure 网络观察程序代理虚拟机扩展](../virtual-machines/windows/extensions-nwa.md)；有关 Linux VM 的信息，请访问[适用于 Linux 的 Azure 网络观察程序代理虚拟机扩展](../virtual-machines/linux/extensions-nwa.md)。

## <a name="check-connectivity-to-a-virtual-machine"></a>检查与虚拟机的连接

此示例通过端口 80 检查与目标虚拟机的连接。

导航到网络观察程序并单击“连接性检查(预览)”。 选择虚拟机以检查其连接性。 在“目标”部分，选择“选择虚拟机”，并选择正确的虚拟机和端口来进行测试。

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

