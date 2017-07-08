---
title: "打开或关闭 StorSimple 设备 | Microsoft Docs"
description: "介绍如何打开新 StorSimple 设备，打开已关闭或断电的设备，以及关闭正在运行的设备。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 532ff423ff53567b6ce40c0ea7ec09a689cee1e7
ms.openlocfilehash: 74196c7d3989cc748a27026c04ea837b29a2785f
ms.contentlocale: zh-cn
ms.lasthandoff: 06/06/2017


---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>打开或关闭 StorSimple 8000 系列设备
## <a name="overview"></a>概述
在正常的系统操作过程中，无需关闭 Microsoft Azure StorSimple 设备。 但是，你可能需要打开新设备或不得已关闭了的设备。 在必须更换故障硬件，移动设备的物理位置或让设备暂停服务的情况下，通常需要关机。 本教程将介绍在不同情况下打开和关闭 StorSimple 设备所需的过程。

## <a name="turn-on-a-new-device"></a>打开新设备
根据设备是 8100 还是 8600 型号，第一次打开 StorSimple 设备的步骤有所不同。 8100 采用单个主机箱，而 8600 是配有主机箱和 EBOD 机箱的双机箱设备。 将在以下各节中介绍这两个型号的详细步骤。

* [只有主机箱的新设备](#new-device-with-primary-enclosure-only)
* [配有 EBOD 机箱的新设备](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>只有主机箱的新设备
StorSimple 8100 型号是单机箱设备。 设备包括冗余电源和冷却模块 (PCM)。 这两个模块必须安装并连接到不同电源，以确保高可用性。

执行以下步骤，为设备进行布线以接通电源。

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> 有关完整的设备安装和布线说明，请转到 [安装 StorSimple 8100 设备](storsimple-8100-hardware-installation.md)。 请确保严格按照这些说明进行操作。
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>配有 EBOD 机箱的新设备
StorSimple 8600 型号配有一个主机箱和一个 EBOD 机箱。 需要将这两个装置连接到一起以获得串行 SCSI (SAS) 连接和电源。

第一次设置此设备时，首先执行 SAS 布线步骤，然后完成电源布线步骤。

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> 有关完整的设备安装和布线说明，请转到 [安装 StorSimple 8600 设备](storsimple-8600-hardware-installation.md)。 请确保严格按照这些说明进行操作。
> 
> 

## <a name="turn-on-a-device-after-shutdown"></a>关闭后打开设备
根据设备是 8100 还是 8600 型号，在 StorSimple 设备关闭后重新打开的步骤有所不同。 8100 采用单个主机箱，而 8600 是配有主机箱和 EBOD 机箱的双机箱设备。

* [只有主机箱的设备](#device-with-primary-enclosure-only)
* [配有 EBOD 机箱的设备](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>只有主机箱的设备
关闭后，请使用以下过程打开配有主机箱但没有 EBOD 机箱的 StorSimple 设备。

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>打开只有主机箱的新设备
1. 请确保电源和冷却模块 (PCM) 上的电源开关处于 OFF（关）位置。 如果这两个模块的开关不在 OFF（关）位置，请将其拨至 OFF（关）位置，并等待指示灯熄灭。
2. 将两个 PCM 的电源开关切换到 ON（开）位置，即可打开设备。 现在，设备应打开。
3. 检查以下项目，确认设备完全开启：
   
   1. 两个 PCM 模块上的 OK（确定）LED 为绿色。
   2. 这两个控制器上的状态 LED 呈现固定的绿色。
   3. 其中一个控制器上的蓝色 LED 一直闪烁，指示控制器处于活动状态。
      
      如果不满足上述任一条件，则设备不正常。 请 [联系 Microsoft 支持部门](storsimple-contact-microsoft-support.md)。

### <a name="device-with-ebod-enclosure"></a>配有 EBOD 机箱的设备
关闭后，请使用以下过程打开配有主机箱和 EBOD 机箱的 StorSimple 设备。 请严格按照所述顺序执行每个步骤。 否则，可能会导致数据丢失。

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>打开配有主机箱和 EBOD 机箱的设备
1. 请确保 EBOD 机箱连接到主机箱。 有关详细信息，请参阅[安装 StorSimple 8600 设备](storsimple-8600-hardware-installation.md)。
2. 确保 EBOD 和主机箱上的电源和冷却模块 (PCM) 处于 OFF（关）位置。 如果这两个模块的开关不在 OFF（关）位置，请将其拨至 OFF（关）位置，并等待指示灯熄灭。
3. 将两个 PCM 的电源开关切换到 ON（开）位置，即可打开 EBOD 机箱。 现在，PCM LED 应为绿色。 此装置上的绿色 EBOD 控制器 LED 指示 EBOD 机箱已打开。
4. 将两个 PCM 的电源开关切换到 ON（开）位置，即可打开主机箱。 现在，整个系统应该打开。
5. 确认 SAS LED 为绿色，这是为了确保 EBOD 机箱与主机箱之间的连接良好。

## <a name="turn-on-a-device-after-a-power-loss"></a>断电后打开设备
停电或电源中断会关闭 StorSimple 设备。 停电可能发生在其中一个电源或两个电源上。 根据设备的型号是 8100 还是 8600，恢复步骤会有所不同。 8100 采用单个主机箱，而 8600 是配有主机箱和 EBOD 机箱的双机箱设备。 本部分将介绍每种情况的恢复过程。

* [只有主机箱的设备](#8100)
* [配有 EBOD 机箱的设备](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>只有主机箱的设备 <a name="8100">
如果其中一个电源中断，系统仍可以继续正常运行。 但是，为了确保设备的高可用性，请尽早恢复电源供电。

如果两个电源都发生停电或供电中断，则系统将以有序且受控的方式关闭。 电源恢复时，系统将自动打开。

### <a name="device-with-ebod-enclosure-a-name8600"></a>配有 EBOD 机箱的设备 <a name="8600">
#### <a name="power-loss-on-one-power-supply"></a>一个电源中断
如果主机箱或 EBOD 机箱上的其中一个电源中断，系统仍可以继续正常运行。 但是，为了确保设备的高可用性，请尽早恢复电源供电。

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>主机箱和 EBOD 机箱上的两个电源都中断
如果两个电源都发生停电或供电中断，则 EBOD 机箱立即关闭，并且主机箱将以有序且受控的方式关闭。 电源恢复时，设备将自动启动。

如果手动关闭电源，请执行以下步骤恢复系统的电源。

1. 打开 EBOD 机箱。
2. EBOD 机箱打开后，请打开主机箱。

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>EBOD 机箱上的两个电源都中断
设置电缆时，必须确保 EBOD 绝不会单独连接到独立的 PDU。 如果 EBOD 机箱和主机箱同时发生故障，系统将恢复。

如果只有 EBOD 机箱上的两个电源出现故障，则系统将不会自动恢复。 请执行以下步骤，打开系统并将其恢复到正常状态：

1. 如果已打开主机箱，请关闭电源和冷却模块 (PCM)。
2. 请等待几分钟，让系统关闭。
3. 打开 EBOD 机箱。
4. EBOD 机箱打开后，请打开主机箱。

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>在主机箱和 EBOD 机箱的连接中断后打开设备
如果备用控制器与相应的 EBOD 控制器之间的连接中断，则该设备将继续工作。 如果系统的主动控制器与相应的 EBOD 控制器之间的连接中断，应发生故障转移，而且该设备应继续照常工作。

拔下两个串行 SCSI (SAS) 电缆，或 EBOD 机箱与主机箱之间的连接断开时，设备将停止工作。 在这种情况下，请执行以下步骤。

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>在连接断开后打开设备
1. 找到该设备的背面。
2. 如果 EBOD 机箱与主机箱之间的 SAS 电缆连接中断，则 EBOD 机箱上的所有 SAS 通道 LED 都将熄灭。
3. 关闭 EBOD 机箱和主机箱上的电源和冷却模块 (PCM)。
4. 请等到两个机箱背面的所有灯都熄灭。
5. 重新插入 SAS 电缆，并确保 EBOD 机箱与主机箱之间的连接良好。
6. 将两个 PCM 的开关切换到 ON（开）位置，即可打开 EBOD 机箱。
7. 检查绿色 LED 是否亮起，确保 EBOD 机箱已打开。
8. 打开主机箱。
9. 检查控制器的绿色 LED 是否亮起，确保主机箱已打开。
10. 检查 SAS 通道 LED（每个 EBOD 控制器有 4 个）是否全都亮起，以便确认主机箱与 EBOD 机箱的连接良好。

> [!IMPORTANT]
> 如果 SAS 电缆有缺陷或 EBOD 机箱与主机箱之间的连接不正确，当你打开系统时，它将进入恢复模式。 如果发生这种情况，请 [联系 Microsoft 支持部门](storsimple-contact-microsoft-support.md)。
> 
> 

## <a name="turn-off-a-running-device"></a>关闭正在运行的设备
如果要移动正在运行的 StorSimple 设备并使其停止服务，或有故障组件需要更换，可能需要将该设备关闭。 根据 StorSimple 设备的型号是 8100 还是 8600，步骤会有所不同。 8100 采用单个主机箱，而 8600 是配有主机箱和 EBOD 机箱的双机箱设备。 本部分将详细介绍关闭正在运行的设备的步骤。

* [配有主机箱的设备](#8100a)
* [配有 EBOD 机箱的设备](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>配有主机箱的设备 <a name="8100a">
若要以有序且受控的方式关闭设备，可以通过 Azure 经典门户或 Windows PowerShell for StorSimple 执行该任务。 

> [!IMPORTANT]
> 请勿使用设备背面的电源按钮来关闭正在运行的设备。
> 
> 关闭设备之前，确保所有的设备组件都运行正常。 在 Azure 经典门户中，请导航到“**设备**”“ > **维护** > ”“**硬件状态**”，并确认所有组件的状态都为绿色。 此方法仅适用于正常运行的系统。 如果正在关闭系统，以便更换出现故障的组件，你会在“**硬件状态**”中看到相应组件的状态为失败（红色）或降级（黄色）。
> 
> 

访问 Windows PowerShell for StorSimple 或 Azure 经典门户之后，请按照 [关闭 StorSimple 设备](storsimple-manage-device-controller.md#shut-down-a-storsimple-device) 中的步骤执行操作。 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>配有 EBOD 机箱的设备 <a name="8600a">
> [!IMPORTANT]
> 在关闭主机箱和 EBOD 机箱之前，请确保所有的设备组件都正常工作。 在 Azure 经典门户中，请导航到“**设备**”“ > **维护** > ”“**硬件状态**”，并确认所有组件都正常工作。
> 
> 

#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>关闭正在运行的配有 EBOD 机箱的设备
1. 请按照 [关闭 StorSimple 设备](storsimple-manage-device-controller.md#shut-down-a-storsimple-device) 中列出的所有步骤关闭主机箱。
2. 关闭主机箱后，通过关闭电源和冷却模块 (PCM) 的开关来关闭 EBOD。
3. 要验证 EBOD 是否已关闭，请检查 EBOD 机箱背面的所有灯是否均已熄灭。

> [!NOTE]
> 系统关闭之前，不应拔掉用于将 EBOD 机箱连接到主机箱的 SAS 电缆。
> 
> 

## <a name="next-steps"></a>后续步骤
如果在打开或关闭 StorSimple 设备时遇到问题，请 [联系 Microsoft 支持部门](storsimple-contact-microsoft-support.md)。


