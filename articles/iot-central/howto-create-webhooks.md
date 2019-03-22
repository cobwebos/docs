---
title: 在 Azure IoT Central 中基于规则创建 Webhook | Microsoft Docs
description: 在 Azure IoT Central 中创建 Webhook，以便在规则触发时自动通知其他应用程序。
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 22167de6676837c45c48a0bafd19b1ba69578827
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58003666"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>在 Azure IoT Central 中基于规则创建 Webhook 操作

本主题适用于构建者和管理员。

使用 Webhook 可以将 IoT Central 应用连接到其他应用程序和服务，以便进行远程监视和通知。 只要在 IoT Central 应用中触发了规则，Webhook 就会自动通知你连接的其他应用程序和服务。 每当触发规则时，IoT Central 应用到其他应用程序的 HTTP 终结点发送 POST 请求。 负载包含设备详细信息和规则触发器详细信息。

## <a name="set-up-the-webhook"></a>设置 webhook

在此示例中，您连接到 RequestBin，规则触发使用 webhook 时获得通知。

1. 打开 [RequestBin](https://requestbin.net/)。

1. 创建一个新的 RequestBin 并复制 **Bin URL**。

1. 创建[遥测规则](howto-create-telemetry-rules.md)或[事件规则](howto-create-event-rules.md)。 保存规则并添加新操作。

    ![Webhook 创建屏幕](media/howto-create-webhooks/webhookcreate.png)

1. 选择 Webhook 操作，提供显示名称并将 Bin URL 粘贴为回调 URL。

1. 保存规则。

现在触发规则时，你将看到在 RequestBin 中显示的新请求。

## <a name="payload"></a>有效负载

触发规则时，会向包含 json 有效负载的回调 URL 发出 HTTP POST 请求，其中包含度量、设备、规则和应用程序详细信息。 对于遥测规则，有效负载如下所示：

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
        "deviceId": "deviceID",
        "deviceTemplate":{
            "id": "ID",
            "version":"1.0.0"
        },
        "properties":{
            "device":{
                "firmwareversion":"1.0"
            },
            "cloud":{
                "location":"One Microsoft Way"
            }
        },
        "measurements":{
            "telemetry":{
                "temperature":20,
                "pressure":10
            }
        }

    },
    "rule": {
        "id": "ID",
        "name": "High temperature alert",
        "enabled": true,
        "deviceTemplate": {
            "id":"GUID",
            "version":"1.0.0"
        }
    },
    "application": {
        "id": "ID",
        "name": "Contoso app",
        "subdomain":"contoso-app"
    }
}
```

## <a name="known-limitations"></a>已知限制

目前，没有通过 API 订阅/取消订阅这些 Webhook 的编程方式。

如果你对如何改进此功能有任何想法，请将你的建议发布到我们的 [Uservoice 论坛](https://feedback.azure.com/forums/911455-azure-iot-central)。

## <a name="next-steps"></a>后续步骤

现在，已了解如何设置和使用 webhook，建议下一步是探索[构建在 Microsoft Flow 中的工作流](howto-add-microsoft-flow.md)。
