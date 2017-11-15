---
title: "StorSimple 8000 发行版发行说明 | Microsoft Docs"
description: "介绍 2014 年 7 月版 Microsoft Azure StorSimple 的新功能、待解决问题以及可用的解决方法。"
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 12f1796e-37c3-42b4-b997-a84fc1950c20
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: e6e087ad61ee54fef3552895c00a478fa51e2072
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2017
---
# <a name="storsimple-8000-series-release-version-release-notes---july-2014"></a>StorSimple 8000 系列发行版发行说明 - 2014 年 7 月

> [!NOTE]
> 已弃用 StorSimple 经典门户。 按弃用计划，StorSimple 设备管理器将自动移至新的 Azure 门户。 会收到有关此移动的电子邮件和门户通知。 此文档亦将在稍后停用。 如有任何与移动相关的问题，请参阅[常见问题解答：移动到 Azure 门户](storsimple-8000-move-azure-portal-faq.md)。

## <a name="overview"></a>概述

以下发行说明描述了 2014 年 7 月公开发行 (GA) 版 Microsoft Azure StorSimple 的 StorSimple 8000 系列的重大待解决问题。 此发行版对应软件版本 6.3.9600.17215。

除非另行指定，否则这些发行说明适用于 StorSimple 设备的所有型号。 发行说明会持续进行更新，并且会陆续将所发现的需要解决的重要问题添加到说明中。 部署 Microsoft Azure StorSimple 解决方案之前，请考虑以下信息。

## <a name="known-issues-in-this-release"></a>本版本中的已知问题

下表提供此版本中已知问题的摘要。  

| 否。 | 功能 | 问题 | 注释/解决方法 | 适用于物理设备 | 适用于虚拟设备 |
| --- | --- | --- | --- | --- | --- |
| 1 |恢复出厂设置 |在某些情况下，执行恢复出厂设置时，StorSimple 设备可能会卡住并显示以下消息：“正在进行恢复出厂设置(阶段 8)”。 如果在该 cmdlet 正在进行时按 CTRL+C，则会发生此问题。 |在启动恢复出厂设置后不要按 CTRL+C。 如果已处于此状态，请联系 Microsoft 支持部门以执行后续步骤。 |是 |否 |
| #N/A |磁盘仲裁 |在极少数情况下，如果 8600 设备的 EBOD 机箱中的大部分磁盘断开连接，这会导致没有磁盘仲裁，然后存储池会脱机。 即使磁盘重新连接，存储池也将保持脱机状态。 |需要重新启动设备。 如果问题仍然存在，请联系 Microsoft 支持部门以了解后续步骤。 |是 |否 |
| 3 |云快照失败 |在极少数情况下，云快照可能会失败并出现错误“达到最大备份限制”。 如果同一设备上从已删除的同一原始卷执行的联机克隆数超过 255，则会发生这种情况。 | |是 |是 |
| 4 |错误的控制器 ID |更换控制器后，控制器 0 可能显示为控制器 1。 在更换控制器的过程中，从对等节点加载映像时，控制器 ID 刚开始可能显示为对等控制器的 ID。 在极少数情况下，此行为也可能在系统重新启动后出现。 |不需要用户操作。 控制器更换过程完成后，这种情况会自动解决。 |是 |否 |
| 5 |设备监视图表 |在 StorSimple Manager 服务中，如果在设备的代理服务器配置中启用“基本”或“NTLM”身份验证，设备监视图表不再工作。 |针对注册到 StorSimple Manager 服务的设备修改 Web 代理配置，将身份验证设置为“无”。 为此，请运行 Windows PowerShell for StorSimple Set-HcsWebProxy cmdlet。 |是 |是 |
| 6 |存储帐户 |此版本不支持使用存储服务删除存储帐户， 否则会导致无法检索用户数据。 | |是 |是 |
| 7 |故障回复 |不支持在灾难恢复 (DR) 后 24 小时内进行故障回复。 | |是 |否 |
| 8 |设备故障转移 |不支持从同一源设备将某个卷容器多次故障转移到不同的目标设备。 如果从单个不活动的设备故障转移到多个设备，第一个故障转移的设备上的卷容器将丢失数据所有权。 进行此类故障转移后，在 Azure 经典门户中查看这些卷容器时，会发现它们的显示或表现有所不同。 | |是 |否 |
| 9 |安装 |安装 StorSimple Adapter for SharePoint 期间，需要提供设备 IP 才能成功完成安装。 | |是 |否 |
| 10 |网络接口 |网络接口 DATA 2 和 DATA 3 在软件中是互换的。 |如果需要配置这些接口，请联系 Microsoft 支持部门。 |是 |否 |

