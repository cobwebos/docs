---
title: include 文件
description: include 文件
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: c79b6f854dc78670a7eb8a1275c3e2fc46fcdd99
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173385"
---
### <a name="code-walkthrough"></a>代码演练

本部分介绍了示例代码的一些关键部分，并解释了它们如何与远程监视解决方案加速器相关。

以下代码片段展示了描述设备功能的报告属性是如何定义的。 这些属性包括：

- 设备所在的位置（用于使解决方案加速器可以将该设备添加到地图）。
- 当前的固件版本。
- 设备支持的方法列表。
- 设备发送的遥测消息的架构。

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

示例包括了一个 **serializeToJson** 函数，它使用 Parson 库对此数据结构进行序列化。

示例包括了多个回调函数，它们在客户端与解决方案加速器进行交互时将信息输出到控制台：

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

以下代码片段显示了 **device_method_callback** 函数。 此函数确定当从解决方案加速器收到方法调用时要执行的操作。 此函数在 **userContextCallback** 参数中接收对 **Chiller** 数据结构的引用。 **userContextCallback** 的值是在 **main** 函数中配置回调函数时设置的：

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

当解决方案加速器调用固件更新方法时，示例会对 JSON 有效负载进行反序列化，并启动一个后台线程来完成更新过程。 以下代码片段显示了在该线程上运行的 **do_firmware_update**：

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

以下代码片段展示了客户端如何将遥测消息发送给解决方案加速器。 消息属性包括了消息架构，以帮助解决方案加速器在仪表板上显示遥测数据：

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=sendmessage "Send telemetry")]

示例中的 **main** 函数：

- 初始化和关闭 SDK 子系统。
- 初始化 **Chiller** 数据结构。
- 将报告的属性发送给解决方案加速器。
- 配置设备方法回调函数。
- 将模拟的遥测数据值发送给解决方案加速器。

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=main "Main")]
