---
title: Azure IoT 中心 TLS 支持
description: 使用安全 TLS 连接实现与 IoT 中心通信的设备和服务的最佳做法
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: ce8ef987adc0cec4fcd8acef4cc075d50c92d62a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722884"
---
# <a name="tls-support-in-iot-hub"></a>IoT 中心的 TLS 支持

IoT 中心使用传输层安全性（TLS）来保护来自 IoT 设备和服务的连接。 目前支持三个版本的 TLS 协议，即版本1.0、1.1 和1.2。

TLS 1.0 和1.1 被视为旧版，并计划弃用。 有关详细信息，请参阅[弃用 TLS 1.0 和 1.1 For IoT 中心](iot-hub-tls-deprecating-1-0-and-1-1.md)。 在连接到 IoT 中心时，强烈建议使用 TLS 1.2 作为首选 TLS 版本。

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>在 IoT 中心资源中限制与 TLS 1.2 的连接

为了增加安全性，建议将 IoT 中心配置为*仅*允许使用 TLS 版本1.2 的客户端连接，并强制使用[推荐的密码](#recommended-ciphers)。

为此，请在任何[受支持的区域](#supported-regions)中预配新的 IoT 中心，并将 `minTlsVersion` 属性设置为 Azure 资源管理器模板的 IoT 中心资源规格 `1.2`：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

使用此配置创建的 IoT 中心资源将拒绝尝试使用 TLS 版本1.0 和1.1 进行连接的设备和服务客户端。 同样，如果客户端呼叫消息未列出任何[建议的密码](#recommended-ciphers)，TLS 握手将被拒绝。

> [!NOTE]
> `minTlsVersion` 属性是只读的，一旦创建了 IoT 中心资源，就不能再对其进行更改。 因此，请务必正确地测试和验证*所有*IoT 设备和服务是否与 TLS 1.2 和[推荐的密码](#recommended-ciphers)兼容。

### <a name="supported-regions"></a>支持的区域

需要使用 TLS 1.2 的 IoT 中心可在以下区域创建：

* 美国东部
* 美国中南部
* 美国西部 2

> [!NOTE]
> 故障转移后，IoT 中心的 `minTlsVersion` 属性在故障转移后将保持有效，在异地配对区域中。

### <a name="recommended-ciphers"></a>建议的密码

配置为仅接受 TLS 1.2 的 IoT 中心还将强制使用以下建议的密码：

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

### <a name="use-tls-12-in-your-iot-hub-sdks"></a>在 IoT 中心 Sdk 中使用 TLS 1。2

使用以下链接在 IoT 中心客户端 Sdk 中配置 TLS 1.2 和允许的密码。

| 语言 | 支持 TLS 1。2 | 文档 |
|----------|-------------------|---------------|
| C        | 是               | [链接](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | 是               | [链接](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | 是               | [链接](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | 是               | [链接](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | 是               | [链接](https://aka.ms/Tls_Node_SDK_IoT) |


### <a name="use-tls-12-in-your-iot-edge-setup"></a>在 IoT Edge 安装程序中使用 TLS 1。2

在与 IoT 中心通信时，可以将 IoT Edge 设备配置为使用 TLS 1.2。 为此，请使用[IoT Edge 文档页](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)。