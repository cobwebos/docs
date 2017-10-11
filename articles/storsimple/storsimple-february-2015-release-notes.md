---
title: "StorSimple 8000 更新 0.3 发行说明 |Microsoft 文档"
description: "描述为 2015 年 2 月的新功能和修补程序、 打开的问题和可用的解决方法 Microsoft Azure StorSimple 版本 (Update 0.3)。"
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: b01bfd04-f9f8-45f4-ade8-95ac2b979e6a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: v-sharos
ms.openlocfilehash: c059fd74854813615754e67547497b7ededbe4dd
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="storsimple-8000-series-update-03-release-notes---february-2015"></a>StorSimple 8000 系列更新 0.3 发行说明-2015 年 2 月
## <a name="overview"></a>概述
以下发行说明标识用于 StorSimple 8000 系列更新 0.3 在 2015 年 2 月发布的重要待解决问题。 它们还包含此版本中包括的 StorSimple 软件和固件更新的列表。 StorSimple 8000 系列发行版本已建立在 2014 年 7 月正式发布之后，这是第三个版本。

此更新不会从年 1 月更新版更改设备软件版本。 它将继续版本 6.3.9600.17312。 你可以确认更新是否已安装程序通过检查**上次更新时间**日期。 如果日期为 2015 年 2 月 10 日或更高版本，然后更新已安装成功。  

我们建议你扫描，并在安装 StorSimple 设备之后立即应用所有可用更新。 你还可以启用自动更新，以下载并安装来自 Microsoft 的高优先级更新，就会立即发布它们。 有关详细信息，请参阅[更新你的 StorSimple 设备](storsimple-update-device.md)。  

请查看在 StorSimple 解决方案中部署更新之前发行说明中包含的信息。  

> [!IMPORTANT]
> * 使用 StorSimple 管理器服务并不是 Windows PowerShell for StorSimple 安装年 2 月更新。   
> * 它采用大约一小时才能安装此更新。 但是，如果你要安装累积更新，过程可能需要大约 3 个小时才能完成。  
> * StorSimple 2 月版不包含 StorSimple 虚拟设备的任何更新。 仍可以任何可用 Windows 更新适用于虚拟设备，包括最新安全修补程序，但你看不到虚拟设备的版本中的更改。  
> 
> 

请确保在更新 StorSimple 设备之前已满足以下先决条件。  

