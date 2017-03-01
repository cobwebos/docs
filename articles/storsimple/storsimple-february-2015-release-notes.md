---
title: "StorSimple 8000 Update 0.3 发行说明 | Microsoft Docs"
description: "介绍 2015 年 2 月版 Microsoft Azure StorSimple (Update 0.3) 的新功能和修补程序、待解决问题以及可用的解决方法。"
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
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a6956a8fd885251c07c31c4e677dba738ec93c64


---
# <a name="storsimple-8000-series-update-03-release-notes---february-2015"></a>StorSimple 8000 系列 Update 0.3 发行说明 - 2015 年 2月
## <a name="overview"></a>概述
以下发行说明描述了 2015 年 2 月发行的 StorSimple 8000 系列 Update 0.3 的重大待解决问题。 其中也包含此版本中随附的 StorSimple 软件和固件更新的列表。 这是在 StorSimple 8000 系列发行版本于 2014 年 7 月正式发布后的第三个发行版本。

此更新不更改 1 月份更新中的设备软件版本。 其版本仍为 6.3.9600.17312。 查看“上次更新”日期即可确认更新是否已安装。 如果日期为 2/10/2015 或更晚，则更新已成功安装。  

建议在安装 StorSimple 设备后立即扫描并应用任何可用的更新。 还可以启用自动更新，在更新发行后立即从 Microsoft 下载并安装高优先级更新。 有关详细信息，请参阅[更新 StorSimple 设备](storsimple-update-device.md)。  

在 StorSimple 解决方案中部署更新之前，请查看发行说明中所包含的信息。  

> [!IMPORTANT]
> * 请使用 StorSimple Manager 服务而非 Windows PowerShell for StorSimple 安装 2 月份的更新。   
> * 安装此更新约需一小时。 但若要安装累积更新，此过程可能需要约 3 小时才能完成。  
> * StorSimple 的 2 月份发行版未包含对 StorSimple 虚拟设备的任何更新。 用户仍可应用针对虚拟设备的可用 Windows 更新，包括最新的安全修补程序，但虚拟设备的版本不会发生更改。  
> 
> 

请确保在更新 StorSimple 设备之前满足以下先决条件。  

