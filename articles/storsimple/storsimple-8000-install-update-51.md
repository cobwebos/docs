---
title: 在 StorSimple 8000 系列设备上安装更新 5.1 |Microsoft Docs
description: 介绍如何在 StorSimple 8000 系列设备上安装 StorSimple 8000 系列更新5.1。
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/05/2020
ms.author: alkohli
ms.openlocfilehash: 5b9958f3dd497aa612a92947b8d968439ef9d0e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575956"
---
# <a name="install-update-51-on-your-storsimple-device"></a>在 StorSimple 设备上安装更新5。1

## <a name="overview"></a>概述

本教程介绍如何通过 Azure 门户在运行早期软件版本的 StorSimple 设备上安装更新5.1。 <!--The hotfix method is used when you are trying to install Update 5.1 on a device running pre-Update 3 versions. The hotfix method is also used when a gateway is configured on a network interface other than DATA 0 of the StorSimple device and you are trying to update from a pre-Update 1 software version.-->

更新5.1 包括非中断安全更新。 可以通过 Azure 门户应用非中断性更新或常规更新 <!--or by the hotfix method-->.

> [!IMPORTANT]
>
> * 更新5.1 是必需的更新，应立即安装。 有关详细信息，请参阅 [Update 5.1 发行说明](storsimple-update51-release-notes.md)。
> * 在安装之前需进行一组手动的和自动的预检查，以便确定设备在硬件状态和网络连接性方面的运行状况。 仅当从 Azure 门户应用更新时，才会执行这些预检查。
> * 如果要使用修补程序方法安装，请联系 [Microsoft 支持部门](mailto:support@microsoft.com)。

<!--
> * We strongly recommend that when updating a device running versions prior to Update 3, you install the updates using hotfix method. If you encounter any issues, [log a support ticket](storsimple-8000-contact-microsoft-support.md). 


> * We recommend that you install the software and other regular updates via the Azure portal. You should only go to the Windows PowerShell interface of the device (to install updates) if the pre-update gateway check fails in the portal. Depending upon the version you are updating from, the updates may take 4 hours (or greater) to install. The maintenance mode updates must be installed through the Windows PowerShell interface of the device. As maintenance mode updates are disruptive updates, these result in a down time for your device.


> * If running the optional StorSimple Snapshot Manager, ensure that you have upgraded your Snapshot Manager version to Update 5.1 prior to updating the device.
-->

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-51-through-the-azure-portal"></a>通过 Azure 门户安装更新5。1

执行以下步骤，将设备更新为 [更新 5.1](storsimple-update51-release-notes.md)。

> [!NOTE]
> Microsoft 从设备中提取其他诊断信息。 因此，当运营团队确定有问题的设备以后，我们就可以更好地从设备中收集信息并诊断问题。

#### <a name="to-install-an-update-from-the-azure-portal"></a>从 Azure 门户安装更新

1. 在“StorSimple 服务”页上，选择设备。

    ![选择设备](./media/storsimple-8000-install-update-51/update1.png)

2. 导航到 "**设备设置**" "  >  **设备更新**"。

    !["设置" 边栏选项卡的屏幕截图，其中包含称为 "设备更新" 选项。](./media/storsimple-8000-install-update-51/update2.png)

3. 如果有新的更新可用，则会显示一条通知。 或者，在“设备更新”边栏选项卡中，单击“扫描更新”。******** 已创建一个用于扫描可用更新的作业。 在作业成功完成时，系统会发出通知。

    !["设置" 边栏选项卡和 "设备更新" 选项的屏幕截图，其中包含新的定期更新。](./media/storsimple-8000-install-update-51/update3.png)

4. 建议先查看版本信息，然后在设备上应用更新。 若要应用更新，请单击“安装更新”。**** 在“确认常规更新”边栏选项卡中，在应用更新之前，复查要完成的先决条件。**** 选中指示已做好更新准备的复选框，并单击“安装”。****

    !["设备更新" 边栏选项卡和 "安装更新" 选项的屏幕截图，其中包含 "同意" 选项和 "安装选项"。](./media/storsimple-8000-install-update-51/update4.png)

