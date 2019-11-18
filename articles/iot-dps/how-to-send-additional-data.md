---
title: 如何在设备和 Azure 设备预配服务之间传输其他数据
description: 本文档介绍如何在设备和 DPS 之间传输其他数据
author: menchi
ms.author: menchi
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 90402dd6fbe19811b5bb6d5ac0fbdd984b71fd33
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123179"
---
# <a name="how-to-transfer-additional-data-between-device-and-dps"></a>如何在设备和 DPS 之间传输其他数据
有时，DPS 需要获得设备发送的更多数据才能将设备妥善预配到正确的 IoT 中心，并且这些数据需要由该设备提供。 反之亦然，DPS 可以将数据返回到设备以便于客户端逻辑。 

## <a name="when-to-use-it"></a>何时使用它
此功能可用作[自定义分配](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)的增强功能。 例如，你想要根据设备型号分配设备，而无需人工干预。 在这种情况下，将使用[自定义分配](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)。 你可以将设备配置为在[注册设备呼叫](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice)过程中报告模型信息。 DPS 会将设备的信息传递到自定义分配 webhook。 当你的函数接收设备型号信息时，它可以决定此设备将到达的 IoT 中心。 同样，如果 Webhook 要将数据返回设备，它将在 Webhook 响应中以字符串形式将数据传递回设备。  

## <a name="device-sends-data-to-dps"></a>设备将数据发送到 DPS
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
       “data”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS 将数据返回到设备
如果自定义分配策略 webhook 希望将一些数据返回到设备，它会将数据作为 webhook 响应中的字符串传递回。 此更改位于下面的 returnData 部分中。 
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
        "returnData": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>SDK 支持
此功能在 C、 C#、JAVA 和 node.js[客户端 sdk](https://docs.microsoft.com/azure/iot-dps/)中可用。  

## <a name="next-steps"></a>后续步骤
* 使用适用于 Azure IoT 中心和 Azure IoT 中心设备预配服务的 [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks)进行开发
