---
title: "适用于 StorSimple 10 GbE 接口的硬件 | Microsoft Docs"
description: "介绍 StorSimple 设备上支持的适用于 10 GbE 网络接口的小型可插拔 (SFP) 收发器、电缆和交换机。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: df8d40c7-f5ad-4f84-93eb-779fbd5f7243
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: db03b3cd668bf8e35913872ac4225de6d4d3edd1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>StorSimple 设备上支持的适用于 10 GbE 网络接口的硬件
## <a name="overview"></a>概述
本文将提供有关适用于 Microsoft Azure StorSimple 设备的附属硬件的信息。

## <a name="list-of-devices-tested-by-microsoft"></a>Microsoft 已测试的设备列表
Microsoft 已测试以下小型可插拔 (SFP) 收发器、电缆和交换机，确保它们能以最佳方式和设备一起工作。 （下表会随着新硬件进行测试而更新。）

### <a name="sfp-transceivers"></a>SFP+ 收发器
| 制造商 | 模型 |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>电缆
| 序列 否。 | 制造商 | 模型 |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Tripp-Lite |N820-05M (OM3) |

### <a name="switches"></a>交换机
| 序列 否。 | 制造商 | 模型 |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>已在现场测试的设备列表
本部分包含 StorSimple 客户已在现场成功部署的设备的列表。 这些设备尚未经过 Microsoft 测试，但可能可以与 StorSimple 设备一起使用。

| 参数 | 值 |
| --- | --- |
| 交换机制造商 |Juniper |
| 交换机型号 |ex4550-32F |
| 交换机操作系统版本 |JunOS 12.3R9.4 |
| 刀片型号 |板载端口 (PIC 0) |
| 收发器制造商 |Juniper |
| 收发器型号 |端口号 740-021308 <br></br> 端口号 740-030658 |
| 收发器固件版本 |修订版 01 版本 0.0（已报告） |
| 电缆型号 |双工跳线 LC/LC 50/125µ、OM3、LSZH |
| StorSimple 型号 |8600 |
| StorSimple 软件版本 |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>OEM 提供商 (Mellanox) 已测试的设备列表
Mellanox 已测试以下小型可插拔 (SFP) 收发器、电缆和交换机，确保它们能以最佳方式与 Mellanox 网络接口一起工作，例如 StorSimple 设备上的 10 GbE 网络接口。

### <a name="cables-and-modules-supported-by-mellanox"></a>Mellanox 支持的电缆和模块
下表列出了 Mellanox 支持的电缆和模块。 这些设备尚未经过 Microsoft 测试，但可能可以与 StorSimple 设备一起使用。

