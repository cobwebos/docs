---
title: "Azure IoT 中心从设备到云选项 | Microsoft Docs"
description: "开发人员指南 - 指导用户何时使用从设备到云的消息、报告属性或文件上传，以进行从云到设备的通信。"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 979136db-c92d-4288-870c-f305e8777bdd
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: e223d0613cd48994315451da87e6b7066585bdb6
ms.openlocfilehash: 8c3479e29b55eacc30842ffdfee23b4a00a13126


---
# <a name="device-to-cloud-communications-guidance"></a>从设备到云通信指南
将信息从设备应用发送到解决方案后端时，IoT 中心会公开三个选项：

* [设备到云 (D2C) 消息][lnk-d2c]，用于时序遥测和警报；
* [报告属性][lnk-twins]，用于报告设备状态信息，例如可用功能、条件和长时间运行的工作流（如配置和软件更新）的状态；
* [文件上传][lnk-fileupload]，用于通过连接的设备间歇性上传的媒体文件和大型遥测批文件（或者是经过压缩的文件以节省带宽）。

下面是各种设备到云通信选项的详细比较。

|  | D2C 消息 | 报告的属性 | 文件上载 |
| ---- | ------- | ---------- | ---- |
| 方案 | 遥测时序和警报，例如 256KB 传感器数据批每 5 分钟发送一次。 | 可用功能和条件，例如设备当前连接模式（手机网络或 WiFi）。 同步长时间运行的工作流，如配置和软件更新。 | 媒体文件。 大型（通常已压缩）遥测批。 |
| 存储和检索 | IoT 中心的临时存储，最多可保存 7 天。 仅顺序读取。 | 由 IoT 中心存储在设备克隆中。 可使用 [IoT 中心查询语言][lnk-query]进行检索。 | 存储在用户提供的 Azure 存储帐户中。 |
| 大小 | 最多 256KB 的消息。 | 报告属性大小最大为 8KB。 | Azure Blob 存储支持的最大文件大小。 |
| 频率 | 高。 有关详细信息，请参阅 [IoT 中心限制][lnk-quotas]。 | 中。 有关详细信息，请参阅 [IoT 中心限制][lnk-quotas]。 | 低。 有关详细信息，请参阅 [IoT 中心限制][lnk-quotas]。 |
| 协议 | 适用于所有协议。 | 目前仅在使用 MQTT 时提供。 | 在使用任何协议时可用，但设备上必须具备 HTTP。 |

> [!NOTE]
> 应用程序可能需要将信息作为遥测时序或警报发送，并且使其在设备克隆中可用。 不管什么情况，既可以通过设备应用发送 D2C 消息并报告属性更改，也可以通过解决方案后端在收到消息时将信息存储在设备孪生的标记中。 由于 D2C 消息允许的吞吐量远高于设备克隆更新，因此有时需要避免为每个 D2C 消息更新设备克隆。
> 
> 

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-fileupload]: iot-hub-devguide-file-upload.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages



<!--HONumber=Dec16_HO1-->


