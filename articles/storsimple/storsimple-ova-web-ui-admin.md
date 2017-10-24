---
title: "StorSimple Virtual Array Web UI 管理 | Microsoft Docs"
description: "介绍如何通过 StorSimple Virtual Array Web UI 执行基本的设备管理任务。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: ea65b4c7-a478-43e6-83df-1d9ea62916a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 989e7b697f9b527df549fb32be18edd1d3c8d224
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>使用 Web UI 管理 StorSimple Virtual Array
![安装流程](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>概述
本文中的教程适用于运行 2016 年 3 月正式 (GA) 版的 Microsoft Azure StorSimple Virtual Array（也称为 StorSimple 本地虚拟设备）。 本文介绍了一些可在 StorSimple Virtual Array 上执行的复杂的工作流和管理任务。 可使用 StorSimple Manager 服务 UI（称为门户 UI）和设备的本地 Web UI 管理 StorSimple Virtual Array。 本文重点介绍可使用 Web UI 执行的任务。

本文包括以下教程：

* 获取服务数据加密密钥
* 解决 Web UI 安装错误
* 生成日志包
* 关闭或重启设备

## <a name="get-the-service-data-encryption-key"></a>获取服务数据加密密钥
向 StorSimple Manager 服务注册第一台设备时，会生成服务数据加密密钥。 向 StorSimple Manager 服务注册其他设备时，需要此密钥以及该服务注册密钥。

如果不慎丢失服务数据加密密钥，并且需要取回它，请在注册到服务的设备的本地 Web UI 中执行以下步骤。

#### <a name="to-get-the-service-data-encryption-key"></a>获取服务数据加密密钥
1. 连接到本地 Web UI。 转到“配置” > “云设置”。
2. 在页面底部，单击“获取服务数据加密密钥”。 将出现密钥。 复制并保存此密钥。
   
    ![获取服务数据加密密钥 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>解决 Web UI 安装错误
在某些情况下，通过本地 Web UI 配置设备时，可能会出现错误。 若要诊断并解决此类错误，可以运行诊断测试。

#### <a name="to-run-the-diagnostic-tests"></a>运行诊断测试
1. 在本地 Web UI 中，转到“故障排除” > “诊断测试”。
   
    ![运行诊断 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. 在页面底部，单击“运行诊断测试”。 这会启动测试以诊断网络、设备、Web 代理、时间或云设置可能存在的任何问题。 用户将收到设备正在运行测试的通知。
3. 测试完成后，会显示结果。 以下示例显示诊断测试的结果。 请注意，此设备上未配置 Web 代理设置，因此没有运行 Web 代理测试。 针对网络设置、DNS 服务器和时间设置的所有其他测试均已成功完成。
   
    ![运行诊断 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>生成日志包
日志包由所有可帮助 Microsoft 支持排查任何设备问题的相关日志组成。 在此版本中，可通过本地 Web UI 生成日志包。

#### <a name="to-generate-the-log-package"></a>生成日志包
1. 在本地 Web UI 中，转到“故障排除” > “系统日志”。
   
    ![生成日志包 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. 在页面底部，单击“创建日志包”。 将创建系统日志的包。 几分钟后即可创建完毕。
   
    ![生成日志包 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    在成功创建包后，用户将收到通知，页面将更新以指示创建包的时间和日期。
   
    ![生成日志包 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. 单击“下载日志包”。 压缩包将下载到系统上。
   
    ![生成日志包 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. 可以解压缩下载的日志包并查看系统日志文件。

## <a name="shut-down-and-restart-your-device"></a>关闭和重启设备
可使用本地 Web UI 关闭或重启虚拟设备。 在重启之前，建议使卷或共享依次在主机和设备上脱机。 这能在最大程度上减少发生数据损坏的可能性。 

#### <a name="to-shut-down-your-virtual-device"></a>关闭虚拟设备
1. 在本地 Web UI 中，转到“维护” > “电源设置”。
2. 在页面底部，单击“关闭”。
   
    ![关闭设备 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. 会出现一条警告消息，表明关闭设备将使任何正在进行的 IO 中断，从而导致停机。 单击选中图标  ![选中图标](./media/storsimple-ova-web-ui-admin/image3.png)。
   
    ![设备关闭警告](./media/storsimple-ova-web-ui-admin/image37.png)
   
    用户将收到已启动关闭的通知。
   
    ![已启动设备关闭](./media/storsimple-ova-web-ui-admin/image38.png)
   
    设备将立即关闭。 如果想要启动设备，需要通过 Hyper-V 管理器执行此操作。

#### <a name="to-restart-your-virtual-device"></a>重启虚拟设备
1. 在本地 Web UI 中，转到“维护” > “电源设置”。
2. 在页面底部，单击“重启”。
   
    ![设备重启](./media/storsimple-ova-web-ui-admin/image36.png)
3. 会出现一条警告消息，表明重启设备将使任何正在进行的 IO 中断，从而导致停机。 单击选中图标  ![选中图标](./media/storsimple-ova-web-ui-admin/image3.png)。
   
    ![重启警告](./media/storsimple-ova-web-ui-admin/image37.png)
   
    用户将收到已启动重启的通知。
   
    ![已启动重启](./media/storsimple-ova-web-ui-admin/image39.png)
   
    正在进行重启时，将无法连接到 UI。 可通过定期刷新 UI 以监视重启。 或者，可通过 Hyper-V 管理器监视设备的重启状态。

## <a name="next-steps"></a>后续步骤
了解如何[使用 StorSimple Manager 服务管理设备](storsimple-virtual-array-manager-service-administration.md)。

