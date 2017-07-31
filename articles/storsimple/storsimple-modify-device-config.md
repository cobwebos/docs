---
title: "修改 StorSimple 设备配置 | Microsoft Docs"
description: "介绍了如何使用 StorSimple Manager 服务重新配置已部署的 StorSimple 设备。"
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: bb679264-8d46-429c-9ef7-630dc3b4a423
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/19/2017
ms.author: v-sharos
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: 5bfbeb038dbedae2bf77016abbc19458c3dc22c9
ms.contentlocale: zh-cn
ms.lasthandoff: 07/20/2017

---
# <a name="use-the-storsimple-manager-service-to-modify-your-storsimple-device-configuration"></a>使用 StorSimple Manager 服务修改 StorSimple 设备配置
## <a name="overview"></a>概述
Azure 经典门户“配置”页包含你可以在由 StorSimple Manager 服务管理的 StorSimple 设备上重新配置的所有设备参数。 本教程介绍了如何使用“配置”页执行以下设备级任务：

* 修改设备设置 
* 修改时间设置 
* 修改 DNS 设置 
* 修改网络接口
* 交换或重新分配 IP

## <a name="modify-device-settings"></a>修改设备设置
设备设置包括设备的友好名称和设备说明。

> [!NOTE] 
> 无法在 Azure 经典门户中修改设备名称。 不支持重命名设备。

连接到 StorSimple Manager 服务的 StorSimple 设备将分配有一个默认名称。 该默认名称通常反映设备的序列号。 例如，长度为 15 个字符的默认设备名称（如 8600-SHX0991003G44HT）表示以下含义：

* **8600**  – 表示设备型号。
* **SHX** – 表示制造地点。
* **0991003** - 表示特定产品。
* **G44HT**- 最后 5 位是递增的，以便创建唯一的序列号。 这可能不是一个连续集合。

可以指定设备说明。 设备说明通常用来帮助识别设备的所有者和物理位置。 说明字段包含的字符数必须少于 256 个。

## <a name="modify-time-settings"></a>修改时间设置
你的设备必须对时间进行同步，才能使用云存储服务提供程序进行身份验证。 从下拉列表中选择时区，并指定最多两台网络时间协议 (NTP) 服务器。 主 NTP 服务器是必需的，并且是在使用 Windows PowerShell for StorSimple 配置设备时指定的。 可以将默认 Windows Server **time.windows.com** 指定为 NTP 服务器。 可以通过 Azure 经典门户查看主 NTP 服务器配置，但是若要更改该配置，必须使用 Windows PowerShell 接口。

辅 NTP 服务器配置是可选的。 可以使用经典门户来配置辅 NTP 服务器。 

在配置 NTP 服务器时，请确保网络允许 NTP 流量从数据中心传递到 Internet。 在指定公共 NTP 服务器时，必须确保网络防火墙和其他安全设备配置为允许将 NTP 流量传送到/传送出外部网络。 如果不允许双向 NTP 流量，则必须使用内部 NTP 服务器（一台 Windows 域控制器提供此功能）。 如果设备无法对时间进行同步，则它可能不能与云存储提供程序进行通信。

