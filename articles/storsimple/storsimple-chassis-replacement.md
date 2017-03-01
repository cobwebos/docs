---
title: "更换 StorSimple 8000 系列设备的机箱 | Microsoft 文档"
description: "介绍如何移除和更换 StorSimple 主机箱或 EBOD 机箱的底盘。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 537659ed-4c46-49c1-b1e4-186262f2542d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: eb56cae77722268f42e5126c45ad2878af7db94a
ms.openlocfilehash: 5295c5dd039b1d4746ebaaf90372932e4c3e7c26


---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>更换 StorSimple 设备上的底盘
## <a name="overview"></a>概述
本教程介绍如何移除和更换 StorSimple 8000 系列设备中的底盘。 StorSimple 8100 机型是单机箱设备（一个底盘），而 8600 是双机箱设备（两个底盘）。 对于 8600 模型，设备中可能有两个底盘（主机箱的底盘或 EBOD 机箱的底盘）发生故障。

不管哪种情况，Microsoft 随附的更换用底盘均为空。 不包括电源和散热模块 (PCM)、控制器模块、固态磁盘驱动器 (SSD)、硬盘驱动器 (HDD) 或 EBOD 模块。

> [!IMPORTANT]
> 移除和更换底盘之前，请查看 [StorSimple 硬件组件更换](storsimple-hardware-component-replacement.md)中的安全信息。
> 
> 

## <a name="remove-the-chassis"></a>移除底盘
执行以下步骤，移除 StorSimple 设备上的底盘。

#### <a name="to-remove-a-chassis"></a>移除底盘
1. 确保 StorSimple 设备已关闭并断开与所有电源的连接。
2. 根据需要移除所有网络电缆和 SAS 电缆。
3. 从机架中移除部件。
4. 移除每个驱动器，并记录是从哪些槽中移除的。 有关详细信息，请参阅[移除磁盘驱动器](storsimple-disk-drive-replacement.md#remove-the-disk-drive)。
5. 在 EBOD 机箱（如果这是发生故障的底盘）上移除 EBOD 控制器模块。 有关详细信息，请参阅[移除 EBOD 控制器](storsimple-ebod-controller-replacement.md#remove-an-ebod-controller)。 
   
    在主机箱（如果这是发生故障的底盘）上移除控制器，并记录是从哪些槽中移除的。 有关详细信息，请参阅[移除控制器](storsimple-controller-replacement.md#remove-a-controller)。

## <a name="install-the-chassis"></a>安装底盘
执行以下步骤，在 StorSimple 设备上安装底盘。

#### <a name="to-install-a-chassis"></a>安装底盘
1. 将底盘装载在机架中。 有关详细信息，请转到[通过机架安装 StorSimple 8100 设备](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device)或[通过机架安装 StorSimple 8600 设备](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device)。
2. 在机架中安装底盘后，请在原来的位置安装控制器模块。
3. 将驱动器安装在原来的位置和插槽中。
   
   > [!NOTE]
   > 建议先在插槽中安装 SSD，然后再安装 HDD。
   > 
   > 
4. 等设备安装在机架中并安装组件以后，将设备连接到适当的电源，然后将设备开机。 有关详细信息，请参阅 [StorSimple 8100 设备布线](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device)或 [StorSimple 8600 设备布线](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device)。

## <a name="next-steps"></a>后续步骤
详细了解 [StorSimple 硬件组件更换](storsimple-hardware-component-replacement.md)。




<!--HONumber=Jan17_HO4-->


