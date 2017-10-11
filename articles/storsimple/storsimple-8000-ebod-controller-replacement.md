---
title: "更换 StorSimple 8600 EBOD 控制器 | Microsoft Docs"
description: "说明如何移除和更换 StorSimple 8600 设备上的一个或两个 EBOD 控制器。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 45699c267d1009c4884dd164fd3f2950d6d5f555
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>更换 StorSimple 设备上的 EBOD 控制器

## <a name="overview"></a>概述
本教程介绍如何更换 Microsoft Azure StorSimple 设备上发生故障的 EBOD 控制器模块。 若要更换 EBOD 控制器模块，需执行以下操作：

* 移除发生故障的 EBOD 控制器
* 安装新的 EBOD 控制器

在开始之前，请注意以下信息：

* 空的 EBOD 模块必须插入到所有未使用的插槽中。 如果一个槽处于打开状态，机箱将无法正常散热。
* EBOD 控制器可以进行热插拔，也可以移除或更换。 在找到更换部件之前，不要移除发生故障的模块。 启动更换过程时，必须在 10 分钟内完成。

> [!IMPORTANT]
> 尝试移除或更换任何 StorSimple 组件之前，请确保查看[安全图标约定](storsimple-safety.md#safety-icon-conventions)以及其他[安全注意事项](storsimple-safety.md)。

## <a name="remove-an-ebod-controller"></a>移除 EBOD 控制器
在 StorSimple 设备中更换发生故障的 EBOD 控制器模块之前，请确保其他 EBOD 控制器模块处于活动和运行状态。 以下过程和表说明了如何移除 EBOD 控制器模块。

#### <a name="to-remove-an-ebod-module"></a>移除 EBOD 模块
1. 打开 Azure 门户。
2. 转到设备并导航到“设置” > “硬件运行状况”，确认活动 EBOD 控制器模块的 LED 状态为绿色，发生故障的 EBOD 控制器模块的 LED 为红色。
3. 在设备背面找到发生故障的 EBOD 控制器模块。
4. 将 EBOD 模块从系统中移除之前，请先移除将 EBOD 控制器模块连接到控制器的电缆。
5. 记下连接到控制器的 EBOD 控制器模块的确切 SAS 端口。 更换 EBOD 模块后，需将系统还原到此配置。
   
   > [!NOTE]
   > 通常情况下，这会是端口 A，在下图中标记为“主机进”。
   
    ![EBOD 控制器的底板](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **图 1** EBOD 模块的背面
   
   | 标签 | 说明 |
   |:--- |:--- |
   | 1 |故障 LED |
   | 2 |电源 LED |
   | 3 |SAS 连接器 |
   | 4 |SAS LED |
   | 5 |仅供工厂使用的串行端口 |
   | 6 |端口 A（主机进） |
   | 7 |端口 B（主机出） |
   | 8 |端口 C（仅供工厂使用） |

## <a name="install-a-new-ebod-controller"></a>安装新的 EBOD 控制器
以下过程和表说明了如何在 StorSimple 设备中安装 EBOD 控制器模块。

#### <a name="to-install-an-ebod-controller"></a>安装 EBOD 控制器
1. 检查 EBOD 设备是否有损坏，特别是接口连接器是否有损坏。 请勿在有连接器销弯曲的情况下安装新的 EBOD 控制器。
2. 闩锁打开后，将模块滑入机箱，直至闩锁啮合。
   
    ![安装 EBOD 控制器](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **图 2**  安装 EBOD 控制器模块
3. 合上闩锁。 闩锁啮合时，应该能听到咔嗒声。
   
    ![解开 EBOD 闩锁](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **图 3**  合上 EBOD 模块闩锁
4. 重新连接电缆。 使用更换前已有的准确配置。 请参阅下面的图和表，详细了解如何连接电缆。
   
    ![为 4U 设备进行电源布线](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **图 4**。 重新连接电缆
   
   | 标签 | 说明 |
   |:--- |:--- |
   | 1 |主机箱 |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |控制器 0 |
   | 5 |控制器 1 |
   | 6 |EBOD 控制器 0 |
   | 7 |EBOD 控制器 1 |
   | 8 |EBOD 机箱 |
   | 9 |配电装置 |

## <a name="next-steps"></a>后续步骤
详细了解 [StorSimple 硬件组件更换](storsimple-8000-hardware-component-replacement.md)。

