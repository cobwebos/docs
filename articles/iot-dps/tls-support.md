---
title: Azure IoT 设备预配服务 (DPS) TLS 支持
description: 对与 IoT 设备预配服务 (DPS) 通信的设备和服务使用安全 TLS 连接的最佳做法
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: wesmc
ms.openlocfilehash: 3a8910cf0e81bd041d74ef95f45220f1c1e0b34c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761288"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>Azure IoT 中心设备预配服务 (DPS) 中的 TLS 支持

DPS 使用 [传输层安全性 (TLS) ](http://wikipedia.org/wiki/Transport_Layer_Security) 来保护来自 IoT 设备的连接。 

DPS 支持的当前 TLS 协议版本如下： 
* TLS 1.2

TLS 1.0 和 1.1 被视为旧版，我们已计划弃用这两个版本。 有关详细信息，请参阅 [IoT 中心弃用 TLS 1.0 和 1.1](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md)。 

## <a name="restrict-connections-to-tls-12"></a>将连接限制为 TLS 1.2

为了提高安全性，建议将 DPS 实例配置为仅允许那些使用 TLS 版本 1.2 的设备客户端连接，并强制使用[建议的密码](#recommended-ciphers)。

为此，请 `minTlsVersion` `1.2` 在 Azure 资源管理器模板的 dps 资源规范中预配一个新的 DPS 资源，将属性设置为。 以下示例模板 JSON 为新的 DPS 实例指定 `minTlsVersion` 属性。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/ProvisioningServices",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-DPS-resource-name>",
            "location": "<any-region>",
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


> [!NOTE]
> 故障转移后，DPS 的 `minTlsVersion` 属性会在故障转移后的异地配对区域中保持有效。

## <a name="recommended-ciphers"></a>建议的密码

配置为仅接受 TLS 1.2 的 DPS 实例还将强制使用以下密码套件：


| 推荐的 TLS 1.2 密码套件 |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256` |


### <a name="legacy-cipher-suites"></a>旧密码套件 

DPS 当前仍支持这些密码套件，但将对其进行折旧。 如果可能，请使用上面建议的密码套件。

| 选项 #1 (更好的安全性)  |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |

| 选项 #2 (更好的性能)  |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |


## <a name="use-tls-12-in-the-iot-sdks"></a>在 IoT SDK 中使用 TLS 1.2

使用以下链接在 Azure IoT 客户端 SDK 中配置 TLS 1.2 和允许的密码。

| 语言 | 支持 TLS 1.2 的版本 | 文档 |
|----------|------------------------------------|---------------|
| C        | 标记 2019-12-11 或更新的标记            | [链接](https://aka.ms/Tls_C_SDK_IoT)。 |
| Python   | 版本 2.0.0 或更高版本             | [链接](https://aka.ms/Tls_Python_SDK_IoT)。 |
| C#       | 版本 1.21.4 或更高版本            | [链接](https://aka.ms/Tls_CSharp_SDK_IoT)。 |
| Java     | 版本 1.19.0 或更高版本            | [链接](https://aka.ms/Tls_Java_SDK_IoT)。 |
| NodeJS   | 版本 1.12.2 或更高版本            | [链接](https://aka.ms/Tls_Node_SDK_IoT)。 |

## <a name="use-tls-12-with-iot-hub"></a>将 TLS 1.2 与 IoT 中心配合使用

与设备通信时，可以将 IoT 中心配置为使用 TLS 1.2。 有关详细信息，请参阅 [IoT 中心弃用 TLS 1.0 和 1.1](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md)。

## <a name="use-tls-12-with-iot-edge"></a>将 TLS 1.2 与 IoT Edge 配合使用

IoT Edge 设备可以配置为在与 IoT 中心和 DPS 通信时使用 TLS 1.2。 有关详细信息，请参阅 [IoT Edge 文档页](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)。