| 序列 否。 | Speed | 模型 | 说明 | 制造商 |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |CAB-SFP-SFP-1M |无源铜缆 SFP+ 10 Gb/s 1 米 |Arista |
| 2. |10 GbE |CAB-SFP-SFP-2M |无源铜缆 SFP+ 10 Gb/s 2 米 |Arista |
| 3. |10 GbE |CAB-SFP-SFP-3M |无源铜缆 SFP+ 10 Gb/s 3 米 |Arista |
| 4. |10 GbE |CAB-SFP-SFP-5M |无源铜缆 SFP+ 10 Gb/s 5 米 |Arista |
| 5. |10 GbE |Cisco SFP-H10GBCU1M |Cisco SFP+ 电缆 |Cisco |
| 6. |10 GbE |Cisco SFP-H10GBCU3M |Cisco SFP+ 电缆 |Cisco |
| 7. |10 GbE |Cisco SFP-H10GBCU5M |Cisco SFP+ 电缆 |Cisco |
| 8. |10 GbE |J9281B HP X242 10G |SFP+ TO SFP+ 1 米直连式铜缆 |HP |
| 9. |10 GbE |455883-B21 HP BLc |10Gb SR SFP+ 光纤 |HP |
| 10. |10 GbE |455886-B21 HP BLc |10Gb LR SFP+ 光纤 |HP |
| 11. |10 GbE |487649-B21 HP BLc |SFP + 0.5 米 10GbE 铜缆 |HP |
| 12. |10 GbE |487652-B21 HP BLc |SFP+ 1 米 10GbE 铜缆 |HP |
| 13. |10 GbE |487655-B21 HP BLc |SFP+ 3 米 10GbE 铜缆 |HP |
| 14. |10 GbE |487658-B21 HP BLc |SFP+ 7 米 10GbE 铜缆 |HP |
| 15. |10 GbE |537963-B21 HP BLc |SFP+ 5 米 10GbE 铜缆 |HP |
| 16. |10 GbE |AP784A HP |3 米 C 系列无源 SFP+ 铜缆 |HP |
| 17. |10 GbE |AP785A HP |5 米 C 系列无源 SFP+ 铜缆 |HP |
| 18. |10 GbE |AP818A HP |1 米 B 系列有源 SFP+ 铜缆 |HP |
| 19. |10 GbE |AP819A HP |3 米 B 系列有源 SFP+ 铜缆 |HP |
| 20. |10 GbE |J9150A HP |X132 10G SFP+ LC SR 收发器 |HP |
| 21. |10 GbE |J9151A HP |X132 10G SFP+ LC LR 收发器 |HP |
| 22. |10 GbE |J9283B HP |X242 10G SFP+ SFP+ 3 米 DAC 电缆 |HP |
| 23. |10 GbE |J9285B HP |X242 10G SFP+ SFP+ 7 米 DAC 电缆 |HP |
| 24. |10 GbE |JD095B HP |X240 10G SFP+ SFP+ 0.65 米 DAC 电缆 |HP |
| 25. |10 GbE |JD096B HP |X240 10G SFP+ SFP+ 1.2 米 DAC 电缆 |HP |
| 26. |10 GbE |JD097B HP |X240 10G SFP+ SFP+ 3 米 DAC 电缆 |HP |
| 27. |10 GbE |MAM1Q00A QSA Mellanox |QSFP To SFP+ 适配器 |Mellanox 技术 |
| 28. |10 GbE |MC2309124-006 Mt |无源铜缆 1x SFP+ To QSFP 10Gb/s 24AWG 7 米 |Mellanox 技术 |
| 29. |10 GbE |MC2309124-007 Mt |无源铜缆 1x SFP+ To QSFP 10Gb/s 24AWG 7 米 |Mellanox 技术 |
| 30. |10 GbE |MC2309130-003 Mt |无源铜缆 1x SFP+ To QSFP 10Gb/s 30AWG 3 米 |Mellanox 技术 |
| 31. |10 GbE |MC2309130-00A Mt |无源铜缆 1x SFP+ To QSFP 10Gb/s 30AWG 0.5 米 |Mellanox 技术 |
| 32. |10 GbE |MC3309124-005 Mt |无源铜缆 1x SFP+ 10Gb/s 24AWG 5 米 |Mellanox 技术 |
| 33. |10 GbE |MC3309124-007 Mt |无源铜缆 1x SFP+ 10Gb/s 24AWG 7 米 |Mellanox 技术 |
| 34. |10 GbE |MC3309130-003 Mt |无源铜缆 1x SFP+ 10Gb/s 30AWG 3 米 |Mellanox 技术 |
| 35. |10 GbE |MC3309130-00A Mt |无源铜缆 1x SFP+ 10Gb/s 30AWG 0.5 米 |Mellanox 技术 |

### <a name="switches-supported-by-mellanox"></a>Mellanox 支持的交换机
下表列出了 Mellanox 支持的交换机。 这些设备尚未经过 Microsoft 测试，但可能可以与 StorSimple 设备一起使用。

| 序列 否。 | Speed | 模型 | 说明 | 制造商 |
| --- | --- | --- | --- | --- |
| 1. |10GbE |516733-B21 |HP ProCurve 6120XG 10GbE 以太网刀片式交换机 |HP |
| 2. |10GbE |538113-B21 |HP 10GbE 直通模块 (PTM) |HP |
| 3. |10GbE |EN4093 |IBM PureFlex System 结构 EN4093 10 千兆位可扩展的交换机模块 |IBM |
| 4. |1GbE |3020 |Cisco Catalyst 3020 1GbE 刀片式交换机 |Cisco |
| 5. |1GbE |3020X |Cisco Catalyst 3020X 1GbE 刀片式交换机 |Cisco |
| 6. |1GbE |438030-B21 |HP 1GbE 交换机模块 - GbE2c 第二/三层以太网刀片式交换机 |HP |
| 7. |1GbE |6120G |HP ProCurve 6120G/XG 1GbE 刀片式交换机 |HP |

## <a name="next-steps"></a>后续步骤
[详细了解 StorSimple 的硬件组件和状态](storsimple-monitor-hardware-status.md)。