若要查看公共 NTP 服务器的列表，请转到 [NTP 服务器 Web](http://support.ntp.org/bin/view/Servers/WebHome)。 

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>如果将设备部署在其他时区中，将会发生什么？
如果将设备部署在其他时区中，则设备时区将更改。 假定所有备份策略都使用设备时区，则备份策略将根据新时区自动调整。 无需用户干预。

## <a name="modify-dns-settings"></a>修改 DNS 设置
当设备尝试与云存储服务提供程序进行通信时将使用 DNS 服务器。 为实现高可用性，在初始设备部署期间必须配置主 DNS 服务器和辅 DNS 服务器。 若要重新配置主 DNS 服务器，将需要在 StorSimple 设备上使用 Windows PowerShell 接口。

若要修改辅 DNS 服务器，可以使用 Azure 经典门户。

## <a name="modify-network-interfaces"></a>修改网络接口
你的设备具有六个设备网络接口，其中四个为 1 GbE 网络接口，另外两个为 10 GbE 网络接口。 这些接口标记为 DATA 0 – DATA 5。 DATA 0、DATA 1、DATA 4 和 DATA 5 是 1 GbE 网络接口，而 DATA 2 和 DATA 3 是 10 GbE 网络接口。

为要使用的每个接口配置**网络接口设置**。 为确保高可用性，建议你的设备上至少存在两个 iSCSI 接口和两个已启用云的接口。 建议你禁用未使用的接口，但这不是必需的。

配置任何网络接口时，必须配置虚拟 IP (VIP)。

默认情况下，DATA 0 已启用云。 配置 DATA 0 时，还必须配置两个固定 IP 地址，每台控制器一个。 这些固定 IP 地址可以用来直接访问设备控制器，并且当在设备上安装更新或者当访问控制器进行故障排除时非常有用。

在 StorSimple 8000 系列 Update 1 中，DATA 0 的路由跃点数设置为最低；因此，如果你的设备正在运行 StorSimple 8000 系列 Update 1，则所有云流量都将通过 DATA 0 进行路由。 如果你的 StorSimple 设备上有多个已启用云的网络接口，请注意这一点。

> [!NOTE]
> 控制器的固定 IP 地址用于为设备提供更新。 因此，这些固定 IP 必须可路由并能够连接到 Internet。
> 
> 

对于每个网络接口，将显示以下参数：

* **速度** – 不是用户可配置参数。 DATA 0、DATA 1、DATA 4 和 DATA 5 始终是 1 GbE 接口，而 DATA 2 和 DATA 3 是 10 GbE 接口。
  
  > [!NOTE]
  > 速度和双工模式始终是自动协商的。 不支持极大帧。
  > 
  > 
* **接口状态** – 接口可以处于启用或禁用状态。 如果已启用，则设备将尝试使用该接口。 建议仅启用连接到网络并且要使用的那些接口。 请禁用未使用的任何接口。
* **接口类型** – 此参数可用来将 iSCSI 流量与云存储流量相隔离。 此参数可以是以下项之一：
  
  * **已启用云** – 当启用后，设备将使用此接口来与云进行通信。
  * **已启用 iSCSI** – 当启用后，设备将使用此接口来与 iSCSI 主机进行通信。
    
    建议你将 iSCSI 流量与云存储流量相隔离。 另请注意，如果主机与设备在同一子网内，则不需要分配网关；但是，如果主机与设备在不同的子网中，则需要分配网关。
* **IP 地址** – 这可以是 IPv4 或 IPv6 地址或两者。 设备网络接口同时支持 IPv4 和 IPv6 地址系列。 当使用 IPv4 时，请采用点分十进制表示法指定 32 位 IP 地址 (*xxx.xxx.xxx.xxx*)。 当使用 IPv6 时，只需要提供一个 4 位的前缀，将会基于该前缀自动为设备网络接口生成一个 128 位的地址。
* **子网** – 这指的是子网掩码，并且是通过 Windows PowerShell 接口配置的。
* **网关** – 这是当此接口尝试与不在同一 IP 地址空间（子网）内的节点进行通信时应该使用的默认网关。 默认网关必须与接口 IP 地址在同一地址空间（子网）内，如子网掩码所确定。
* **固定 IP 地址** – 只有配置 DATA 0 接口时，此字段才可用。 要执行更新或设备故障排除之类的操作，可能需要直接连接到设备控制器。 固定 IP 地址可以用来访问设备上的主动和被动控制器。

可以通过 Azure 经典门户重新配置控制器 0 和控制器 1。

> [!NOTE]
> * 为确保正确操作，请在每个设备接口连接到的交换机上验证接口速度和双工模式。 应当与千兆位以太网 (1000 Mbps) 协商或者为其配置交换机接口，并且这些接口应当是全双工的。 接口以较低的速度或者以半双工模式运行将导致性能问题。
> * 为了尽量减少中断和停机时间，建议你在设备的 iSCSI 网络接口将连接到的每个交换机端口上启用 portfast。 这将确保在发生故障转移时可以快速建立网络连接。
> 
> 

## <a name="swap-or-reassign-ips"></a>交换或重新分配 IP
目前，如果为控制器上的任何网络接口分配了在使用中的 VIP（由同一设备或网络中的其他设备使用的），则控制器将进行故障转移。 因此，如果为设备网络接口交换 VIP，必须遵循正确的过程，因为这会导致 IP 重复情形。

可以执行以下步骤来为任何网络接口交换或重新分配 VIP：

#### <a name="to-reassign-ips"></a>重新分配 IP
1. 清除两个接口的 IP 地址。
2. 在清除 IP 地址后，为相应的接口分配新的 IP 地址。

## <a name="next-steps"></a>后续步骤
* 了解如何[为 StorSimple 设备配置 MPIO](storsimple-configure-mpio-windows-server.md)。
* 了解如何[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)。


