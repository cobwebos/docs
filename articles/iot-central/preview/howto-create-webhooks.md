---
title: 在 Azure IoT Central 中基于规则创建 Webhook | Microsoft Docs
description: 在 Azure IoT Central 中创建 Webhook，以便在规则触发时自动通知其他应用程序。
author: viv-liu
ms.author: viviali
ms.date: 10/21/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: d1f99be5f21cbf8f1dcfe4a32595a639d3760494
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929938"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central-preview-features"></a>针对 Azure IoT Central 中的规则创建 webhook 操作（预览功能）

本主题适用于构建者和管理员。

使用 Webhook 可以将 IoT Central 应用连接到其他应用程序和服务，以便进行远程监视和通知。 只要在 IoT Central 应用中触发了规则，Webhook 就会自动通知你连接的其他应用程序和服务。 每当触发规则时，IoT Central 应用就会向其他应用程序的 HTTP 终结点发送 POST 请求。 负载包含设备详细信息和规则触发器的详细信息。

## <a name="set-up-the-webhook"></a>设置 webhook

在此示例中，将连接到 RequestBin，以便在使用 webhook 触发规则时收到通知。

1. 打开 [RequestBin](https://requestbin.net/)。

1. 创建一个新的 RequestBin 并复制 **Bin URL**。

1. 创建[遥测规则](tutorial-create-telemetry-rules.md)。 保存规则并添加新操作。

    ![Webhook 创建屏幕](media/howto-create-webhooks/webhookcreate.png)

1. 选择 Webhook 操作，提供显示名称并将 Bin URL 粘贴为回调 URL。

1. 保存规则。

现在，触发规则时，会在 RequestBin 中看到一个新的请求。

## <a name="payload"></a>有效负载

触发规则时，会发出 HTTP POST 请求，其中包含具有遥测、设备、规则和应用程序详细信息的 json 有效负载。 有效负载可能如下所示：

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
        "displayName": "x - Store Analytics Checkout---PnP",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>已知限制

目前，没有通过 API 订阅/取消订阅这些 Webhook 的编程方式。

如果你对如何改进此功能有任何想法，请将你的建议发布到我们的 [Uservoice 论坛](https://feedback.azure.com/forums/911455-azure-iot-central)。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何设置和使用 webhook，接下来建议的下一步是探索[配置 Azure Monitor 操作组](howto-use-action-groups.md)。