* 在扫描更新前，请确保两个设备控制器都在运行。 如果任一控制器未运行，扫描会失败。 若要验证控制器是否处于正常状态，请在“维护”页中导航到“硬件状态”。 如果存在**需要注意**的组件，请在继续操作之前联系 Microsoft 支持部门。
* 确保控制器 0 和控制器 1 的固定 IP 均可路由并可连接到 Internet，因为向设备提供更新需要使用这些 IP。 可以使用 [Test-Connection cmdlet](https://technet.microsoft.com/library/hh849808.aspx) ping 网络之外的已知地址（例如 outlook.com），确保控制器能够连接到外部网络。
* 确保端口 80 和 443 在 StorSimple 设备上可用于出站通信。 有关详细信息，请参阅 [StorSimple 设备的网络要求](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)。
* 如果设备软件版本早于 6.3.9600.17312（2014 年 10 月版更新），请在开始更新前禁用 Data 2 和 Data 3 端口（若已启用）。 在应用更新时让 Data 2 或 Data 3 端口处于启用状态可能导致设备控制器进入恢复模式。 请注意，禁用网络接口时，所有关联的卷都会脱机，I/O 在更新期间会中断。  

## <a name="whats-new-in-the-february-release"></a>2 月份发行版的新增功能
此更新包含对出厂重置问题的修补程序，该问题发生在从 GA 版升级到 2014 年 10 月版的设备上。 有关详细信息，请参阅[此版本修复的问题](#issues-fixed-in-the-february-release)。   

此更新不包含新特性或新功能。  

## <a name="issues-fixed-in-the-february-release"></a>2 月份发行版修复的问题
下表说明了此更新修复的问题。  

| 不能。 | 功能 | 问题 | 适用于物理设备 | 适用于虚拟设备 |
| --- | --- | --- | --- | --- |
| 1 |恢复出厂设置 |用户尝试在最初安装了 GA 版（6.3.9600.17215 版）但后来又更新到了 10 月份发行版（6.3.9600.17312 版）的设备上执行出厂重置。 出厂重置失败，设备变得不稳定。 |是 |否 |

## <a name="known-issues-in-the-february-release"></a>2 月份发行版的已知问题
下表提供此版本中已知问题的摘要。

| 否。 | 功能 | 问题 | 注释/解决方法 | 适用于物理设备 | 适用于虚拟设备 |
| --- | --- | --- | --- | --- | --- |
| 1 |恢复出厂设置 |在某些情况下，执行出厂重置时，StorSimple 设备会卡住并显示以下消息：“正在进行出厂重置(阶段 8)”。 如果在该 cmdlet 正在运行时按下 CTRL+C，就会发生这种情况。 |请勿在启动出厂重置后按 CTRL+C。 如果已处于此状态，请联系 Microsoft 支持部门以执行后续步骤。 |是 |否 |
| 2 |磁盘仲裁 |在极少数情况下，如果 8600 设备的 EBOD 机箱中的大部分磁盘断开连接，导致没有磁盘仲裁，则存储池会脱机。 即使磁盘重新连接，存储池也将保持脱机状态。 |需要重新启动设备。 如果问题仍然存在，请联系 Microsoft 支持部门以了解后续步骤。 |是 |否 |
| 3 |云快照失败 |在极少数情况下，云快照可能会失败并出现错误“达到最大备份限制”。 如果同一设备上的联机克隆数超出 255，则会发生这种情况，无法从已删除的原始卷执行云快照操作。 | |是 |是 |
| 4 |错误的控制器 ID |当执行控制器更换时，控制器 0 可能显示为控制器 1。 控制器更换过程中，从对等节点加载图像时，控制器 ID 可能最初显示为对等控制器的 ID。 在极少数情况下，此行为也可能在系统重新启动后出现。 |不需要用户操作。 控制器更换过程完成后，这种情况将自动解决。 |是 |否 |
| 5 |设备监视图表 |在 StorSimple Manager 服务中，如果在设备的代理服务器配置中启用“基本”或“NTLM”身份验证，设备监视图表将不起作用。 |针对注册到 StorSimple Manager 服务的设备修改 Web 代理配置，将身份验证设置为 NONE。 为此，请运行 Windows PowerShell for StorSimple Set-HcsWebProxy cmdlet。 |是 |是 |
| 6 |存储帐户 |使用存储服务删除存储帐户是一个不受支持的方案。 这将导致无法在其中检索用户数据的情况。 | |是 |是 |
| 7 |设备故障转移 |不支持从同一源设备将某个卷容器多次故障转移到不同的目标设备。    从单个不活动的设备故障转移到多个设备，会使第一个故障转移设备上卷容器丢失数据所有权。 进行此类故障转移后，当你在 Azure 经典门户中查看这些卷容器时，会发现它们的显示或表现有所不同。 | |是 |否 |
| 8 |安装 |安装 StorSimple Adapter for SharePoint 期间，你需要提供设备 IP 才能成功完成安装。 | |是 |否 |
| 9 |Web 代理 |如果 Web 代理服务器配置将 HTTPS 作为指定的协议，则设备到服务通信将受到影响，并且设备将进入脱机状态。 在此过程中，会在进程中生成支持包，耗用设备上的大量资源。 |请确保 Web 代理 URL 将 HTTP 作为指定的协议。 详细了解如何[配置设备的 Web 代理](storsimple-configure-web-proxy.md)。 |是 |否 |
| 10 |Web 代理 |如果在注册的设备上配置并启用 Web 代理，将需要重新启动设备上的主动控制器。 | |是 |否 |
| 11 |云高延迟和高 I/O 工作负载 |当 StorSimple 设备同时遇到非常高的云延迟（大约秒数）和高 I/O 工作负载情况时，设备卷将进入降级状态，并且 I/O 可能会出现故障，发生“设备未就绪”错误。 |需要手动重新启动设备控制器或执行设备故障转移，才可以从这种情况中恢复。 |是 |否 |

## <a name="physical-device-updates-in-the-february-release"></a>2 月份发行版中的物理设备更新
此更新修复了出厂重置问题，该问题发生在从 GA 版升级到 2014 年 10 月版的设备上。 它不包含对 StorSimple 设备的任何其他更新。  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-february-release"></a>2 月份发行版中的串行连接 SCSI (SAS) 控制器和固件更新
此发行版不包含对串行连接 SCSI (SAS) 控制器或固件的任何更新。 驱动程序更新位于 2014 年 10 月版中。  

## <a name="virtual-device-updates-in-the-february-release"></a>2 月份发行版中的虚拟设备更新
此发行版不包含虚拟设备的任何更新。 应用此更新不会更改虚拟设备的软件版本。




<!--HONumber=Nov16_HO3-->


