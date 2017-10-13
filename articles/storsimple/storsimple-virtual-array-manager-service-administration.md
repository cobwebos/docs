---
title: "Microsoft Azure StorSimple Manager 虚拟阵列管理 | Microsoft Docs"
description: "了解如何在 Azure 门户中使用 StorSimple Device Manager 服务管理本地 StorSimple 虚拟阵列。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 958244a5-f9f5-455e-b7ef-71a65558872e
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: a74a160eae88a2d03460a1346479c333d8f9d524
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>使用 StorSimple Device Manager 服务管理 StorSimple 虚拟阵列
![安装流程](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>概述
本文介绍了 StorSimple Device Manager 服务界面，包括连接到该服务的方式、各种可用选项并提供通过此 UI 执行的具体工作流的链接。

阅读本文后，将了解如何：

* 连接到 StorSimple Device Manager 服务
* 在 StorSimple Device Manager UI 中导航
* 通过 StorSimple Device Manager 服务管理 StorSimple 虚拟阵列

> [!NOTE]
> 若要查看可用于 StorSimple 8000 系列设备的管理选项，请转到[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)。
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>连接到 StorSimple Device Manager 服务
StorSimple Device Manager 服务在 Microsoft Azure 中运行并连接到多个 StorSimple 虚拟阵列。 用户将使用在浏览器中运行的中央 Microsoft Azure 门户管理这些设备。 若要连接到 StorSimple Device Manager 服务，请执行以下操作。

#### <a name="to-connect-to-the-service"></a>连接到服务
1. 转到 [https://ms.portal.azure.com](https://ms.portal.azure.com)。
2. 使用 Microsoft 帐户凭据，登录到 Microsoft Azure 门户（位于窗格右上角）。
3. 在 StorSimple Device Manager 中导航到“浏览”->“筛选器”，以查看给定订阅中的所有设备管理器。

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>使用 StorSimple Device Manager 服务执行管理任务
下表显示了可以在 StorSimple Device Manager 服务摘要边栏选项卡中执行的所有常见管理任务和复杂工作流的摘要。 这些任务根据在其中启动它们的边栏选项卡进行组织。

有关每个工作流的详细信息，请单击表中的相应过程。

#### <a name="storsimple-device-manager-workflows"></a>StorSimple Device Manager 工作流
| 目标 | 使用此过程 |
| --- | --- | --- |
| 创建服务</br>删除服务</br>获取服务注册密钥</br>重新生成服务注册密钥 |[部署 StorSimple Device Manager 服务](storsimple-virtual-array-manage-service.md) |
| 查看活动日志 |[使用 StorSimple 服务摘要](storsimple-virtual-array-service-summary.md) |
| 停用 Virtual Array</br>删除 Virtual Array |[停用或删除虚拟阵列](storsimple-virtual-array-deactivate-and-delete-device.md) |
| 灾难恢复和设备故障转移</br>故障转移先决条件</br>业务连续性灾难恢复 (BCDR)</br>在灾难恢复期间出错 |[StorSimple Virtual Array 的灾难恢复和设备故障转移](storsimple-virtual-array-failover-dr.md) |
| 备份共享和卷</br>创建手动备份</br>更改备份计划</br>查看现有备份 |[备份 StorSimple Virtual Array](storsimple-virtual-array-backup.md) |
| 从备份集克隆共享</br>从备份集克隆卷</br>项目级恢复（仅限文件服务器） |[从 StorSimple 虚拟阵列的备份克隆](storsimple-virtual-array-clone.md) |
| 关于存储帐户</br>添加存储帐户</br>编辑存储帐户</br>删除存储帐户 |[管理 StorSimple Virtual Array 的存储帐户](storsimple-virtual-array-manage-storage-accounts.md) |
| 关于访问控制记录</br>添加或修改访问控制记录 </br>删除访问控制记录 |[管理 StorSimple Virtual Array 的访问控制记录](storsimple-virtual-array-manage-acrs.md) |
| 查看作业详细信息 |[管理 StorSimple Virtual Array 作业](storsimple-virtual-array-manage-jobs.md) |
| 配置警报设置</br>接收警报通知</br>管理警报</br>查看警报 |[查看和管理 StorSimple Virtual Array 的警报](storsimple-virtual-array-manage-alerts.md) |
| 修改设备管理员密码 |[更改 StorSimple Virtual Array 设备管理员密码](storsimple-virtual-array-change-device-admin-password.md) |
| 安装软件更新 |[更新 Virtual Array](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> 必须使用[本地 Web UI](storsimple-ova-web-ui-admin.md) 执行以下任务：
> 
> * [检索服务数据加密密钥](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [创建支持包](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [停止和重新启动 Virtual Array](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>后续步骤
有关 Web UI 以及如何使用它的信息，请转到[使用 StorSimple Web UI 管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。

