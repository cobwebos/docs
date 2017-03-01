---
title: "StorSimple Manager 服务管理 | Microsoft Docs"
description: "了解如何在 Azure 经典门户中使用 StorSimple Manager 服务管理 StorSimple 设备。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 2586582e-d85c-42e1-afb3-be734c1c0461
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d5d5aa67d14d2344c58e67ee78ea3f2b5d8fd415


---
# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-device"></a>使用 StorSimple Manager 服务管理 StorSimple 设备
## <a name="overview"></a>概述
本文介绍了 StorSimple Manager 服务界面，包括连接到该服务的方式、各种可用选项以及可以通过此 UI 执行的具体工作流的链接。 本指南同时适用于 StorSimple 物理和虚拟设备。

阅读本文后，你将学会：

* 连接到 StorSimple Manager 服务
* 在 StorSimple Manager UI 中导航
* 通过 StorSimple Manager 服务管理 StorSimple 设备

## <a name="connect-to-storsimple-manager-service"></a>连接到 StorSimple Manager 服务
StorSimple Manager 服务在 Microsoft Azure 中运行并连接到多个 StorSimple 设备。 你将使用在浏览器中运行的中央 Microsoft Azure 经典门户管理这些设备。 若要连接到 StorSimple Manager 服务，请执行以下操作。

