---
title: Azure IoT 设备预配服务 (DPS) TLS 支持
description: 对与 IoT 设备预配服务 (DPS) 通信的设备和服务使用安全 TLS 连接的最佳做法
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: wesmc
ms.openlocfilehash: 285832d80d37c8553ffc8e37c6f6eab5d7f6d943
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984845"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>Azure IoT 中心设备预配服务 (DPS) 中的 TLS 支持

DPS 使用传输层安全性 (TLS) 保护来自 IoT 设备的连接。 目前支持三个版本的 TLS 协议，即版本 1.0、1.1 和 1.2。

TLS 1.0 和 1.1 被视为旧版，我们已计划弃用这两个版本。 有关详细信息，请参阅 [IoT 中心弃用 TLS 1.0 和 1.1](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md)。 在连接到 DPS 时，强烈建议使用 TLS 1.2 作为首选 TLS 版本。

## <a name="restrict-connections-to-tls-12"></a>将连接限制为 TLS 1.2

为了提高安全性，建议将 DPS 实例配置为仅允许那些使用 TLS 版本 1.2 的设备客户端连接，并强制使用[建议的密码](#recommended-ciphers)。

为此，请在任何[支持的区域](#supported-regions)中预配新的 DPS 资源并将 `minTlsVersion` 属性设置为 Azure 资源管理器模板的 DPS 资源规范中的 `1.2`。 以下示例模板 JSON 为新的 DPS 实例指定 `minTlsVersion` 属性。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/ProvisioningServices",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-DPS-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "S1",
                "capacity": 1
            },
        }     
    ]
}
```

可以使用以下 Azure CLI 命令来部署模板。 

```azurecli
az deployment group create -g <your resource group name> --template-file template.json
```

若要详细了解如何使用资源管理器模板来创建 DPS 资源，请参阅[使用 Azure 资源管理器模板设置 DPS](quick-setup-auto-provision-rm.md)。

使用此配置创建的 DPS 资源会拒绝那些尝试使用 TLS 版本 1.0 和 1.1 进行连接的设备。 同样，如果设备客户端的 HELLO 消息未列出任何[建议的密码](#recommended-ciphers)，TLS 握手会被拒绝。

> [!NOTE]
> `minTlsVersion` 属性是只读的，创建 DPS 资源后，便不能再更改该属性。 因此，必须事先正确测试并验证你的所有 IoT 设备是否与 TLS 1.2 和[建议的密码](#recommended-ciphers)兼容。

## <a name="supported-regions"></a>支持的区域

需要使用 TLS 1.2 的 IoT DPS 实例可在以下区域中创建：

* US Gov 亚利桑那州
* US Gov 弗吉尼亚州

> [!NOTE]
> 故障转移后，DPS 的 `minTlsVersion` 属性会在故障转移后的异地配对区域中保持有效。

## <a name="recommended-ciphers"></a>建议的密码

配置为仅接受 TLS 1.2 的 DPS 实例还会强制使用以下建议的密码：

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

## <a name="use-tls-12-in-the-iot-sdks"></a>在 IoT SDK 中使用 TLS 1.2

使用以下链接在 Azure IoT 客户端 SDK 中配置 TLS 1.2 和允许的密码。

| 语言 | 支持 TLS 1.2 的版本 | 文档 |
|----------|------------------------------------|---------------|
| C        | 标记 2019-12-11 或更新的标记            | [链接](https://aka.ms/Tls_C_SDK_IoT)。 |
| Python   | 版本 2.0.0 或更高版本             | [链接](https://aka.ms/Tls_Python_SDK_IoT)。 |
| C#       | 版本 1.21.4 或更高版本            | [链接](https://aka.ms/Tls_CSharp_SDK_IoT)。 |
| Java     | 版本 1.19.0 或更高版本            | [链接](https://aka.ms/Tls_Java_SDK_IoT)。 |
| NodeJS   | 版本 1.12.2 或更高版本            | [链接](https://aka.ms/Tls_Node_SDK_IoT)。 |


## <a name="use-tls-12-with-iot-edge"></a>将 TLS 1.2 与 IoT Edge 配合使用

IoT Edge 设备可以配置为在与 IoT 中心和 DPS 通信时使用 TLS 1.2。 为此，请参阅 [IoT Edge 文档页](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)。