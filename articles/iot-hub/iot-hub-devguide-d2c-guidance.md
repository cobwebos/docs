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
ms.date: 05/25/2017
ms.author: elioda
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: a36283053939ccd53856a394cd9efb66285271ae
ms.contentlocale: zh-cn
ms.lasthandoff: 06/01/2017


---
# <a name="device-to-cloud-communications-guidance"></a>从设备到云通信指南
将信息从设备应用发送到解决方案后端时，IoT 中心会公开三个选项：

* [设备到云消息][lnk-d2c]，用于时序遥测和警报。
* [报告属性][lnk-twins]，用于报告设备状态信息，例如可用功能、条件或长时间运行的工作流的状态。 例如，配置和软件更新。
* [文件上传][lnk-fileupload]，用于由间歇性连接的设备上传的或为了节省带宽而压缩的媒体文件和大型遥测批文件。

下面是各种设备到云通信选项的详细比较。

|  | 设备到云的消息 | 报告的属性 | 文件上传 |
| ---- | ------- | ---------- | ---- |
| 方案 | 遥测时序和警报。 例如，每隔 5 分钟发送 256-KB 的传感器数据批。 | 可用功能和条件。 例如，当前设备连接模式，诸如手机网络或 WiFi。 同步长时间运行的工作流，如配置和软件更新。 | 媒体文件。 大型（通常为压缩的）遥测批。 |
| 存储和检索 | IoT 中心的临时存储，最多可保存 7 天。 仅顺序读取。 | 由 IoT 中心存储在设备孪生中。 可使用 [IoT 中心查询语言][lnk-query]进行检索。 | 存储在用户提供的 Azure 存储帐户中。 |
| 大小 | 消息大小最大为 256-KB。 | 报告属性大小最大为 8 KB。 | Azure Blob 存储支持的最大文件大小。 |
| 频率 | 高。 有关详细信息，请参阅 [IoT 中心限制][lnk-quotas]。 | 中。 有关详细信息，请参阅 [IoT 中心限制][lnk-quotas]。 | 低。 有关详细信息，请参阅 [IoT 中心限制][lnk-quotas]。 |
| 协议 | 适用于所有协议。 | 目前仅在使用 MQTT 时提供。 | 在使用任何协议时可用，但设备上必须具备 HTTP。 |

应用程序可能需要将信息作为遥测时序或警报发送，并且使其在设备孪生中可用。 在这种情况下，你可以选择以下选项之一：

* 设备应用发送一条设备到云消息并报告属性更改。
* 解决方案后端在收到消息时可以将信息存储在设备孪生的标记中。

由于设备到云消息允许的吞吐量远高于设备孪生更新，因此有时需要避免为每条设备到云消息更新设备孪生。


[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-fileupload]: iot-hub-devguide-file-upload.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md

