---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 0e8db8779958afe1fd3cf4bf12f2a6fd4a97c61c
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165302"
---
<!--author=alkohli last changed: 9/16/15-->

#### <a name="to-cable-for-power"></a>进行电源布线
1. 请确保每个电源和冷却模块 (PCM) 上的电源开关处于“关”位置。
2. 将电源线连接到主机箱中的每个 PCM。
3. 将电源线连接到配电装置 (PDU)，如下图所示。 请确保两个 PCM 使用单独的电源。
   
   > [!IMPORTANT]
   > 要确保系统的高可用性，我们建议严格遵循下图所示的电源布线方案。 
   > 
   > 
   
    ![为 2U 设备进行电源布线](./media/storsimple-cable-8100-for-power/HCSCableYour2UDeviceforPower.png)
   
    **8100 设备上的电源布线**
   
   | 标签 | Description |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |控制器 1 |
   | 3 |控制器 0 |
   | 4 |PCM 1 |
   | 5 |PDU |
4. 要打开系统，则将两个 PCM 的电源开关切换到“开”位置。

