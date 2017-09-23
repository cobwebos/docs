---
title: "StorSimple 设备的安全性 | Microsoft Docs"
description: "描述安全约定、指南和注意事项，并解释如何安全地安装和运行你的 StorSimple 设备。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2017
ms.author: alkohli
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: e45b09a62f33fd9811714febd63a74149784980d
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017

---
# <a name="safely-install-and-operate-your-storsimple-device"></a>安全地安装和运行你的 StorSimple 设备
![警告图标](./media/storsimple-safety/IC740879.png)
![阅读安全注意事项图标](./media/storsimple-safety/IC740885.png) **阅读安全和运行状况信息**

阅读本文中适用于 Microsoft Azure StorSimple 设备的所有安全和运行状况信息。 保留 StorSimple 设备附带的所有打印的指南以供将来参考。 如果不按说明对此产品进行正确设置、使用和护理等操作，伤亡或设备损坏风险将会增加。 [本指南提供可下载版本](http://www.microsoft.com/download/details.aspx?id=44233)。

## <a name="safety-icon-conventions"></a>安全图标约定
以下是在查看安全预防措施时出现的图标，安装和运行 Microsoft Azure StorSimple 设备时应遵循这些措施。

| 图标 | 说明 |
|:--- |:--- |
| ![危险图标](./media/storsimple-safety/IC740879.png) **DANGER!** |指示如果未能避免危险情况，将导致死亡或重伤。 此警示语仅限于最为极端的情况。 |
| ![警告图标](./media/storsimple-safety/IC740879.png) **WARNING!** |指示如果未能避免危险情况，可能导致死亡或重伤。 |
| ![警告图标](./media/storsimple-safety/IC740879.png) **CAUTION!** |指示如果未能避免危险情况，可能导致轻伤或重伤。 |
| ![注意图标](./media/storsimple-safety/IC740881.png) **NOTICE:** |指示应该给予重视但不涉及危险情况的信息。 |
| ![电击图标](./media/storsimple-safety/IC740882.png) **Electrical Shock Hazard** |高电压 |
| ![重物图标](./media/storsimple-safety/IC740883.png) **Heavy Weight** | |
| ![用户不可维修的部件图标](./media/storsimple-safety/IC740879.png) **No User Serviceable Parts** |除非经过适当的培训，否则请勿接触。 |
| ![阅读安全注意事项图标 ](./media/storsimple-safety/IC740885.png)**Read All Instructions First** | |
| ![倾翻危险图标](./media/storsimple-safety/IC740886.png) **Tip Hazard** | |

## <a name="handling-precautions"></a>操作注意事项
![警告图标](./media/storsimple-safety/IC740879.png)![重物图标](./media/storsimple-safety/IC740883.png) **WARNING!** 

若要降低受伤的风险：

* 完整配置的机箱重量可达 32 kg (70 lbs)；请不要尝试自行提起。
* 在移动机箱之前，始终确保有两个人来搬运。 请注意，一个人尝试自行搬动此重物可能会受伤。
* 不要通过位于此装置背面的电源和冷却模块 (PCM) 上的手柄抬起机箱。 这些设计不是用来承重的。

## <a name="connection-precautions"></a>连接注意事项
![警告图标](./media/storsimple-safety/IC740879.png) ![电击图标](./media/storsimple-safety/IC740882.png) **WARNING!**

若要降低受伤、电击或死亡的可能性：

* 当由多个 AC 电源供电时，断开所有电源以完全隔离。
* 在移动之前，或者如果你认为该设备出现了任何形式的损坏，请永久拔掉电源。
* 为电源线提供安全接地连接。 在接通电源前，请验证机箱的接地符合国家和地区的要求。
* 从机箱中取下 PCM 之前，请确保始终断开电源连接。
* 考虑到电源线上的插头是主要的断开设备，请确保插座口位于设备附近且易于访问。

![警告图标](./media/storsimple-safety/IC740879.png) ![电击图标](./media/storsimple-safety/IC740882.png) **WARNING!**

若要降低电气连接温度过高或着火的可能性：

* 使用带电气过载保护的合适电源，以满足技术规格中详述的要求。
* 请勿使用分叉型电源线（“Y”导线）。
* 为了符合适用的安全、发射和热要求，不应移除任意盖子，并且所有托架中必须填充插件模块或驱动器挡板。
* 确保以制造商指定的方式使用设备。 如果未以制造商指定的方式使用本设备，则该设备提供的保护可能会减弱。

![注意图标](./media/storsimple-safety/IC740881.png) **NOTICE:**

为正确操作设备并防止产品受损：

* 设备背面的 RJ45 端口仅适用于以太网连接。 请勿将其连接到电信网络。
* 请务必在可容纳从前到后冷却设计的机架中安装该设备。
* 所有插件模块和挡板都是系统机箱的一部分。 只能在可以立即添加替代件时，才能将其移除。 未将所有模块或挡板安装到位前，不得运行系统。

## <a name="rack-system-precautions"></a>机架系统注意事项
在机架机柜中装载设备时，必须考虑以下安全要求。

![警告图标](./media/storsimple-safety/IC740879.png)![倾翻危险图标](./media/storsimple-safety/IC740886.png) **WARNING!**

若要减少因倾翻受伤的可能性：

* 机架设计应支持所安装机箱的总重量，并且应增加适当的稳定功能，以防机架在安装或正常使用期间倾翻或被推倒。
* 装载机架时，从底部向上装入机架，从顶部向下拆卸。
* 每次从机架滑出的机箱不能超过一个，以避免机架倒塌的危险。

![警告图标](./media/storsimple-safety/IC740879.png) ![电击图标](./media/storsimple-safety/IC740882.png) **WARNING!**

若要降低受伤、电击或死亡的可能性：

* 机架应具有安全的配电系统。 它必须为机箱提供过流保护，并且决不能过载安装机箱总数。 应遵循名牌上显示的额定功耗。
* 配电系统必须在机架中为每个机箱提供可靠的接地。
* 配电系统的设计必须考虑所有机箱中所有电源的总接地泄漏电流。 请注意，每个机箱中的每个电源在 264 伏，60 Hz 的情况下最大接地泄漏电流为 1.0 mA。 机架可能需要标有“高泄漏电流。 在连接至电源前，接地连接至关重要。”
* 当为机架配置机箱时，必须满足 UL 60950-1 和 IEC 60950-1/EN 60950-1 的安全要求。

![注意图标](./media/storsimple-safety/IC740881.png) **NOTICE:**

机架系统的正确冷却：

* 确保机架设计考虑到 35 摄氏度（95 华氏度）的最高机箱操作环境温度。
* 系统采用低压后排气安装运行（机架门和障碍物生成的背压不能超过 5 帕斯卡 [0.5 毫米水位]）。

## <a name="power-cooling-module-pcm-precautions"></a>电源冷却模块 (PCM) 注意事项
该设备的设计可操作两台 PCM。 每个 PCM 都有电源和双轴风扇。 在紧急情况下，系统可以在其中一个电源发生故障时继续正常操作。 必须始终安装两个 PCM（以及相应的电源）。 单个 PCM 不提供冗余电源。 因此，即使一个 PCM 发生故障，也会导致停机或可能造成数据丢失。

![警告图标](./media/storsimple-safety/IC740879.png) ![电击图标](./media/storsimple-safety/IC740882.png) **WARNING!**

若要降低受伤、电击或死亡的可能性：

* 不要从 PCM 取下盖子。 内部存在电击的危险。 若要返回 PCM 并获取更换件，请 [联系 Microsoft 支持部门](storsimple-contact-microsoft-support.md)。

![注意图标](./media/storsimple-safety/IC740881.png) **NOTICE:**

为正确操作设备并防止产品受损：

* 必须在 24 小时内更换故障的 PCM。 为进行更换移除 PCM 之后，必须在移除后的 10 分钟内完成更换。
* 除非可以立即安装更换，否则请勿移除 PCM。 所有模块未准备就绪时，请勿对机箱进行操作。

## <a name="electrostatic-discharge-esd-precautions"></a>静电放电 (ESD) 预防措施
![注意图标](./media/storsimple-safety/IC740881.png) **NOTICE:**

请遵循以下与 ESD 相关的注意事项。

* 确保已安装并检查合适的防静电腕带或踝带。
* 在处理模块和组件时，请遵守所有常见的 ESD 注意事项。
* 避免接触底板组件和模块连接器。
* ESD 损坏不在保修范围内。

## <a name="battery-disposal-precautions"></a>电池处置注意事项
电源使用特殊电池在临时性的短期停电期间保护内存中的内容。 电池装在 PCM 中。 请注意以下有关电池的信息。

![警告图标](./media/storsimple-safety/IC740879.png) **WARNING!**

若要降低短路、火灾、 爆炸、 受伤或死亡的风险：

* 根据国家/地区法规处置用过的电池。
* 请勿拆卸、碾碎或在 60 摄氏度（140 华氏度）以上加热或焚烧电池。 仅使用提供的电池更换 PCM 电池。 使用其他电池可能存在火灾或爆炸风险。
* 如果要从电源中将其移除，请使用电池上的保护端盖。

![注意图标](./media/storsimple-safety/IC740881.png) **NOTICE:**

当通过船运或空运对电池进行运输时，请遵循 [http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx) 中提供的 IATA 锂电池指南文档

查看完这些安全注意事项之后，后续步骤是对设备进行解包、安装机架并连接电缆。

## <a name="next-steps"></a>后续步骤
* 对于对 8100 设备，请转到 [安装 StorSimple 8100 设备](storsimple-8100-hardware-installation.md)。
* 对于对 8600 设备，请转到 [安装 StorSimple 8600 设备](storsimple-8600-hardware-installation.md)。


