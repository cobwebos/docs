---
title: "StorSimple 8000 Update 0.1 发行说明 | Microsoft Docs"
description: "介绍了 2014 年 10 月版 Microsoft Azure StorSimple (Update 0.1) 的新功能和修复程序、待解决问题以及可用的解决方法。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: fd35e3c3-4770-460c-999d-f72ab7053a20
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: 4dfd3973593a94adfc15a6e15d69c697e13998af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-8000-series-update-01-release-notes--october-2014"></a>StorSimple 8000 系列 Update 0.1 发行说明 – 2014 年 10 月
## <a name="overview"></a>概述
以下发行说明描述了 2014 年 10 月发行的 StorSimple 8000 系列 Update 0.1 的重大待解决问题。 其中也包含此版本中随附的 StorSimple 软件和固件更新的列表。 这是在 StorSimple 8000 系列发行版本于 2014 年 10 月公开发布后的第一个发行版，对应于软件版本 6.3.9600.17312。  

建议在安装设备后立即扫描并应用任何可用的更新。 还可以启用自动更新以在更新发行后立即从 Microsoft 下载并安装高优先级更新。 有关详细信息，请参阅如何[更新 StorSimple 设备](storsimple-update-device.md)。  

在 StorSimple 解决方案中部署更新之前，请查看发行说明中所包含的信息。  

> [!IMPORTANT]
> * 请使用 StorSimple Manager 服务而非 Windows PowerShell for StorSimple 来安装 10 月份的更新。  
> * 更新通常需要花费大约 3 个小时才能完成。  
> * StorSimple 的 10 月份发行版未包含对 StorSimple 虚拟设备的任何更新。 仍然可以应用任何可用的 Windows 更新，包括最新的安全修复程序，但是在版本中不会看到对虚拟设备的更改。  
> 
> 

在更新 StorSimple 设备之前，请确保满足下列先决条件。  

