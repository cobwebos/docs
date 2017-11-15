---
title: "StorSimple 8000 Update 0.2 发行说明 | Microsoft Docs"
description: "介绍 2015 年 1 月版 Microsoft Azure StorSimple (Update 0.2) 的新功能和修补程序、待解决问题以及可用的解决方法。"
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: d9684ae3-b38f-4678-9d70-e5dbc6b03350
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: f2b147f32f10208b8daa391095a7d0094a0c2c09
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2017
---
# <a name="storsimple-8000-series-update-02-release-notes---january-2015"></a>StorSimple 8000 系列 Update 0.2 发行说明 - 2015 年 1 月
> [!NOTE]
> 已弃用 StorSimple 经典门户。 根据弃用计划，StorSimple 设备管理器将自动迁移至新的 Azure 门户。 你将收到有关此移动的电子邮件和门户通知。 另外，此文档也将在稍后停用。 任何与移动相关的问题，请参阅[常见问题解答：转移到 Azure 门户](storsimple-8000-move-azure-portal-faq.md)。

## <a name="overview"></a>概述
以下发行说明描述了 2015 年 1 月发行的 Microsoft Azure StorSimple 的重大待解决问题。 其中也包含此版本中随附的 StorSimple 软件和固件更新的列表。 这是在 StorSimple 8000 系列发行版本于 2014 年 7 月正式发布后的第二个发行版本。

此更新不更改 10 月份更新中的物理设备软件版本。 其版本仍为 6.3.9600.17312。 虚拟设备映像所使用的映像在此版本中已更改。 因此，所有在 2015 年 1 月 20 日后新建的虚拟设备都会显示版本 6.3.9600.17361。  

请查看包含在 2015 年 1 月份更新的发行说明中的以下信息。

> [!IMPORTANT]
> * 此更新不通过 Windows 更新提供，不能像其他更新一样安装。 用户的设备不会收到此更新，即使用户已通过 Azure 经典门户应用了相关更新。 此更新将只适用于 2015 年 1 月 20 日后创建的虚拟设备。 
> * StorSimple 的 1 月份发行版未包含对 StorSimple 物理设备的任何更新。 用户仍可应用针对虚拟设备的可用 Windows 更新，包括最新的安全修补程序，但 StorSimple 物理设备的版本不会发生更改。
> 
> 

## <a name="whats-new-in-the-january-release"></a>1 月份发行版的新增功能
此更新包含的修补程序与在虚拟设备上脱机的卷相关。 （请参阅[此版本修复的问题](#issues-fixed-in-the-january-release)。）  

此更新不包含新特性或新功能。  

## <a name="issues-fixed-in-the-january-release"></a>1 月份发行版修复的问题
下表说明了此更新修复的问题。

| 不能。 | 功能 | 问题 | 适用于物理设备 | 适用于虚拟设备 |
| --- | --- | --- | --- | --- |
| 1 |卷进入脱机状态 |当云延迟很严重且持续数分钟时，StorSimple 虚拟设备卷会在主机上脱机。 此修补程序会提高云延迟的阈值，因此可尽量减少导致卷在主机上进入脱机状态的情况。 |否 |是 |

## <a name="known-issues-in-the-january-release"></a>1 月份发行版的已知问题
下表提供此版本中已知问题的摘要。

| 否。 | 功能 | 问题 | 注释/解决方法 | 适用于物理设备 | 适用于虚拟设备 |
| --- | --- | --- | --- | --- | --- |
| 1 |恢复出厂设置 |在某些情况下，执行恢复出厂设置时，StorSimple 设备可能会卡住并显示以下消息：“正在进行出厂重置(阶段 8)”。 如果在该 cmdlet 正在运行时按下 CTRL+C，就会发生这种情况。 |在启动恢复出厂设置后不要按 CTRL+C。 如果已处于此状态，请联系 Microsoft 支持部门以执行后续步骤。 |是 |否 |
| #N/A |磁盘仲裁 |在极少数情况下，如果 8600 设备的 EBOD 机箱中的大部分磁盘断开连接，导致没有磁盘仲裁，然后存储池会脱机。 即使磁盘重新连接，存储池也将保持脱机状态。 |需要重新启动设备。 如果问题仍然存在，请联系 Microsoft 支持部门以了解后续步骤。 |是 |否 |
| 3 |云快照失败 |在极少数情况下，云快照可能会失败并出现错误“达到最大备份限制”。 如果同一设备上从已删除的同一原始卷执行的联机克隆数超过 255，则会发生这种情况。 | |是 |是 |
| 4 |控制器 ID 错误 |更换控制器后，控制器 0 可能显示为控制器 1。 在更换控制器的过程中，从对等节点加载映像时，控制器 ID 刚开始可能显示为对等控制器的 ID。  在极少数情况下，此行为也可能在系统重新启动后出现。 |不需要用户操作。 控制器更换过程完成后，这种情况会自动解决。 |是 |否 |
| 5 |设备监视图表 |在 StorSimple Manager 服务中，如果在设备的代理服务器配置中启用“基本”或“NTLM”身份验证，设备监视图表不再工作。 |针对注册到 StorSimple Manager 服务的设备修改 Web 代理配置，将身份验证设置为“无”。 为此，请运行 Windows PowerShell for StorSimple Set-HcsWebProxy cmdlet。 |是 |是 |
| 6 |存储帐户 |此版本不支持使用存储服务删除存储帐户， 否则会导致无法检索用户数据。 | |是 |是 |
| 7 |设备故障转移 |不支持从同一源设备将某个卷容器多次故障转移到不同的目标设备。 |从单个不活动的设备故障转移到多个设备，会使第一个故障转移设备上卷容器丢失数据所有权。 进行此类故障转移后，在 Azure 经典门户中查看这些卷容器时，会发现它们的显示或表现有所不同。 |是 |否 |
| 8 |安装 |安装 StorSimple Adapter for SharePoint 期间，需要提供设备 IP 才能成功完成安装。 | |是 |否 |
| 9 |Web 代理 |如果 Web 代理配置将 HTTPS 作为指定的协议，则设备到服务通信将受到影响，并且设备将进入脱机状态。 在此过程中会生成支持包，从而耗用设备上的大量资源。 |请确保 Web 代理 URL 将 HTTP 作为指定的协议。 详细了解如何[为设备配置 Web 代理](storsimple-configure-web-proxy.md)。 |是 |否 |
| 10 |Web 代理 |如果在注册的设备上配置并启用 Web 代理，将需要重新启动设备上的主动控制器。 | |是 |否 |
| 11 |云高延迟和高 I/O 工作负载 |当 StorSimple 设备同时遇到非常高的云延迟（秒级）和高 I/O 工作负载情况时，设备卷将进入降级状态，并且 I/O 可能会出现故障，发生“设备未就绪”错误。 |需要手动重新启动设备控制器或执行设备故障转移，才可以从这种情况中恢复。 |是 |否 |

## <a name="physical-device-updates-in-the-january-release"></a>1 月份发行版中的物理设备更新
此更新不包含对 StorSimple 设备的任何其他更改。

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-january-release"></a>1 月份发行版中的串行连接 SCSI (SAS) 控制器和固件更新
此发行版不包含对串行连接 SCSI (SAS) 控制器或固件的任何更新。 驱动程序更新位于 2014 年 10 月版中。 

## <a name="virtual-device-updates-in-the-january-release"></a>1 月份发行版中的虚拟设备更新
此发行版包含虚拟设备的已更新映像。 所有在 2015 年 1 月 20 日后创建的虚拟设备都会显示软件版本 6.3.9600.17361。

