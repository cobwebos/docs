---
title: 如何在设备和 Azure 设备预配服务之间传输有效负载
description: 本文档介绍如何在设备和设备预配服务（DPS）之间传输有效负载
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 3eec39e975b1e782eafe16205623c625f462a865
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209403"
---
# <a name="how-to-transfer-a-payload-between-device-and-dps"></a>如何在设备和 DPS 之间传输有效负载
有时，DPS 需要设备中的更多数据，以便将它们正确预配到正确的 IoT 中心，并且该数据需要由设备提供。 反之亦然，DPS 可以将数据返回到设备以便于客户端逻辑。 

## <a name="when-to-use-it"></a>何时使用
此功能可用作[自定义分配](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)的增强功能。 例如，你想要根据设备型号分配设备，而无需人工干预。 在这种情况下，将使用[自定义分配](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)。 你可以将设备配置为在[注册设备呼叫](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice)过程中报告模型信息。 DPS 会将设备的负载传递到自定义分配 webhook。 当你的函数接收设备型号信息时，它可以决定此设备将到达的 IoT 中心。 同样，如果 webhook 希望将一些数据返回到设备，它会将数据作为 webhook 响应中的字符串传递回。  

## <a name="device-sends-data-payload-to-dps"></a>设备将数据负载发送到 DPS
当你的设备向 DPS 发送[注册设备调用](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice)时，可以增强注册调用以获取正文中的其他字段。 正文如下所示： 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “interfaces”: “TODO: get how interfaces are reported by devices from PnP folks.”, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS 将数据返回到设备
如果自定义分配策略 webhook 希望将一些数据返回到设备，它会将数据作为 webhook 响应中的字符串传递回。 此更改位于下面的 "负载" 部分。 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>SDK 支持
此功能在 C、 C#、JAVA 和 node.js[客户端 sdk](https://docs.microsoft.com/azure/iot-dps/)中可用。  

## <a name="next-steps"></a>后续步骤
* 使用适用于 Azure IoT 中心和 Azure IoT 中心设备预配服务的 [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks)进行开发
