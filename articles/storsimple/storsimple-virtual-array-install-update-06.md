---
title: "在 StorSimple Virtual Array 上安装 Update 0.6 | Microsoft Docs"
description: "介绍如何使用 StorSimple Virtual Array Web UI，通过 Azure 门户和修补程序方法应用更新"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/18/2017
ms.author: alkohli
ms.openlocfilehash: 111976cd684561f5bc63b92f09a20470fe3036d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="install-update-06-on-your-storsimple-virtual-array"></a>在 StorSimple Virtual Array 上安装Update 0.6

## <a name="overview"></a>概述

本文介绍通过本地 Web UI 和 Azure 门户在 StorSimple Virtual Array 上安装 Update 0.6 所需的步骤。 应用软件更新或修补程序，使 StorSimple Virtual Array 保持最新。

在应用更新之前，建议使卷或共享依次在主机和设备上脱机。 这能在最大程度上减少发生数据损坏的可能性。 在卷或共享离线后，还应手动备份设备。

> [!IMPORTANT]
> - Update 0.6 对应于设备上的 10.0.10293.0 软件版本。 有关此更新中新增功能的信息，请转到 [Update 0.6 发行说明](storsimple-virtual-array-update-06-release-notes.md)。
>
> - 如果要运行 Update 0.2 或更高版本，建议通过 Azure 门户安装更新。 如果正在运行 Update 0.1 或 GA 软件版本，必须使用修补程序方法通过本地 Web UI 安装 Update 0.6。
>
> - 请记住，安装更新或修补程序会重新启动设备。 假定 StorSimple Virtual Array 是单节点设备，任何正在进行的 I/O 都将中断，设备也会停机。

## <a name="use-the-azure-portal"></a>使用 Azure 门户

如果要运行 Update 0.2 和更高版本，建议通过 Azure 门户安装更新。 门户过程需要用户扫描、下载和安装更新。 此过程完成时间大约为 7 分钟。 执行以下步骤，安装更新或修补程序。

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

完成安装后，转到 StorSimple Device Manager 服务。 选择“设备”，并选择并单击刚刚更新的设备。 转到“设置”>“管理”>“设备更新”。 显示的软件版本应为 10.0.10293.0。

## <a name="use-the-local-web-ui"></a>使用本地 Web UI

通过两个步骤使用本地 Web UI：

* 下载更新或修补程序
* 安装更新或修补程序

### <a name="download-the-update-or-the-hotfix"></a>下载更新或修补程序

执行以下步骤，从 Microsoft 更新目录下载软件更新。

#### <a name="to-download-the-update-or-the-hotfix"></a>下载更新或修补程序

1. 启动 Internet Explorer 并导航到 [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com)。

2. 如果是首次在此计算机上使用 Microsoft 更新目录，请在系统提示是否安装 Microsoft 更新目录外接程序时单击“安装”。

3. 在 Microsoft 更新目录的搜索框中，输入要下载的修补程序的知识库 (KB) 编号。 针对 Update 0.6 输入 4023268，然后单击“搜索”。
   
    随即显示修补程序列表（例如 StorSimple Virtual Array Update 0.6）。
   
    ![搜索目录](./media/storsimple-virtual-array-install-update-06/download1.png)

4. 单击“下载”。

5. 应出现 5 个要下载的文件。 请将其中每个文件下载到一个文件夹中。 也可以将该文件夹复制到可通过设备访问的网络共享位置。

6. 打开文件所在的文件夹。
    ![程序包中的文件](./media/storsimple-virtual-array-install-update-06/update06folder.png)

    可看到：
    -  Microsoft 更新独立程序包文件 `WindowsTH-KB3011067-x64`。 此文件用于更新设备软件。
    - Geneva 监视代理程序包文件 `GenevaMonitoringAgentPackageInstaller`。 此文件用于更新监视和诊断服务 (MDS) 代理。 双击 cab 文件。 随即显示一个 .msi 文件。 选择该文件，右键单击并“提取”该文件。 使用该 .msi 文件来更新代理。

        ![提取 MDS 代理更新文件](./media/storsimple-virtual-array-install-update-06/extract-geneva-monitoring-agent-installer.png)

        > [!IMPORTANT]
        > 如果正在运行 StorSimple Update 0.5 (0.0.10293.0)，则无需更新 MDS 代理。

    - 包含关键 Windows 安全更新的三个文件 `windows8.1-kb4012213-x64`、`windows8.1-kb3205400-x64` 和 `windows8.1-kb4019213-x64`。


### <a name="install-the-update-or-the-hotfix"></a>安装更新或修补程序

在安装更新或修补程序之前，确保更新或修补程序已本地下载到主机上，或可通过网络共享访问。

使用此方法，在运行 GA 或 Update 0.1 软件版本的设备上安装更新。 完成此过程大约需 12 分钟。 执行以下步骤，安装更新或修补程序。

#### <a name="to-install-the-update-or-the-hotfix"></a>安装更新或修补程序

1. 在本地 Web UI 中，转到“维护” > “软件更新”。 记下运行的软件版本。 如果正在运行 10.0.10290.0，则在步骤 6 中无需更新 MDS 代理。
   
    ![更新设备](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. 在“更新文件路径”中，输入更新或修补程序的文件名。 也可以浏览到网络共享上的更新或修补程序安装文件。 单击“应用” 。
   
    ![更新设备](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. 显示一条警告。 假定 Virtual Array 为单节点设备，应用更新后，设备将重启并且会出现停机。 单击选中图标。
   
   ![更新设备](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. 更新启动。 成功更新设备后，该设备将重新启动。 本地 UI 在此期间不可访问。
   
    ![更新设备](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. 重新启动完成后，会转到“登录”页。 若要验证设备软件是否已更新，请在本地 Web UI 中，转到“维护” > “软件更新”。 Update 0.6 显示的软件版本应为 10.0.0.0.0.10293。
   
   > [!NOTE]
   > 我们在本地 Web UI 和 Azure 门户中报告的软件版本稍有不同。 例如，针对同一版本，本地 Web UI 报告 10.0.0.0.0.10293，而 Azure 门户则报告 10.0.10293.0。
   
    ![更新设备](./media/storsimple-virtual-array-install-update-06/update6m.png)

6. 如果应用此更新之前运行的是 StorSimple Virtual Array Update 0.5 (10.0.10290.0)，请跳过此步骤。 开始更新前，请记下步骤 1 中的软件版本。 如果正在运行 Update 0.5，则表示 MDS 代理已经是最新的。

    如果运行的软件版本低于 Update 0.5，则下一步是更新 MDS 代理。 在“软件更新”页上，转到“更新文件路径”并浏览到 `GenevaMonitoringAgentPackageInstaller.msi` 文件。 重复步骤 2-4。 在 Virtual Array 重启后，登录到本地 Web UI。

7. 重复步骤 2-4，使用文件 `windows8.1-kb4012213-x64`、`windows8.1-kb3205400-x64` 和 `windows8.1-kb4019213-x64` 安装 Windows 安全修补程序。 每次安装后 virtual array 都会重启，用户需登录本地 Web UI。

## <a name="next-steps"></a>后续步骤

详细了解如何[管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。

