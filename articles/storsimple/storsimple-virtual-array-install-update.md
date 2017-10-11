---
title: "在 Microsoft Azure StorSimple 虚拟阵列上安装更新 | Microsoft 文档"
description: "介绍如何使用 StorSimple Virtual Array Web UI，通过门户和修补程序方法应用更新"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 9997a97b-9382-43ed-b56e-61369335c987
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c2d081604c0ca01f47c3ff2aab7477859d280963
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="install-updates-on-your-storsimple-virtual-array---azure-portal"></a>在 StorSimple 虚拟阵列上安装更新 - Azure 门户

## <a name="overview"></a>概述

本文介绍通过本地 Web UI 和 Azure 门户在 StorSimple 虚拟阵列上安装更新所需的步骤。 需要应用软件更新或修补程序，使 StorSimple Virtual Array 保持最新。 

请记住，安装更新或修补程序会重新启动设备。 假定 StorSimple Virtual Array 是单节点设备，任何正在进行的 I/O 都将中断，设备也会停机。 

在应用更新之前，建议使卷或共享依次在主机和设备上脱机。 这能在最大程度上减少发生数据损坏的可能性。

> [!IMPORTANT]
> 如果要运行 Update 0.1 或 GA 软件版本，必须使用修补程序方法通过本地 Web UI 安装 Update 0.3。 如果要运行 Update 0.2，建议通过 Azure 经典门户安装更新。
 

## <a name="use-the-local-web-ui"></a>使用本地 Web UI

通过两个步骤使用本地 Web UI：

* 下载更新或修补程序
* 安装更新或修补程序

### <a name="download-the-update-or-the-hotfix"></a>下载更新或修补程序

执行以下步骤，从 Microsoft 更新目录下载软件更新。

#### <a name="to-download-the-update-or-the-hotfix"></a>下载更新或修补程序

1. 启动 Internet Explorer 并导航到 [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com)。

2. 如果这是你在此计算机上首次使用 Microsoft 更新目录，请在系统提示是否安装 Microsoft 更新目录外接程序时单击“安装”。

3. 在 Microsoft 更新目录的搜索框中，输入要下载的修补程序的知识库 (KB) 编号。 针对 Update 0.3 输入 **3182061**，并单击“搜索”。
   
    该修补程序列表（例如 **StorSimple Virtual Array Update 0.3**）显示。
   
    ![搜索目录](./media/storsimple-virtual-array-install-update/download1.png)

4. 单击 **“添加”**。 该更新随即添加到购物篮中。

5. 单击“查看购物篮”。

6. 单击“下载”。 指定或**浏览**到下载项要保存到的本地位置。 更新随即会下载到指定的位置，放置在与更新名称相同的子文件夹中。 也可以将该文件夹复制到可通过设备访问的网络共享位置。

7. 打开复制的文件夹，会看到 Microsoft 更新独立程序包文件 `WindowsTH-KB3011067-x64`。 此文件用于安装更新或修补程序。

### <a name="install-the-update-or-the-hotfix"></a>安装更新或修补程序

在安装更新或修补程序之前，确保更新或修补程序已本地下载到主机上，或可通过网络共享访问。 

使用此方法，在运行 GA 或 Update 0.1 软件版本的设备上安装更新。 此过程完成时间小于 2 分钟。 执行以下步骤，安装更新或修补程序。

#### <a name="to-install-the-update-or-the-hotfix"></a>安装更新或修补程序

1. 在本地 Web UI 中，转到“维护” > “软件更新”。
   
    ![更新设备](./media/storsimple-virtual-array-install-update/update1m.png)

2. 在“更新文件路径”中，输入更新或修补程序的文件名。 也可以浏览到网络共享上的更新或修补程序安装文件。 单击“应用” 。
   
    ![更新设备](./media/storsimple-virtual-array-install-update/update2m.png)

3. 显示一条警告。 假定这是单节点设备，应用更新后，设备将重新启动并且会出现停机。 单击选中图标。
   
   ![更新设备](./media/storsimple-virtual-array-install-update/update3m.png)

4. 更新启动。 成功更新设备后，该设备将重新启动。 本地 UI 在此期间不可访问。
   
    ![更新设备](./media/storsimple-virtual-array-install-update/update5m.png)

5. 重新启动完成后，会转到“登录”页。 若要验证设备软件是否已更新，请在本地 Web UI 中，转到“维护” > “软件更新”。 Update 0.3 显示的软件版本应为 **10.0.0.0.0.10288.0**。
   
   > [!NOTE]
   > 我们在本地 Web UI 和 Azure 门户中报告的软件版本稍有不同。 例如，针对同一版本，本地 Web UI 报告 **10.0.0.0.0.10288**，而 Azure 门户则报告 **10.0.10288.0**。
   
    ![更新设备](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>使用 Azure 门户

如果要运行 Update 0.2，建议通过 Azure 门户安装更新。 门户过程需要用户扫描、下载和安装更新。 此过程完成时间大约为 7 分钟。 执行以下步骤，安装更新或修补程序。

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal.md)]

完成安装（即作业状态为 100%）后，转到 StorSimple Device Manager 服务。 选择“设备”，并从连接到此服务的设备列表中选择并单击要更新的设备。 在“设置”边栏选项卡中，转到“管理”部分，并选择“设备更新”。 显示的软件版本应为 **10.0.10288.0**。


## <a name="next-steps"></a>后续步骤

详细了解如何[管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。