* 在扫描更新前，请确保两个设备控制器都在运行。 如果任何一个控制器未在运行，则扫描会失败。 要验证控制器是否处于正常状态，请在“维护”页中导航到“硬件状态”。 如果存在**需要注意**的组件，在继续进一步操作之前，请联系 Microsoft 支持部门。  
* 确保控制器 0 和控制器 1 的固定 IP 可路由并且可以连接到 Internet，因为它们用于为设备提供更新。 可以使用 [Test-Connection cmdlet](https://technet.microsoft.com/library/hh849808.aspx) 来对网络外部的已知地址（例如 outlook.com）执行 ping 操作，以验证控制器是否具有到外部网络的连接。  
* 请确保所需的出站端口在 StorSimple 设备上可用于出站通信。 有关详细信息，请参阅 [StorSimple 设备的网络要求](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)。  
* 如果设备软件版本早于 6.3.9600.17312（2014 年 10 月版更新），请在开始更新前禁用 Data 2 和 Data 3 端口（若已启用）。 如果在应用更新时让 Data 2 或 Data 3 端口保持启用状态，则可能会导致设备控制器进入恢复模式。 请注意，禁用网络接口时，所有关联的卷都会脱机，并且 I/O 在更新期间会中断。  

## <a name="whats-new-in-the-october-release"></a>10 月份发行版的新增功能
此更新包含以下改进：

* 现在可以使用 StorSimple Manager 服务 UI 管理设备控制器了。 管理操作包括重新启动、关闭或打开控制器。 有关详细信息，请转到[管理 StorSimple 设备控制器](storsimple-manage-device-controller.md)。  
* 可以根据周几和一天中时间的组合计划 WAN 带宽分配。 这样，可以在非高峰时间更好地利用 WAN 带宽。 针对不同的卷容器，可以使用不同的带宽模板。 有关详细信息，请转到[管理 StorSimple 带宽模板](storsimple-manage-bandwidth-templates.md)。  
* 可以配置电子邮件通知来主动向管理员和其他人通知现有的问题或可能将出现的问题。 有关详细信息，请转到[配置警报设置](storsimple-manage-alerts.md#configure-alert-settings)。  

## <a name="issues-fixed-in-the-october-release"></a>10 月份发行版修复的问题
下表提供了在此更新中已修复的问题的摘要。  

| 编号 | 功能 | 问题 | 适用于物理设备 | 适用于虚拟设备 |
| --- | --- | --- | --- | --- |
| 1 |网络接口 |在以前的发行版中，网络接口 DATA 2 和 DATA 3 在软件中是互换的。 此更新中已修复了此问题。 在安装更新之前，请清除设置并禁用这些网络接口。 在安装更新后，必须重新配置这些接口。 |是 |否 |
| #N/A |支持包 |在以前的发行版中，如果运行 Windows PowerShell Export-HcsSupportPackage cmdlet 来检索基板管理控制器 (BMC) 日志，则操作会失败并显示以下警告：“操作在此控制器上成功，但在对等控制器上由于以下错误而失败。 请检查对等节点是否正常以及当前节点是否可以连接到对等节点。” 此问题现在已修复。 |是 |否 |
| 3 |设备故障转移 |在以前的发行版中，如果在设备故障转移期间**搜索备份**作业失败，则可能会出现数据不一致。 此问题现在已修复。 |是 |否 |
| 4 |设备故障转移 |在以前的发行版中，在设备故障转移后，在目标设备上可以看见备份但不存在关联的卷容器。 此问题现在已修复。 |是 |否 |
| 5 |设备故障转移 |在以前的发行版中，在执行注册表还原操作期间在云备份的枚举中存在一个 bug，如果存在云连接问题，这可能会导致数据不一致。 |是 |否 |
| 6 |固件更新 |在以前的发行版中，设备固件更新作业失败并且显示一个错误，指出 cmdlet 不可识别，因此更新失败。 然后，控制器进入了恢复模式。 此问题现在已修复。 |是 |否 |
| 7 |安装 |由在安装期间未正确映像的设备导致的错误现在已修复。 |是 |否 |
| 8 |恢复出厂设置 |对于恢复出厂设置，现在可以跳过固件检查。 这是与以前发行版的一个不同之处。 |是 |否 |
| 9 |恢复出厂设置 |在以前的发行版中，当运行恢复出厂设置 cmdlet 时，只会针对某些硬件组件执行固件版本检查。 其他的固件检查在第一次重新启动开始后执行，这可能会导致恢复出厂设置失败。 此修复确保所有固件检查都在运行恢复出厂设置 cmdlet 时执行，并且是在第一次系统重新启动前执行。 |是 |否 |
| 10 |存储帐户密钥轮换 |用来轮替存储帐户密钥的 **Invoke-HcsmServiceDataEncryptionKeyChange** cmdlet 现在会提示用户输入服务数据加密密钥。 这是与以前发行版的一个不同之处，在以前的发行版中，服务数据加密密钥是作为内联参数传递的。 |是 |否 |
| 11 |24 小时内的故障回复 |在灾难恢复期间，源设备上的清理没有彻底完成，导致故障回复失败。 此发行版中已修复了此问题。 |是 |否 |

## <a name="known-issues-in-the-october-release"></a>10 月份发行版中的已知问题
下表提供了此版本中已知问题的摘要。

| 否。 | 功能 | 问题 | 注释/解决方法 | 适用于物理设备 | 适用于虚拟设备 |
| --- | --- | --- | --- | --- | --- |
| 1 |恢复出厂设置 |在某些情况下，执行恢复出厂设置时，StorSimple 设备可能会卡住并显示以下消息：“正在进行恢复出厂设置(阶段 8)”。 如果在该 cmdlet 正在进行时按 CTRL+C，则会发生此问题。 |在启动恢复出厂设置后不要按 CTRL+C。 如果已处于此状态，请联系 Microsoft 支持部门以执行后续步骤。 |是 |否 |
| #N/A |恢复出厂设置 |不要对从公开发行版更新到 2014 年 10 月版的 StorSimple 设备执行恢复出厂设置。 |只有安装了修补程序后，此操作才起作用。 请联系 Microsoft 支持部门来获取此必需的修补程序。 |是 |否 |
| 3 |磁盘仲裁 |在极少数情况下，如果 8600 设备的 EBOD 机箱中的大部分磁盘断开连接，导致没有磁盘仲裁，然后存储池会脱机。 即使磁盘重新连接，存储池也将保持脱机状态。 |需要重新启动设备。 如果问题仍然存在，请联系 Microsoft 支持部门以了解后续步骤。 |是 |否 |
| 4 |云快照故障 |在极少数情况下，云快照可能会失败并出现错误“达到最大备份限制”。 如果同一设备上从已删除的同一原始卷执行的联机克隆数超过 255，则会发生这种情况。 | |是 |是 |
| 5 |错误的控制器 ID |更换控制器后，控制器 0 可能显示为控制器 1。 在更换控制器的过程中，从对等节点加载映像时，控制器 ID 刚开始可能显示为对等控制器的 ID。 在极少数情况下，此行为也可能在系统重新启动后出现。 |不需要用户操作。 控制器更换过程完成后，这种情况会自动解决。 |是 |否 |
| 6 |设备监视图表 |在 StorSimple Manager 服务中，如果在设备的代理服务器配置中启用“基本”或“NTLM”身份验证，设备监视图表不再工作。 |针对注册到 StorSimple Manager 服务的设备修改 Web 代理配置，将身份验证设置为“无”。 为此，请运行 Windows PowerShell for StorSimple Set-HcsWebProxy cmdlet。 |是 |是 |
| 7 |存储帐户 |此版本不支持使用存储服务删除存储帐户， 否则会导致无法检索用户数据。 | |是 |是 |
| 8 |设备故障转移 |不支持从同一源设备将某个卷容器多次故障转移到不同的目标设备。 |从单个不活动的设备故障转移到多个设备，会使第一个故障转移设备上卷容器丢失数据所有权。 进行此类故障转移后，在 Azure 经典门户中查看这些卷容器时，会发现它们的显示或表现有所不同。 |是 |否 |
| 9 |安装 |安装 StorSimple Adapter for SharePoint 期间，需要提供设备 IP 才能成功完成安装。 | |是 |否 |
| 10 |Web 代理 |如果 Web 代理配置将 HTTPS 作为指定的协议，则设备到服务通信将受到影响，并且设备将进入脱机状态。 在此过程中会生成支持包，从而耗用设备上的大量资源。 |请确保 Web 代理 URL 将 HTTP 作为指定的协议。 详细了解如何[为设备配置 Web 代理](storsimple-configure-web-proxy.md)。 |是 |否 |
| 11 |Web 代理 |如果在注册的设备上配置并启用 Web 代理，将需要重新启动设备上的主动控制器。 | |是 |否 |
| 12 |云高延迟和高 I/O 工作负载 |当 StorSimple 设备同时遇到非常高的云延迟（秒级）和高 I/O 工作负载情况时，设备卷将进入降级状态，并且 I/O 可能会出现故障，发生“设备未就绪”错误。 |需要手动重新启动设备控制器或执行设备故障转移，才可以从这种情况中恢复。 |是 |否 |

## <a name="physical-device-updates-in-the-october-release"></a>10 月份发行版中的物理设备更新
将这些更新应用于物理设备后，软件版本将更改为 6.3.9600.17312。 除非另行指定，否则这些发行说明适用于 StorSimple 设备的所有型号。 有关这些更新的详细信息，请参阅 [2014 年 10 月针对 Microsoft Azure StorSimple 设备的物理设备软件更新](http://support.microsoft.com/kb/2986997)。  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-october-release"></a>10 月份发行版中的串行连接 SCSI (SAS) 控制器和固件更新
此发行版更新了物理设备的 SAS 控制器上的驱动程序和固件。 有关 SAS 控制器更新的详细信息，请参阅 [2014 年 10 月针对 Microsoft Azure StorSimple 设备中的 LSI SAS 控制器的更新](http://support.microsoft.com/kb/2987020)。   

此发行版还应用了一个累积固件更新，该更新解决了设备硬件组件的可靠性问题。 有关该固件更新的详细信息，请参阅 [2014 年 10 月针对 Microsoft Azure StorSimple 设备的固件更新](http://support.microsoft.com/kb/2987015)。  

## <a name="virtual-device-updates-in-the-october-release"></a>10 月份发行版中的虚拟设备更新
此发行版未包含针对虚拟设备的任何更新。 应用此更新不会更改虚拟设备的软件版本。

