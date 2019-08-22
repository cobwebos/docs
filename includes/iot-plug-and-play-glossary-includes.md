---
author: ChrisGMsft
ms.service: iot-pnp
ms.topic: include
ms.date: 06/28/2019
ms.author: chrisgre
ms.openlocfilehash: 7e8174855800bc6beea8750c32a6dd32588ccd9e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880457"
---
## <a name="iot-plug-and-play-device"></a>IoT 即插即用设备

IoT 即插即用设备通常是一种小规模、独立的计算设备, 它可以收集数据或控制其他设备, 并运行实现[设备功能模型](#device-capability-model)中声明的功能的软件或固件。  例如, IoT 即插即用设备可以是环境监视设备, 也可以是 agrigulture 灌溉系统的控制器。 云托管的 IoT 解决方案可以写入命令、控制和接收 IoT 即插即用设备中的数据。 可以通过[Azure IoT 认证设备目录](https://catalog.azureiotsolutions.com/)找到 IoT 即插即用设备。 目录中的每个 IoT 即插即用设备均已验证, 且具有[设备功能模型](#device-capability-model)。

## <a name="digital-twin"></a>数字输出

数字孪生是 IoT 即插即用设备的型号。  数字孪生使用[数字克隆定义语言](https://aka.ms/DTDL)进行建模。  可以使用 Azure IoT API 与数字孪生进行交互。 

## <a name="digital-twin-definition-language"></a>数字克隆定义语言

用于描述[IoT 即插即用设备](#iot-plug-and-play-device)的模型和接口的一种语言。  使用[数字克隆定义语言](https://aka.ms/DTDL)描述[数字克隆的](#digital-twin)功能, 并使 iot 平台和 iot 解决方案能够利用实体的语义。

## <a name="device-capability-model"></a>设备功能模型

设备功能模型描述设备, 并定义设备实现的接口集。 创建与物理设备、产品或 SKU 相对应的设备功能模型。

## <a name="interface"></a>接口

接口描述由设备或数字克隆实现的相关功能。 可以跨不同的功能模型重用接口。

## <a name="property"></a>属性

属性是在[接口](#interface)中定义的数据字段, 表示数字克隆的某种状态。 可以将属性声明为只读或可写。 只读属性由 IoT 即插即用设备本身的上下文中运行的代码 (如序列号) 设置。  可写属性由外部实体 (如警报阈值) 设置。

## <a name="telemetry"></a>遥测

在[接口](#interface)中定义的遥测字段表示度量值。 这些度量值通常是诸如传感器读数这样的值。

## <a name="command"></a>Command

在[接口](#interface)中定义的命令表示可以在数字克隆上执行的方法。 例如, 用于重置设备的 reset 命令。
