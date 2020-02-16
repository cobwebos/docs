---
title: Azure 物联网 (IoT) 简介
description: 简单介绍 Azure IoT 和 IoT 服务的基础知识，包括说明如何使用 IoT 的示例。
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.openlocfilehash: c79f18669e1b13f79491e98658107221b43f3ff5
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046173"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Azure 物联网 (IoT) 是什么？

Azure 物联网 (IoT) 是 Microsoft 托管的云服务的集合，这些服务用于连接、监视和控制数十亿项 IoT 资产。 更简单地讲，IoT 解决方案由一个或多个 IoT 设备构成，这些设备与云中托管的一个或多个后端服务通信。 

## <a name="iot-devices"></a>IoT 设备

IoT 设备通常包括一块装有传感器的电路板，这些传感器使用 WiFi 连接到 Internet。 例如：

* 远程油泵上的压力传感器。
* 空调设备中的温度和湿度传感器。
* 电梯中的加速计。
* 房间中的感测器。

不同的制造商提供多种设备用于构建解决方案。 有关经认证可与 Azure IoT 中心配合使用的设备列表，请参阅 [Azure IoT 认证设备目录](https://catalog.azureiotsolutions.com/alldevices)。 对于原型制作，可以使用 [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) 或 [Raspberry Pi](https://www.raspberrypi.org/) 等设备。 Devkit 包含内置的温度、压力、湿度传感器，以及陀螺仪、加速度传感器和磁力计。 Raspberry Pi 允许连接多种不同类型的传感器。 

Microsoft 提供开源[设备 SDK](../iot-hub/iot-hub-devguide-sdks.md) 用于构建在设备上运行的应用。 这些 [SDK 可以简化并加速](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) IoT 解决方案的开发。

## <a name="communication"></a>通信

通常，IoT 设备将来自传感器的遥测数据发送到云中的后端服务。 但是，也可以实现其他类型的通信，例如，在后端服务中将命令发送到设备。 下面是设备到云和云到设备的通信的一些示例：

* 流动冷藏货车每隔 5 分钟向 IoT 中心发送温度数据。 

* 后端服务向设备发送一条命令，以更改设备发送遥测数据的频率来帮助诊断问题。 

* 设备根据其传感器读数值发送警报。 例如，监视化学工厂中间歇式反应器的设备在温度超出特定值时发送警报。

* 设备发送信息并在仪表板上显示，供操作人员查看。 例如，精炼厂的控制室可以显示每个管道中的温度、压力和流量，使操作员能够监视设施。 

[IoT 设备 SDK](../iot-hub/iot-hub-devguide-sdks.md) 和 IoT 中心支持常用的[通信协议](../iot-hub/iot-hub-devguide-protocols.md)，例如 HTTP、MQTT 和 AMQP。

相比于其他客户端（例如浏览器和移动应用），IoT 设备有不同的特征。 设备 SDK 可帮助解决以安全可靠的方式将设备连接到后端服务的难题。  具体而言，IoT 设备具有以下特征：

* 通常是无人操作的嵌入式系统（与电话不同）。
* 可以部署到物理访问昂贵的远程位置。
* 可能只能通过解决方案后端来访问。
* 能力和处理资源可能都有限。
* 网络连接可能不稳定、缓慢或昂贵。
* 可能需要使用专属、自定义或行业特定的应用程序协议。

## <a name="back-end-services"></a>后端服务 

在 IoT 解决方案中，后端服务提供如下功能：

* 接收大规模的来自设备的遥测数据，并确定如何处理和存储该数据。
* 通过分析遥测数据为用户提供见解，不管是实时的还是事后的。
* 从云向特定设备发送命令。 
* 预配设备并控制哪些设备可以连接到基础结构。
* 控制设备状态并监视设备活动。
* 管理设备上安装的固件。

例如，在输油站的远程监视解决方案中，云后端使用来自油泵的遥测数据来识别异常行为。 当后端服务识别到异常时，可以自动将一条命令发回给设备，以采取纠正措施。 此过程在设备和云之间产生一个自动反馈循环，大大提高了解决方案效率。

## <a name="azure-iot-examples"></a>Azure IoT 示例

有关演示组织如何使用 Azure IoT 的现实示例，请参阅 [Microsoft IoT 技术案例研究](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)。 

## <a name="next-steps"></a>后续步骤

如果需要一些使用过的实际商业用例和体系结构，请参阅 [Microsoft Azure IoT 技术案例研究](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)。

如果需要一些可以通过 IoT DevKit 来试用的示例项目，请参阅 [IoT DevKit 项目目录](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)。 

有关不同服务的更详细说明及其用法，请参阅 [Azure IoT 服务和技术](iot-services-and-technologies.md)。

有关 IoT 体系结构的深入介绍，请参阅 [Microsoft Azure IoT 参考体系结构](https://aka.ms/iotrefarchitecture)。
