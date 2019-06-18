---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6d0737a7300b2a6025f776c1ed65a05cacf2141a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66157675"
---
创建远程桌面连接来连接到部署到 VNet 的 VM。 若要验证是否能够连接到 VM，最好的方式是使用其专用 IP 地址而不是计算机名称进行连接。 这种方式测试的是能否进行连接，而不是测试名称解析是否已正确配置。 

1. 定位 VM 的专用 IP 地址。 若要查找 VM 的专用 IP 地址，可以在 Azure 门户中或使用 PowerShell 查看 VM 的属性。
2. 验证你是否已使用点到站点 VPN 连接连接到 VNet。 
3. 若要打开远程桌面连接，请在任务栏上的搜索框中键入 *RDP* 或*远程桌面连接*，然后选择“远程桌面连接”。  也可以在 PowerShell 中使用 **mstsc** 命令打开远程桌面连接。 
3. 在“远程桌面连接”中，输入 VM 的专用 IP 地址。  如果需要，选择“显示选项”来调整其他设置，然后进行连接。 

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>排查到 VM 的 RDP 连接的问题

如果无法通过 VPN 连接连接到虚拟机，可以查看一些项目。 

- 验证 VPN 连接是否成功。
- 验证是否已连接到 VM 的专用 IP 地址。
- 输入 **ipconfig** 来检查分配给以太网适配器的 IPv4 地址，该适配器所在的计算机正是你要从其进行连接的计算机。 如果该 IP 地址位于要连接到的 VNet 的地址范围内，或者位于 VPNClientAddressPool 的地址范围内，则会出现重叠的地址空间。 当地址空间以这种方式重叠时，网络流量不会抵达 Azure，而是呆在本地网络中。
- 如果可以使用专用 IP 地址连接到 VM，但不能使用计算机名称进行连接，则请验证是否已正确配置 DNS。 若要详细了解如何对 VM 进行名称解析，请参阅[针对 VM 的名称解析](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。
- 为 VNet 指定 DNS 服务器 IP 地址之后，验证是否生成了 VPN 客户端配置包。 如果更新了 DNS 服务器 IP 地址，请生成并安装新的 VPN 客户端配置包。

如需更多故障排除信息，请参阅[排查到 VM 的远程桌面连接问题](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)。