5. 此时会开始执行一系列先决条件检查。 这些检查包括：
   
   * **控制器健康状况检查**：验证两个设备控制器是否状况良好且联机。
   * **硬件组件运行状况检查**：验证 StorSimple 设备上的所有硬件组件是否状况良好。
   * **DATA 0 检查**：验证设备上是否已启用 DATA 0。 如果未启用此接口，必须将它启用，然后重试。

     只有所有检查都成功完成后才会下载并安装更新。 在进行检查的同时，系统会通知你。 如果预检查失败，系统会提供失败原因。 请解决这些问题，然后重试操作。 如果无法自行解决这些问题，可能需要联系 Microsoft 支持人员。

7. 预检查成功完成后，将创建更新作业。 成功创建更新作业后，系统会发出通知。
   
    ![显示 "正在启动软件更新作业" 的通知屏幕截图。](./media/storsimple-8000-install-update-51/update6.png)
   
    然后，在设备上应用更新。

9. 更新需要数小时才能完成。 随时可以选择更新作业并单击“详细信息”来查看作业详细信息。****

    !["设备更新" 边栏选项卡的屏幕截图，其中包含 "下载并安装正在进行的软件更新" 选项（称为 "已下载" 和 "安装更新"](./media/storsimple-8000-install-update-51/update8.png)

     还可以从“设备设置”>“作业”检索更新作业的进度****。 在“作业”边栏选项卡上，可以看到更新进度。****

     !["设置" 边栏选项卡的屏幕截图，其中包含被称为 "作业" 和 "作业" 边栏选项卡，显示更新进度。](./media/storsimple-8000-install-update-51/update7.png)

10. 在作业完成后，导航到“设备设置”>“设备更新”。**** 软件版本现在应当已更新。


验证设备是否正在运行 **StorSimple 8000 系列更新 5.1 (6.3.9600.17885) **。 应修改“上次更新日期”。****
<!-- 5.1 - KB 4542887-->

<!--You will now see that the Maintenance mode updates are available (this message might continue to be displayed for up to 24 hours after you install the updates). The steps to install maintenance mode update are detailed in the next section.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## Install Update 5.1 as a hotfix

The software versions that can be upgraded using the hotfix method are:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1
* Update 4
* Update 5

> [!NOTE]
> The recommended method to install Update 5.1 is through the Azure portal when trying to update from Update 3 and later version. When updating a device running versions prior to Update 3, use this procedure. You can also use this procedure if you fail the gateway check when trying to install the updates through the Azure portal. The check fails when you have a gateway assigned to a non-DATA 0 network interface and your device is running a software version earlier than Update 1.

The hotfix method involves the following three steps:

1. Download the hotfixes from the Microsoft Update Catalog.
2. Install and verify the regular mode hotfixes.
3. Install and verify the maintenance mode hotfix.

#### Download updates for your device

You must download and install the following hotfixes in the prescribed order and the suggested folders:

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Software update<br> Download both _HcsSoftwareUpdate.exe_ and _CisMSDAgent.exe_ |Regular <br></br>Non-disruptive |~ 25 mins |FirstOrderUpdate|

If updating from a device running Update 4, you only need to install the OS cumulative updates as second order updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |OS cumulative updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|

If installing from a device running Update 3 or earlier, install the following in addition to the cumulative updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI driver and firmware updates <br> USM firmware update (version 3.38) |Regular <br></br>Non-disruptive |~ 3 hrs <br> (includes 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |OS security updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |OS updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|



You may also need to install disk firmware updates on top of all the updates shown in the preceding tables. You can verify whether you need the disk firmware updates by running the `Get-HcsFirmwareVersion` cmdlet. If you are running these firmware versions: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, then you do not need to install these updates.

| Order | KB | Description | Update type | Install time | Install in folder|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Disk firmware |Maintenance <br></br>Disruptive |~ 30 mins | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * If updating from Update 4, the total install time is close to 4 hours.
> * Before using this procedure to apply the update, make sure that both the device controllers are online and all the hardware components are healthy.

Perform the following steps to download and install the hotfixes.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]
-->
<!--
[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]
-->

## <a name="next-steps"></a>后续步骤

了解有关 [更新5.1 版本](storsimple-update51-release-notes.md)的详细信息。
