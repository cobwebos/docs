---
title: Azure IoT 设备预配服务（DPS） TLS 支持
description: 为设备和服务使用安全 TLS 连接与 IoT 设备预配服务（DPS）通信的最佳实践
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: wesmc
ms.openlocfilehash: 285832d80d37c8553ffc8e37c6f6eab5d7f6d943
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984845"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>Azure IoT 中心设备预配服务（DPS）中的 TLS 支持

DPS 使用传输层安全性（TLS）来保护来自 IoT 设备的连接。 目前支持三个版本的 TLS 协议，即版本1.0、1.1 和1.2。

TLS 1.0 和1.1 被视为旧版，并计划弃用。 有关详细信息，请参阅[弃用 TLS 1.0 和 1.1 For IoT 中心](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md)。 在连接到 DPS 时，强烈建议使用 TLS 1.2 作为首选 TLS 版本。

## <a name="restrict-connections-to-tls-12"></a>限制与 TLS 1.2 的连接

为了增加安全性，建议将 DPS 实例配置为*仅*允许使用 TLS 版本1.2 的设备客户端连接，并强制使用[推荐的密码](#recommended-ciphers)。

为此，请在任何[受支持的区域](#supported-regions)中预配新的 DPS 资源， `minTlsVersion`并将`1.2`属性设置为 Azure 资源管理器模板的 DPS 资源规范。 下面的示例模板 JSON 为新`minTlsVersion`的 DPS 实例指定属性。

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

可以通过以下 Azure CLI 命令来部署模板。 

```azurecli
az deployment group create -g <your resource group name> --template-file template.json
```

有关使用资源管理器模板创建 DPS 资源的详细信息，请参阅[使用 Azure 资源管理器模板设置 dps](quick-setup-auto-provision-rm.md)。

使用此配置创建的 DPS 资源将拒绝尝试使用 TLS 版本1.0 和1.1 进行连接的设备。 同样，如果设备客户端的 HELLO 消息未列出任何[建议的密码](#recommended-ciphers)，TLS 握手将被拒绝。

> [!NOTE]
> 此`minTlsVersion`属性是只读的，一旦创建了 DPS 资源，就不能再对其进行更改。 因此，请务必正确地测试和验证*所有*IoT 设备是否与 TLS 1.2 和[推荐的密码](#recommended-ciphers)兼容。

## <a name="supported-regions"></a>支持的区域

需要使用 TLS 1.2 的 IoT DPS 实例可在以下区域创建：

* US Gov 亚利桑那州
* US Gov 弗吉尼亚州

> [!NOTE]
> 故障转移后， `minTlsVersion`在故障转移后，你的 DPS 的属性将保持有效。

## <a name="recommended-ciphers"></a>建议的密码

配置为仅接受 TLS 1.2 的 DPS 实例还将强制使用以下建议的密码：

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

## <a name="use-tls-12-in-the-iot-sdks"></a>在 IoT Sdk 中使用 TLS 1。2

使用以下链接在 Azure IoT 客户端 Sdk 中配置 TLS 1.2 和允许的密码。

| 语言 | 支持 TLS 1.2 的版本 | 文档 |
|----------|------------------------------------|---------------|
| C        | 标记2019-12-11 或更高版本            | [链接](https://aka.ms/Tls_C_SDK_IoT)。 |
| Python   | 版本2.0.0 或更高版本             | [链接](https://aka.ms/Tls_Python_SDK_IoT)。 |
| C#       | 版本1.21.4 或更高版本            | [链接](https://aka.ms/Tls_CSharp_SDK_IoT)。 |
| Java     | 版本1.19.0 或更高版本            | [链接](https://aka.ms/Tls_Java_SDK_IoT)。 |
| NodeJS   | 版本1.12.2 或更高版本            | [链接](https://aka.ms/Tls_Node_SDK_IoT)。 |


## <a name="use-tls-12-with-iot-edge"></a>将 TLS 1.2 与 IoT Edge 配合使用

在与 IoT 中心和 DPS 通信时，可以将 IoT Edge 设备配置为使用 TLS 1.2。 为此，请使用[IoT Edge 文档页](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)。