#### <a name="to-connect-to-the-service"></a>连接到服务
1. 导航到 [https://manage.windowsazure.com/](https://manage.windowsazure.com/)。
2. 使用你的 Microsoft 帐户凭据登录到 Microsoft Azure 经典门户（位于窗格右上角）。
3. 向下滚动左侧的导航窗格以访问 StorSimple Manager 服务。

## <a name="navigate-storsimple-manager-service-ui"></a>在 StorSimple Manager 服务 UI 中导航
下表中显示了 StorSimple Manager 服务 UI 的导航层次结构。

* **StorSimple Manager** 登陆页用于访问该服务中适用于所有设备的 UI 服务级页面。
* “设备”页用于访问适用于特定设备的设备级 UI 页面。
* “卷容器”页用于访问卷页面，其中显示与某个设备关联的所有卷。

#### <a name="storsimple-manager-service-navigational-hierarchy"></a>StorSimple Manager 服务导航层次结构
| 登陆页 | 服务级页面 | 设备级页面 | 设备级页面 |
| --- | --- | --- | --- |
| StorSimple 管理器服务 |服务仪表板 |设备仪表板 | |
| 设备 → |监视 | | |
| 备份目录 |卷容器 → |卷 | |
| 配置（服务） |备份策略 | | |
| 作业 |配置（设备） | | |
| 警报 |维护 | | |

![可用视频](./media/storsimple-manager-service-administration/Video_icon.png) **可用视频**

若要观看将引导你快速浏览 StorSimple Manager 服务用户界面的视频，请单击[此处](https://azure.microsoft.com/documentation/videos/storsimple-manager-service-overview/)。

## <a name="administer-storsimple-device-using-storsimple-manager-service"></a>使用 StorSimple Manager 服务管理 StorSimple 设备
下表显示了可以在 StorSimple Manager 服务 UI 中执行的所有常见管理任务和复杂工作流的摘要。 这些任务是按启动它们的 UI 页面组织的。

有关每个工作流的详细信息，请单击表中的相应过程。

#### <a name="storsimple-manager-workflows"></a>StorSimple Manager 工作流
| 目标 | 转到以下 UI 页面 ... | 遵循以下过程。 |
| --- | --- | --- |
| 创建服务</br>删除服务</br>获取服务注册密钥</br>重新生成服务注册密钥 |StorSimple 管理器服务 |[部署 StorSimple Manager 服务](storsimple-manage-service.md) |
| 更改服务数据加密密钥</br>查看操作日志 |StorSimple Manager 服务 → 仪表板 |[使用 StorSimple Manager 服务仪表板](storsimple-service-dashboard.md) |
| 停用设备</br>删除设备 |StorSimple Manager 服务 → 设备 |[停用或删除设备](storsimple-deactivate-and-delete-device.md) |
| 了解灾难恢复和设备故障转移</br>故障转移到物理设备</br>故障转移到虚拟设备</br>业务连续性灾难恢复 (BCDR) |StorSimple Manager 服务 → 设备 |[StorSimple 设备的故障转移和灾难恢复](storsimple-device-failover-disaster-recovery.md) |
| 列出卷的备份</br>选择备份集</br>删除备份集 |StorSimple Manager 服务 → 备份目录 |[管理备份](storsimple-manage-backup-catalog.md) |
| 克隆卷 |StorSimple Manager 服务 → 备份目录 |[克隆卷](storsimple-clone-volume.md) |
| 还原备份集 |StorSimple Manager 服务 → 备份目录 |[还原备份集](storsimple-restore-from-backup-set.md) |
| 关于存储帐户</br>添加存储帐户</br>编辑存储帐户</br>删除存储帐户</br>存储帐户的密钥轮换 |StorSimple Manager 服务 → 配置 |[管理存储帐户](storsimple-manage-storage-accounts.md) |
| 关于带宽模板</br>添加带宽模板</br>编辑带宽模板</br>删除带宽模板</br>使用默认带宽模板</br>创建在指定的时间启动的全天带宽模板 |StorSimple Manager 服务 → 配置 |[管理带宽模板](storsimple-manage-bandwidth-templates.md) |
| 关于访问控制记录</br>创建访问控制记录</br>编辑访问控制记录</br>删除访问控制记录 |StorSimple Manager 服务 → 配置 |[管理访问控制记录](storsimple-manage-acrs.md) |
| 查看作业详细信息</br>取消作业 |StorSimple Manager 服务 → 作业 |[管理作业](storsimple-manage-jobs.md) |
| 接收警报通知</br>管理警报</br>查看警报 |StorSimple Manager 服务 → 警报 |[查看和管理 StorSimple 警报](storsimple-manage-alerts.md) |
| 查看已连接的发起程序</br>查找设备序列号</br>查找目标 IQN |StorSimple Manager 服务 → 设备 → 仪表板 |[使用 StorSimple 设备仪表板](storsimple-device-dashboard.md) |
| 创建监视图表 |StorSimple Manager 服务 → 设备 → 监视 |[监视 StorSimple 设备](storsimple-monitor-device.md) |
| 添加卷容器</br>修改卷容器</br>删除卷容器 |StorSimple Manager 服务 → 设备 → 卷容器 |[管理卷容器](storsimple-manage-volume-containers.md) |
| 添加卷</br>修改卷</br>使卷脱机</br>删除卷</br>监视卷 |StorSimple Manager 服务 → 设备 → 卷容器 → 卷 |[管理卷](storsimple-manage-volumes.md) |
| 修改设备设置</br>修改时间设置</br>修改 DNS.md 设置</br>配置网络接口 |StorSimple Manager 服务 → 设备 → 配置 |[修改 StorSimple 设备的设备配置](storsimple-modify-device-config.md) |
| 查看 Web 代理设置 |StorSimple Manager 服务 → 设备 → 配置 |[为设备配置 Web 代理](storsimple-configure-web-proxy.md) |
| 修改设备管理员密码</br>修改 StorSimple Snapshot Manager 密码 |StorSimple Manager 服务 → 设备 → 配置 |[更改 StorSimple 密码](storsimple-change-passwords.md) |
| 配置远程管理 |StorSimple Manager 服务 → 设备 → 配置 |[远程连接到 StorSimple 设备](storsimple-remote-connect.md) |
| 配置警报设置 |StorSimple Manager 服务 → 设备 → 配置 |[查看和管理 StorSimple 警报](storsimple-manage-alerts.md) |
| 为 StorSimple 设备配置 CHAP |StorSimple Manager 服务 → 设备 → 配置 |[为你的 StorSimple 设备配置 CHAP](storsimple-configure-chap.md) |
| 添加备份策略</br>添加或修改计划</br>删除备份策略</br>创建手动备份</br>创建包含多个卷和计划的自定义备份策略 |StorSimple Manager 服务 → 设备 → 备份策略 |[管理备份策略](storsimple-manage-backup-policies.md) |
| 停止设备控制器</br>重新启动设备控制器</br>关闭设备控制器</br>将设备重置为出厂默认设置</br>（以上内容仅适用于本地设备） |StorSimple Manager 服务 → 设备 → 维护 |[维护 StorSimple 设备控制器](storsimple-manage-device-controller.md) |
| 了解 StorSimple 硬件组件</br>监视硬件状态</br>（以上内容仅适用于本地设备） |StorSimple Manager 服务 → 设备 → 维护 |[监视硬件组件](storsimple-monitor-hardware-status.md) |
| 创建支持包 |StorSimple Manager 服务 → 设备 → 维护 |[创建和管理支持包](storsimple-create-manage-support-package.md) |
| 安装软件更新 |StorSimple Manager 服务 → 设备 → 维护 |[更新设备](storsimple-update-device.md) |

## <a name="next-steps"></a>后续步骤
如果你在 StorSimple 设备的日常操作或其硬件组件方面遇到任何问题，请参阅：

* [对运转的设备进行故障排除](storsimple-troubleshoot-operational-device.md)
* [使用 StorSimple 监视指示器 LED](storsimple-monitoring-indicators.md)

如果无法解决问题并且需要创建服务请求，请参阅[联系 Microsoft 支持部门](storsimple-contact-microsoft-support.md)。




<!--HONumber=Nov16_HO3-->


