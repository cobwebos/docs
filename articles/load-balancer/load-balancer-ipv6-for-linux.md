---
title: 为 Linux VM 配置 DHCPv6 | Microsoft Docs
description: 如何为 Linux VM 配置 DHCPv6。
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
keywords: ipv6, azure 负载均衡器, 双堆栈, 公共 ip, 本机 ipv6, 移动, iot
ms.assetid: b32719b6-00e8-4cd0-ba7f-e60e8146084b
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 6248ed2f55fb5bbcc2061af6ce1dedf2bd31ccad
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
ms.locfileid: "30261841"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>为 Linux VM 配置 DHCPv6


Azure Marketplace 中的某些 Linux 虚拟机映像默认未配置动态主机配置协议版本 6 (DHCPv6)。 要支持 IPv6，必须在使用的 Linux OS 分发版中配置 DHCPv6。 各种 Linux 分发会以不同方式配置 DHCPv6，因为它们使用不同的包。

> [!NOTE]
> Azure Marketplace 中最新的 SUSE Linux 和 CoreOS 映像已预先配置 DHCPv6。 使用这些映像不需要进行额外的更改。

本文档介绍如何启用 DHCPv6，使 Linux 虚拟机能够获取 IPv6 地址。

> [!WARNING]
> 不恰当地编辑网络配置文件的操作可能会使你失去 VM 的网络访问权限。 建议先在非生产系统上测试配置更改。 本文中的说明已根据 Azure Marketplace 中的最新版 Linux 映像进行测试。 有关详细说明，请参阅适用于你所用 Linux 版本的文档。

## <a name="ubuntu"></a>Ubuntu

1. 编辑 /etc/dhcp/dhclient6.conf 文件，并添加以下行：

        timeout 10;

2. 编辑使用以下配置的 eth0 接口的网络配置：

   * 在 Ubuntu 12.04 和 14.04 上编辑 /etc/network/interfaces.d/eth0.cfg 文件。 
   * 在 Ubuntu 16.04 上编辑 /etc/network/interfaces.d/50-cloud-init.cfg 文件。

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. 续订 IPv6 地址：

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. 编辑 /etc/dhcp/dhclient6.conf 文件，并添加以下行：

        timeout 10;

2. 编辑 /etc/network/interfaces 文件，添加以下配置：

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. 续订 IPv6 地址：

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL、CentOS 和 Oracle Linux

1. 编辑 /etc/sysconfig/network 文件，添加以下参数：

        NETWORKING_IPV6=yes

2. 编辑 /etc/sysconfig/network-scripts/ifcfg-eth0 文件，添加以下两个参数：

        IPV6INIT=yes
        DHCPV6C=yes

3. 续订 IPv6 地址：

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 和 openSUSE 13

Azure 中最新的 SUSE Linux Enterprise Server (SLES) 和 openSUSE 映像已预先配置 DHCPv6。 使用这些映像不需要进行额外的更改。 如果 VM 基于旧版或自定义的 SUSE 映像，请执行以下步骤：

1. 根据需要安装 `dhcp-client` 包：

    ```bash
    sudo zypper install dhcp-client
    ```

2. 编辑 /etc/sysconfig/network/ifcfg-eth0 文件，添加以下参数：

        DHCLIENT6_MODE='managed'

3. 续订 IPv6 地址：

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 和 openSUSE Leap

Azure 中最新的 SLES 和 openSUSE 映像已预先配置 DHCPv6。 使用这些映像不需要进行额外的更改。 如果 VM 基于旧版或自定义的 SUSE 映像，请执行以下步骤：

1. 编辑 /etc/sysconfig/network/ifcfg-eth0 文件，并使用以下值替换 `#BOOTPROTO='dhcp4'` 参数：

        BOOTPROTO='dhcp'

2. 编辑 /etc/sysconfig/network/ifcfg-eth0 文件，添加以下参数：

        DHCLIENT6_MODE='managed'

3. 续订 IPv6 地址：

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Azure 中最新的 CoreOS 映像已预先配置 DHCPv6。 使用这些映像不需要进行额外的更改。 如果 VM 基于旧版或自定义的 CoreOS 映像，请执行以下步骤：

1. 编辑 /etc/systemd/network/10_dhcp.network 文件：

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. 续订 IPv6 地址：

    ```bash
    sudo systemctl restart systemd-networkd
    ```
