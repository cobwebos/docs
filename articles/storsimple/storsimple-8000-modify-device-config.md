---
title: "修改 StorSimple 8000 系列设备配置 | Microsoft Docs"
description: "介绍如何使用 StorSimple 设备管理器服务重新配置已部署的 StorSimple 设备。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/19/2017
ms.author: alkohli
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: 01e1e7447d6951d1b2c89f3b0ef726af9c03fd66
ms.contentlocale: zh-cn
ms.lasthandoff: 07/20/2017

---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>使用 StorSimple 设备管理器服务修改 StorSimple 设备配置

## <a name="overview"></a>概述

Azure 门户“设置”边栏选项卡中的“设备设置”部分包含可以在由 StorSimple 设备管理器服务管理的 StorSimple 设备上重新配置的所有设备参数。 本教程介绍如何使用“设置”边栏选项卡执行以下设备级任务：

* 修改设备的友好名称
* 修改设备时间设置
* 分配辅助 DNS
* 修改网络接口
* 交换或重新分配 IP

## <a name="modify-device-friendly-name"></a>修改设备的友好名称

可以使用 Azure 门户更改设备名称并为其分配选择的唯一友好名称。 使用设备上的“常规设置”边栏选项卡修改设备的友好名称。 友好名称可以包含任何字符并且最大长度为 64 个字符。

> [!NOTE] 
> 只能在完成设备设置之前，在 Azure 门户中修改设备名称。 完成最低要求的设备设置后，无法更改设备名称。

