---
title: StorSimple Device Manager 服务管理 | Microsoft Docs
description: 了解如何在 Azure 门户中使用 StorSimple Device Manager 服务管理 StorSimple 设备。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: alkohli
ms.openlocfilehash: 0e7d7f44a70278a7777ba6c32c8e546074953fdc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23108565"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>使用 StorSimple Device Manager 服务管理 StorSimple 设备

## <a name="overview"></a>概述

本文介绍 StorSimple Device Manager 服务界面，包括连接到该服务的方式、各种可用选项以及可以通过此 UI 执行的具体工作流的链接。 本指南同时适用于 StorSimple 物理设备和云设备。

阅读本文后，将学会：

* 连接到 StorSimple Device Manager 服务
* 通过 StorSimple Device Manager 服务管理 StorSimple 设备

## <a name="connect-to-storsimple-device-manager-service"></a>连接到 StorSimple Device Manager 服务

StorSimple Device Manager 服务在 Microsoft Azure 中运行并连接到多个 StorSimple 设备。 用户将使用在浏览器中运行的中央 Microsoft Azure 门户管理这些设备。 若要连接到 StorSimple Device Manager 服务，请执行以下操作。

#### <a name="to-connect-to-the-service"></a>连接到服务
1. 导航到 [https://portal.azure.com/](https://portal.azure.com/)。
2. 使用 Microsoft 帐户凭据，登录到 Microsoft Azure 门户（位于窗格右上角）。
3. 向下滚动左侧的导航窗格以访问 StorSimple Device Manager 服务。


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>使用 StorSimple Device Manager 服务管理 StorSimple 设备

下表显示了可以在 StorSimple Device Manager 服务 UI 中执行的所有常见管理任务和复杂工作流的摘要。 这些任务根据在其中启动它们的 UI 边栏选项卡进行组织。

有关每个工作流的详细信息，请单击表中的相应过程。

#### <a name="storsimple-device-manager-workflows"></a>StorSimple Device Manager 工作流

| 目标 | 请遵循以下过程。 |
| --- | --- |
| 创建服务</br>删除服务</br>获取服务注册密钥</br>重新生成服务注册密钥 |[部署 StorSimple Device Manager 服务](storsimple-8000-manage-service.md) |
| 查看活动日志 |[使用 StorSimple Device Manager 服务摘要](storsimple-8000-service-dashboard.md) |
| 更改服务数据加密密钥</br>查看操作日志 |[使用 StorSimple Device Manager 服务仪表板](storsimple-8000-service-dashboard.md) |
| 停用设备</br>删除设备 |[停用或删除设备](storsimple-8000-deactivate-and-delete-device.md) |
| 了解灾难恢复和设备故障转移</br>故障转移到物理设备</br>故障转移到虚拟设备</br>业务连续性灾难恢复 (BCDR) |[StorSimple 设备的故障转移和灾难恢复](storsimple-8000-device-failover-disaster-recovery.md) |
| 列出卷的备份</br>选择备份集</br>删除备份集 |[管理备份](storsimple-8000-manage-backup-catalog.md) |
| 克隆卷 |[克隆卷](storsimple-8000-clone-volume-u2.md) |
| 还原备份集 |[还原备份集](storsimple-8000-restore-from-backup-set-u2.md) |
| 关于存储帐户</br>添加存储帐户</br>编辑存储帐户</br>删除存储帐户</br>存储帐户的密钥轮换 |[管理存储帐户](storsimple-8000-manage-storage-accounts.md) |
| 关于带宽模板</br>添加带宽模板</br>编辑带宽模板</br>删除带宽模板</br>使用默认带宽模板</br>创建在指定的时间启动的全天带宽模板 |[管理带宽模板](storsimple-8000-manage-bandwidth-templates.md) |
| 关于访问控制记录</br>创建访问控制记录</br>编辑访问控制记录</br>删除访问控制记录 |[管理访问控制记录](storsimple-8000-manage-acrs.md) |
| 查看作业详细信息</br>取消作业 |[管理作业](storsimple-8000-manage-jobs-u2.md) |
| 接收警报通知</br>管理警报</br>查看警报 |[查看和管理 StorSimple 警报](storsimple-8000-manage-alerts.md) |
| 创建监视图表 |[监视 StorSimple 设备](storsimple-monitor-device.md) |
| 添加卷容器</br>修改卷容器</br>删除卷容器 |[管理卷容器](storsimple-8000-manage-volume-containers.md) |
| 添加卷</br>修改卷</br>使卷脱机</br>删除卷</br>监视卷 |[管理卷](storsimple-8000-manage-volumes-u2.md) |
| 修改设备设置</br>修改时间设置</br>修改 DNS.md 设置</br>配置网络接口 |[修改 StorSimple 设备的设备配置](storsimple-8000-modify-device-config.md) |
| 查看 Web 代理设置 |[为设备配置 Web 代理](storsimple-8000-configure-web-proxy.md) |
| 修改设备管理员密码</br>修改 StorSimple Snapshot Manager 密码 |[更改 StorSimple 密码](storsimple-8000-change-passwords.md) |
| 配置远程管理 |[远程连接到 StorSimple 设备](storsimple-8000-remote-connect.md) |
| 配置警报设置 |[查看和管理 StorSimple 警报](storsimple-8000-manage-alerts.md) |
| 为 StorSimple 设备配置 CHAP |[为 StorSimple 设备配置 CHAP](storsimple-configure-chap.md) |
| 添加备份策略</br>添加或修改计划</br>删除备份策略</br>创建手动备份</br>创建包含多个卷和计划的自定义备份策略 |[管理备份策略](storsimple-8000-manage-backup-policies-u2.md) |
| 停止设备控制器</br>重新启动设备控制器</br>关闭设备控制器</br>将设备重置为出厂默认设置</br>（以上内容仅适用于本地设备） |[维护 StorSimple 设备控制器](storsimple-8000-manage-device-controller.md) |
| 了解 StorSimple 硬件组件</br>监视硬件状态</br>（以上内容仅适用于本地设备） |[监视硬件组件](storsimple-8000-monitor-hardware-status.md) |
| 创建支持包 |[创建和管理支持包](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| 安装软件更新 |[更新设备](storsimple-update-device.md) |

## <a name="next-steps"></a>后续步骤

如果在 StorSimple 设备的日常操作或其硬件组件方面遇到任何问题，请参阅：

* [使用诊断工具进行故障排除](storsimple-8000-diagnostics.md)
* [使用 StorSimple 监视指示器 LED](storsimple-monitoring-indicators.md)

如果无法解决问题并且需要创建服务请求，请参阅[联系 Microsoft 支持部门](storsimple-8000-contact-microsoft-support.md)。

