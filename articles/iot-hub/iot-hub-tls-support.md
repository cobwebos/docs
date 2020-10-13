---
title: Azure IoT 中心 TLS 支持
description: 对与 IoT 中心通信的设备和服务使用安全 TLS 连接的最佳做法
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: jlian
ms.openlocfilehash: 08ecb766a1a9bd7ff75bf97647be811577212eb5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90006034"
---
# <a name="tls-support-in-iot-hub"></a>IoT 中心的 TLS 支持

IoT 中心使用传输层安全性 (TLS) 保护来自 IoT 设备和服务的连接。 目前支持三个版本的 TLS 协议，即版本 1.0、1.1 和 1.2。

TLS 1.0 和 1.1 被视为旧版，我们已计划弃用这两个版本。 有关详细信息，请参阅 [IoT 中心弃用 TLS 1.0 和 1.1](iot-hub-tls-deprecating-1-0-and-1-1.md)。 在连接到 IoT 中心时，强烈建议使用 TLS 1.2 作为首选 TLS 版本。

## <a name="tls-12-enforcement-available-in-select-regions"></a>所选区域中可用的 TLS 1.2 强制实施

为了增加安全性，请将 IoT 中心配置为 *仅* 允许使用 TLS 版本1.2 的客户端连接，并强制使用 [密码套件](#cipher-suites)。 仅在以下区域支持此功能：

* 美国东部
* 美国中南部
* 美国西部 2
* US Gov 亚利桑那州
* US Gov 弗吉尼亚州

为此，请在任何支持的区域中预配新的 IoT 中心并将 Azure 资源管理器模板的 IoT 中心资源规范中的 `minTlsVersion` 属性设置为 `1.2`：

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

使用此配置创建的 IoT 中心资源会拒绝那些尝试使用 TLS 版本 1.0 和 1.1 进行连接的设备和服务客户端。 同样，如果 `ClientHello` 消息未列出任何 [建议的密码](#cipher-suites)，TLS 握手将被拒绝。

> [!NOTE]
> `minTlsVersion` 属性处于只读状态，创建 IoT 中心资源后，便不能再更改该属性。 因此，必须事先正确测试并验证你的所有 IoT 设备和服务是否与 TLS 1.2 和[建议的密码](#cipher-suites)兼容。
> 
> 故障转移后，IoT 中心的 `minTlsVersion` 属性在异地配对区域中仍然有效。

## <a name="cipher-suites"></a>密码套件

配置为仅接受 TLS 1.2 的 IoT 中心还将强制使用以下建议的密码套件：

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

对于未配置 TLS 1.2 强制的 IoT 中心，TLS 1.2 仍适用于以下密码套件：

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

客户端可以建议在过程中使用的更高密码套件的列表 `ClientHello` 。 但是，IoT 中心可能不支持其中某些 (例如 `ECDHE-ECDSA-AES256-GCM-SHA384`) 。 在这种情况下，IoT 中心将尝试按照客户端的喜好进行操作，但最终会将密码套件与进行协商 `ServerHello` 。

## <a name="use-tls-12-in-your-iot-hub-sdks"></a>在 IoT 中心 SDK 使用 TLS 1.2

使用以下链接在 IoT 中心客户端 SDK 中配置 TLS 1.2 和允许的密码。

| 语言 | 支持 TLS 1.2 的版本 | 文档 |
|----------|------------------------------------|---------------|
| C        | 标记 2019-12-11 或更新的标记            | [链接](https://aka.ms/Tls_C_SDK_IoT)。 |
| Python   | 版本 2.0.0 或更高版本             | [链接](https://aka.ms/Tls_Python_SDK_IoT)。 |
| C#       | 版本 1.21.4 或更高版本            | [链接](https://aka.ms/Tls_CSharp_SDK_IoT)。 |
| Java     | 版本 1.19.0 或更高版本            | [链接](https://aka.ms/Tls_Java_SDK_IoT)。 |
| NodeJS   | 版本 1.12.2 或更高版本            | [链接](https://aka.ms/Tls_Node_SDK_IoT)。 |


## <a name="use-tls-12-in-your-iot-edge-setup"></a>在 IoT Edge IoT 设置中使用 TLS 1.2

IoT Edge 设备可以配置为在与 IoT 中心通信时使用 TLS 1.2。 为此，请参阅 [IoT Edge 文档页](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)。

## <a name="device-authentication"></a>设备身份验证

成功进行 TLS 握手后，IoT 中心可以使用对称密钥或 x.509 证书对设备进行身份验证。 对于基于证书的身份验证，这可以是任何 x.509 证书，包括 ECC。 IoT 中心根据你提供)  (CA 的指纹或证书颁发机构验证证书。 IoT 中心尚不支持基于 x.509 的相互身份验证 (mTLS) 。 若要了解详细信息，请参阅 [支持的 x.509 证书](iot-hub-devguide-security.md#supported-x509-certificates)。
