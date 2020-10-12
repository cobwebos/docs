---
title: 在 Azure IoT Central 中基于规则创建 Webhook | Microsoft Docs
description: 在 Azure IoT Central 中创建 Webhook，以便在规则触发时自动通知其他应用程序。
author: viv-liu
ms.author: viviali
ms.date: 04/03/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: b2ac4bbf1457144d23a91c4e83b554b3ee806119
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87337222"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>在 Azure IoT Central 中基于规则创建 Webhook 操作

本主题适用于构建者和管理员  。

使用 Webhook 可以将 IoT Central 应用连接到其他应用程序和服务，以便进行远程监视和通知。 只要在 IoT Central 应用中触发了规则，Webhook 就会自动通知你连接的其他应用程序和服务。 每当触发规则时，IoT Central 应用就会向其他应用程序的 HTTP 终结点发送 POST 请求。 有效负载包含设备详细信息和规则触发详细信息。

## <a name="set-up-the-webhook"></a>设置 Webhook

在此示例中，将连接到 RequestBin，以便在使用 Webhook 触发规则时收到通知。

1. 打开 [RequestBin](https://requestbin.net/)。

1. 创建一个新的 RequestBin 并复制 **Bin URL**。

1. 创建[遥测规则](tutorial-create-telemetry-rules.md)。 保存规则并添加新操作。

    ![Webhook 创建屏幕](media/howto-create-webhooks/webhookcreate.png)

1. 选择 Webhook 操作，提供显示名称并将 Bin URL 粘贴为回调 URL。

1. 保存规则。

现在，当触发规则时，你会看到一个新请求出现在 RequestBin 中。

## <a name="payload"></a>有效负载

触发规则时，会向包含 json 有效负载的回调 URL 发出 HTTP POST 请求，其中包含遥测数据、设备、规则和应用程序详细信息。 有效负载可能如下所示：

```json
{
    "timestamp": "2020-04-06T00:20:15.06Z",
    "action": {
        "id": "<id>",
        "type": "WebhookAction",
        "rules": [
            "<rule_id>"
        ],
        "displayName": "Webhook 1",
        "url": "<callback_url>"
    },
    "application": {
        "id": "<application_id>",
        "displayName": "Contoso",
        "subdomain": "contoso",
        "host": "contoso.azureiotcentral.com"
    },
    "device": {
        "id": "<device_id>",
        "etag": "<etag>",
        "displayName": "MXChip IoT DevKit - 1yl6vvhax6c",
        "instanceOf": "<device_template_id>",
        "simulated": true,
        "provisioned": true,
        "approved": true,
        "cloudProperties": {
            "City": {
                "value": "Seattle"
            }
        },
        "properties": {
            "deviceinfo": {
                "firmwareVersion": {
                    "value": "1.0.0"
                }
            }
        },
        "telemetry": {
            "<interface_instance_name>": {
                "humidity": {
                    "value": 47.33228889360127
                }
            }
        }
    },
    "rule": {
        "id": "<rule_id>",
        "displayName": "Humidity monitor"
    }
}
```
如果规则监视一段时间内的聚合遥测数据，则负载会包含不同的遥测部分。

```json
{
    "telemetry": {
        "<interface_instance_name>": {
            "Humidity": {
                "avg": 39.5
            }
        }
    }
}
```

## <a name="data-format-change-notice"></a>数据格式更改通知

如果在 **2020 年4月 3**日之前创建并保存了一个或多个 webhook，则需要删除 webhook 并创建新的 webhook。 这是因为，较旧的 webhook 使用的是将来将弃用的旧有效负载格式。

### <a name="webhook-payload-format-deprecated-as-of-3-april-2020"></a>从2020年4月3日起，Webhook 负载 (格式弃用) 

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>已知的限制

目前，没有通过 API 订阅/取消订阅这些 Webhook 的编程方式。

如果你有关于如何改进此功能的建议，请将你的建议发布到 [用户语音论坛](https://feedback.azure.com/forums/911455-azure-iot-central)。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何设置和使用 Webhook，建议下一步是探索如何[配置 Azure Monitor 操作组](howto-use-action-groups.md)。