* 请确保在扫描更新之前运行两个设备控制器。 如果任一控制器未运行，扫描将会失败。 若要验证控制器是否处于正常状态，请导航到**硬件状态**下**维护**页。 如果存在组件，**需要关注**，在继续操作之前联系 Microsoft 支持。
* 确保为控制器 0 和控制器 1 固定的 Ip 可路由，并且可以连接到 Internet，因为它们用于为设备提供更新。 你可以使用[Test-connection cmdlet](https://technet.microsoft.com/library/hh849808.aspx)外部网络 （如 outlook.com) 若要验证控制器是否能连接到外部网络的地址执行 ping 操作。
* 确保端口 80 和 443 的出站通信 StorSimple 设备上可用。 有关详细信息，请参阅[StorSimple 设备的网络要求](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)。
* 如果启用，在开始更新之前，如果设备软件版本低于 6.3.9600.17312 （2014 年 10 月更新），禁用 Data 2 和 Data 3 端口。 离开 Data 2 或 Data 3 端口启用应用更新时可能会导致你的设备控制器转为恢复模式。 请注意，当禁用网络接口时，会将所有关联的卷脱机，更新期间将中断 I/o。  

## <a name="whats-new-in-the-february-release"></a>什么是年 2 月发行版中新增
有关出厂重置发生的问题，此更新包含修复设备从 GA 已升级到 2014 年 10 月发行版发行。 有关详细信息，请参阅[此版本中修复问题](#issues-fixed-in-the-february-release)。   

此更新不包含新功能。  

## <a name="issues-fixed-in-the-february-release"></a>年 2 月版本中修复的问题
下表描述了已在此更新中修复该问题。  

| 否。 | 功能 | 问题 | 适用于物理设备 | 适用于虚拟设备 |
| --- | --- | --- | --- | --- |
| 1 |恢复出厂设置 |你尝试执行恢复出厂设置的设备最初 GA 版 （版本 6.3.9600.17215） 安装，但已更新到年 10 月版 （版本 6.3.9600.17312）。 出厂重置失败和设备变得不稳定。 |是 |否 |

## <a name="known-issues-in-the-february-release"></a>年 2 月版本中已知的问题
下表提供了此版本中已知问题的摘要。

| 否。 | 功能 | 问题 | 注释/解决方法 | 适用于物理设备 | 适用于虚拟设备 |
| --- | --- | --- | --- | --- | --- |
| 1 |恢复出厂设置 |在某些情况下，当你执行出厂重置，StorSimple 设备可能死机，并且显示此消息：**出厂重置正在进行中 （阶段 8）**。 如果正在 cmdlet 时按 CTRL + C，将发生这种情况。 |不要在启动出厂重置后按 CTRL + C。 如果已在此状态中，请联系 Microsoft 支持部门以后续步骤。 |是 |否 |
| 2 |磁盘仲裁 |在极少数情况下，如果的大部分磁盘 8600device 的 EBOD 机箱中断开连接，因而没有磁盘仲裁，则存储池将脱机。 即使磁盘重新连接，则它将保持脱机状态。 |你将需要重新启动设备。 如果问题仍然存在，请联系 Microsoft 支持部门以后续步骤。 |是 |否 |
| 3 |云快照故障 |在极少数情况下，云快照可能会失败并出现错误**达到的最大备份限制**。 如果超过 255 在同一设备上，从已删除原始卷的联机克隆，将发生这种情况。 | |是 |是 |
| 4 |错误的控制器 ID |当执行控制器更换时，控制器 0 可能显示为控制器 1。 控制器更换过程中从对等节点加载映像时的控制器 ID 可能最初显示作为对等控制器的 id。 在极少数情况下，也可能在系统重启后出现此行为。 |无需用户操作。 控制器更换完成后，这种情况下将自行解决。 |是 |否 |
| 5 |设备监视图 |在 StorSimple Manager 服务中，设备监视图无法使用基本或 NTLM 身份验证启用设备的代理服务器配置中。 |修改设备注册与你的 StorSimple Manager 服务，因此该身份验证设置为 NONE 的 web 代理配置。 若要执行此操作，运行 Windows PowerShell for StorSimple Set-hcswebproxy cmdlet。 |是 |是 |
| 6 |存储帐户 |使用存储服务删除存储帐户是一个不受支持的方案。 这将导致不能在其中检索用户数据的情况。 | |是 |是 |
| 7 |设备故障转移 |不支持从同一源设备到不同的目标设备的卷容器的多次故障转移。    从单个死设备故障转移到多个设备将在第一个故障转移的设备的卷容器丢失数据所有权。 此类故障转移后，这些卷容器将显示，或当你在 Azure 经典门户中查看它们时以不同方式行为。 | |是 |否 |
| 8 |安装 |在 StorSimple Adapter for SharePoint 安装，你需要提供设备 IP 才能成功完成安装的顺序。 | |是 |否 |
| 9 |Web 代理 |如果你的 web 代理配置具有 HTTPS 作为指定的协议，然后将影响设备到服务通信，并且设备将处于脱机状态。 在过程中，使用你的设备上的重要资源还会生成支持包。 |请确保 web 代理 URL 具有 HTTP 作为指定的协议。 有关如何的详细信息[配置你的设备的 web 代理](storsimple-configure-web-proxy.md)。 |是 |否 |
| 10 |Web 代理 |如果你配置并启用已注册的设备上的 web 代理，你将需要重新启动你的设备上的主动控制器。 | |是 |否 |
| 11 |高云延迟和高 I/O 工作负荷 |当你的 StorSimple 设备遇到极高的云延迟 （几秒钟） 和高 I/O 工作负荷的组合时，设备卷将进入降级状态，并且 I/o 可能会失败并出现"设备未就绪"错误。 |你将需要手动重新启动设备控制器或执行设备故障转移从这种情况中恢复。 |是 |否 |

## <a name="physical-device-updates-in-the-february-release"></a>发布年 2 月中的物理设备更新
此更新解决了发生的出厂重置问题已从 GA 已升级的设备释放到 2014 年 10 月发行版。 它不包含任何其他更新到 StorSimple 设备。  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-february-release"></a>发布的串行连接 SCSI (SAS) 控制器和年 2 月中的固件更新
此版本不包含对串行连接 SCSI (SAS) 控制器或固件的任何更新。 驱动程序更新时在 10 月，2014年版本。  

## <a name="virtual-device-updates-in-the-february-release"></a>发布年 2 月中的虚拟设备更新
此版本不包含任何虚拟设备的更新。 应用此更新不会更改虚拟设备的软件版本。

