---
title: "StorSimple Manager Virtual Array 管理 | Microsoft Docs"
description: "了解如何在 Azure 经典门户中使用 StorSimple Manager 服务管理本地 StorSimple Virtual Array。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: f0ae362c-dffd-4a14-bbcf-c304bfb93268
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d90864501485344d93c1f3bd32237683c2abb4ab


---
# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-virtual-array"></a>使用 StorSimple Manager 服务管理 StorSimple Virtual Array
![安装流程](./media/storsimple-ova-manager-service-administration/manage4.png)

## <a name="overview"></a>概述
本文介绍了 StorSimple Manager 服务界面，包括连接到该服务的方式、各种可用选项并提供通过此 UI 执行的具体工作流的链接。 

阅读本文后，你将了解如何：

* 连接到 StorSimple Manager 服务
* 在 StorSimple Manager UI 中导航
* 通过 StorSimple Manager 服务管理 StorSimple Virtual Array

> [!NOTE]
> 若要查看可用于 StorSimple 8000 系列设备的管理选项，请转到[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)。
> 
> 

## <a name="connect-to-the-storsimple-manager-service"></a>连接到 StorSimple Manager 服务
StorSimple Manager 服务在 Microsoft Azure 中运行并连接到多个 StorSimple Virtual Array。 你将使用在浏览器中运行的集中 Microsoft Azure 经典门户管理这些设备。 若要连接到 StorSimple Manager 服务，请执行以下操作。

#### <a name="to-connect-to-the-service"></a>连接到服务
1. 转到 [https://manage.windowsazure.com/](https://manage.windowsazure.com/)。
2. 使用你的 Microsoft 帐户凭据，登录到 Microsoft Azure 经典门户（位于窗格右上角）。
3. 向下滚动左侧的导航窗格以访问 StorSimple Manager 服务。
   
    ![滚动到服务](./media/storsimple-ova-manager-service-administration/admin-scroll.png)

## <a name="navigate-the-storsimple-manager-service-ui"></a>在 StorSimple Manager 服务 UI 中导航
下表中显示了 StorSimple Manager 服务 UI 的导航层次结构。

* **StorSimple Manager** 登陆页面用于访问该服务中适用于所有 Virtual Array 的 UI 服务级页面。
* “设备”页用于访问适用于特定 Virtual Array 的设备级 UI 页面。

#### <a name="storsimple-manager-service-navigational-hierarchy"></a>StorSimple Manager 服务导航层次结构
| 登陆页面 | 服务级页面 | 设备级页面 |
| --- | --- | --- |
| StorSimple 管理器服务 |仪表板（服务） |仪表板（设备） |
| 设备 → |监视 | |
| 备份目录 |共享（文件服务器）或 </br>卷（iSCSI 服务器） | |
| 配置（服务） |配置（设备） | |
| 作业 |维护 | |
| 警报 | | |

## <a name="use-the-storsimple-manager-service-to-perform-management-tasks"></a>使用 StorSimple Manager 服务执行管理任务
下表显示了可以在 StorSimple Manager 服务 UI 中执行的所有常见管理任务和复杂工作流的摘要。 这些任务根据启动它们的 UI 页面进行组织。

有关每个工作流的详细信息，请单击表中的相应过程。

#### <a name="storsimple-manager-workflows"></a>StorSimple Manager 工作流
| 目标 | 转到此 UI 页面 ... | 使用此过程 |
| --- | --- | --- |
| 创建服务</br>删除服务</br>获取服务注册密钥</br>重新生成服务注册密钥 |StorSimple 管理器服务 |[部署 StorSimple Manager 服务](storsimple-ova-manage-service.md) |
| 更改服务数据加密密钥</br>查看操作日志 |StorSimple Manager 服务 → 仪表板 |[使用 StorSimple 服务仪表板](storsimple-ova-service-dashboard.md) |
| 停用 Virtual Array</br>删除 Virtual Array |StorSimple Manager 服务 → 设备 |[停用或删除 Virtual Array](storsimple-ova-deactivate-and-delete-device.md) |
| 灾难恢复和设备故障转移</br>故障转移先决条件</br>故障转移到虚拟设备</br>业务连续性灾难恢复 (BCDR)</br>在灾难恢复期间出错 |StorSimple Manager 服务 → 设备 |[StorSimple Virtual Array 的灾难恢复和设备故障转移](storsimple-ova-failover-dr.md) |
| 备份共享和卷</br>创建手动备份</br>更改备份计划</br>查看现有备份 |StorSimple Manager 服务 → 备份目录 |[备份 StorSimple Virtual Array](storsimple-ova-backup.md) |
| 从备份集中还原共享</br>从备份集中还原卷</br>项目级恢复（仅限文件服务器） |StorSimple Manager 服务 → 备份目录 |[从 StorSimple Virtual Array 的备份中还原](storsimple-ova-restore.md) |
| 关于存储帐户</br>添加存储帐户</br>编辑存储帐户</br>删除存储帐户 |StorSimple Manager 服务 → 配置 |[管理 StorSimple Virtual Array 的存储帐户](storsimple-ova-manage-storage-accounts.md) |
| 关于访问控制记录</br>添加或修改访问控制记录 </br>删除访问控制记录 |StorSimple Manager 服务 → 配置 |[管理 StorSimple Virtual Array 的访问控制记录](storsimple-ova-manage-acrs.md) |
| 查看作业详细信息 |StorSimple Manager 服务 → 作业 |[管理 StorSimple Virtual Array 作业](storsimple-ova-manage-jobs.md) |
| 配置警报设置</br>接收警报通知</br>管理警报</br>查看警报 |StorSimple Manager 服务 → 警报 |[查看和管理 StorSimple Virtual Array 的警报](storsimple-ova-manage-alerts.md) |
| 修改设备管理员密码 |StorSimple Manager 服务 → 设备 → 配置 |[更改 StorSimple Virtual Array 设备管理员密码](storsimple-ova-change-device-admin-password.md) |
| 安装软件更新 |StorSimple Manager 服务 → 设备 → 维护 |[更新 Virtual Array](storsimple-ova-install-update-01.md) |

> [!NOTE]
> 必须使用[本地 Web UI](storsimple-ova-web-ui-admin.md) 执行以下任务：
> 
> * [检索服务数据加密密钥](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [创建支持包](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [停止和重新启动 Virtual Array](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>后续步骤
有关 Web UI 以及如何使用它的信息，请转到[使用 StorSimple Web UI 管理你的 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。




<!--HONumber=Nov16_HO3-->