![“常规设置”中的设备名称](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

连接到 StorSimple 设备管理器服务的 StorSimple 设备分配有一个默认名称。 该默认名称通常反映设备的序列号。 例如，长度为 15 个字符的默认设备名称（如 8600-SHX0991003G44HT）表示以下含义：

* **8600**  – 表示设备型号。
* **SHX** – 表示制造地点。
* **0991003** - 表示特定产品。
* **G44HT**- 最后 5 位是递增的，以便创建唯一的序列号。 这可能不是一个连续集合。

## <a name="modify-device-description"></a>修改设备说明

使用设备上的“常规设置”边栏选项卡修改设备的说明。

![“常规设置”中的设备说明](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

设备说明通常用来帮助识别设备的所有者和物理位置。 说明字段包含的字符数必须少于 256 个。

## <a name="modify-time-settings"></a>修改时间设置

你的设备必须对时间进行同步，才能使用云存储服务提供程序进行身份验证。 使用设备上的“常规设置”边栏选项卡修改设备时间设置。

![“常规设置”中的设备说明](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 从下拉列表中选择所在的时区。 最多可以指定两个网络时间协议 (NTP) 服务器：

 - **主 NTP 服务器** - 该配置是必需的，并且是在使用用于 StorSimple 的 Windows PowerShell 配置设备时指定的。 可以将默认 Windows Server **time.windows.com** 指定为 NTP 服务器。 可以通过 Azure 门户查看主 NTP 服务器配置，但若要更改此配置，必须使用 Windows PowerShell 界面。 使用 `Set-HcsNTPClientServerAddress` cmdlet 修改设备的主 NTP 服务器。 有关详细信息，请参阅 [Set-HcsNTPClientServerAddress] (https://technet.microsoft.com/library/dn688138.aspx) cmdlet 的语法。

- **辅助 NTP 服务器** - 该配置是可选的。 可以使用门户来配置辅助 NTP 服务器。

在配置 NTP 服务器时，请确保网络允许 NTP 流量从数据中心传递到 Internet。 在指定公共 NTP 服务器时，必须确保网络防火墙和其他安全设备配置为允许将 NTP 流量传送到/传送出外部网络。 如果不允许双向 NTP 流量，则必须使用内部 NTP 服务器（一台 Windows 域控制器提供此功能）。 如果设备无法对时间进行同步，则它可能不能与云存储提供程序进行通信。

若要查看公共 NTP 服务器的列表，请转到 [NTP 服务器 Web](http://support.ntp.org/bin/view/Servers/WebHome)。

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>如果将设备部署在其他时区中，将会发生什么？

如果将设备部署在其他时区中，则设备时区将更改。 假定所有备份策略都使用设备时区，则备份策略将根据新时区自动调整。 无需用户干预。

## <a name="modify-dns-settings"></a>修改 DNS 设置

当设备尝试与云存储服务提供程序进行通信时将使用 DNS 服务器。 使用设备上的“网络设置”边栏选项卡查看和修改已配置的 DNS 设置。 

![“网络设置”中的 DNS 设置](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

为实现高可用性，在初始设备部署期间必须配置主 DNS 服务器和辅 DNS 服务器。

**主 DNS 服务器** - 在初始设置期间，使用用于 StorSimple 的 Windows PowerShell 来首先指定主 DNS 服务器。 只能通过 Windows PowerShell 界面重新配置主 DNS 服务器。 使用 `Set-HcsDNSClientServerAddress` cmdlet 修改设备的主 DNS 服务器。 有关详细信息，请参阅 [Set-HcsDNSClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) cmdlet 的语法。

**辅助 DNS 服务器** - 若要修改辅助 DNS 服务器，请在设备的 Windows PowerShell 界面中使用 `Set-HcsDNSClientServerAddress` cmdlet，或者在 Azure 门户中使用 StorSimple 设备的“网络设置”边栏选项卡。

若要在 Azure 门户中修改辅助 DNS 服务器，请执行以下步骤。

1. 转到 StorSimple Device Manager 服务。 在设备列表中，选择并单击自己的设备。

2. 在“设置”边栏选项卡中，转到“设备设置”>“网络”。 此时会打开“网络设置”边栏选项卡。 单击“DNS 设置”磁贴。 修改辅助 DNS 服务器的 IP 地址。

    ![修改辅助 DNS 服务器的 IP 地址](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. 在命令栏中单击“保存”，出现确认提示时，单击“确定”。

    ![保存并确认更改](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>修改网络接口

你的设备具有六个设备网络接口，其中四个为 1 GbE 网络接口，另外两个为 10 GbE 网络接口。 这些接口标记为 DATA 0 – DATA 5。 DATA 0、DATA 1、DATA 4 和 DATA 5 是 1 GbE 网络接口，而 DATA 2 和 DATA 3 是 10 GbE 网络接口。

使用“网络设置”边栏选项卡配置每个要使用的接口。

![通过“网络设置”配置网络接口](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

为确保高可用性，建议你的设备上至少存在两个 iSCSI 接口和两个已启用云的接口。 建议你禁用未使用的接口，但这不是必需的。

对于每个网络接口，将显示以下参数：

* **速度** – 不是用户可配置参数。 DATA 0、DATA 1、DATA 4 和 DATA 5 始终是 1 GbE 接口，而 DATA 2 和 DATA 3 是 10 GbE 接口。
  
  > [!NOTE]
  > 速度和双工模式始终是自动协商的。 不支持极大帧。
  
* **接口状态** – 接口可以处于启用或禁用状态。 如果已启用，则设备将尝试使用该接口。 建议仅启用连接到网络并且要使用的那些接口。 请禁用未使用的任何接口。
* **接口类型** – 此参数可用来将 iSCSI 流量与云存储流量相隔离。 此参数可以是以下项之一：
  
  * **已启用云** – 当启用后，设备将使用此接口来与云进行通信。
  * **已启用 iSCSI** – 当启用后，设备将使用此接口来与 iSCSI 主机进行通信。
    
    建议你将 iSCSI 流量与云存储流量相隔离。 另请注意，如果主机与设备在同一子网内，则不需要分配网关；但是，如果主机与设备在不同的子网中，则需要分配网关。
* **IP 地址** - 配置任何网络接口时，必须配置虚拟 IP (VIP)。 这可以是 IPv4 和/或 IPv6 地址。 设备网络接口同时支持 IPv4 和 IPv6 地址系列。 当使用 IPv4 时，请采用点分十进制表示法指定 32 位 IP 地址 (*xxx.xxx.xxx.xxx*)。 当使用 IPv6 时，只需要提供一个 4 位的前缀，将会基于该前缀自动为设备网络接口生成一个 128 位的地址。
* **子网** – 这指的是子网掩码，并且是通过 Windows PowerShell 接口配置的。
* **网关** – 这是当此接口尝试与不在同一 IP 地址空间（子网）内的节点进行通信时应该使用的默认网关。 默认网关必须与接口 IP 地址在同一地址空间（子网）内，如子网掩码所确定。
* **固定 IP 地址** – 只有配置 DATA 0 接口时，此字段才可用。 要执行更新或设备故障排除之类的操作，可能需要直接连接到设备控制器。 固定 IP 地址可以用来访问设备上的主动和被动控制器。

> [!NOTE]
> * 为确保正确操作，请在每个设备接口连接到的交换机上验证接口速度和双工模式。 应当与千兆位以太网 (1000 Mbps) 协商或者为其配置交换机接口，并且这些接口应当是全双工的。 接口以较低的速度或者以半双工模式运行将导致性能问题。
> * 为了尽量减少中断和停机时间，建议你在设备的 iSCSI 网络接口将连接到的每个交换机端口上启用 portfast。 这将确保在发生故障转移时可以快速建立网络连接。

### <a name="configure-data-0"></a>配置 DATA 0

默认情况下，DATA 0 已启用云。 配置 DATA 0 时，还必须配置两个固定 IP 地址，每台控制器一个。 这些固定 IP 地址可以用来直接访问设备控制器，并且当在设备上安装更新或者当访问控制器进行故障排除时非常有用。

可以通过 DATA 0 设置边栏选项卡重新配置固定 IP 控制器。

![配置网络接口 - DATA 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> 控制器的固定 IP 地址用于为设备提供更新。 因此，这些固定 IP 必须可路由并能够连接到 Internet。

### <a name="configure-data-1---data-5"></a>配置 DATA 1 - DATA 5

对于 DATA 1 - DATA 5 网络接口，可按以下屏幕截图中所示配置所有网络设置：

![配置网络接口 DATA 1 - DATA 5](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>交换或重新分配 IP

目前，如果为控制器上的任何网络接口分配了在使用中的 VIP（由同一设备或网络中的其他设备使用的），则控制器将进行故障转移。 如果交换或重新分配设备网络接口的 VIP，必须遵循正确的过程，就像是创建重复 IP 一样。

可以执行以下步骤来为任何网络接口交换或重新分配 VIP：

#### <a name="to-reassign-ips"></a>重新分配 IP

1. 清除两个接口的 IP 地址。
2. 在清除 IP 地址后，为相应的接口分配新的 IP 地址。

## <a name="next-steps"></a>后续步骤

* 了解如何[为 StorSimple 设备配置 MPIO](storsimple-8000-configure-mpio-windows-server.md)。
* 了解如何[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)。


