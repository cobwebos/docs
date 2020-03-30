---
title: 内置边缘代理直接方法 - Azure IoT 边缘
description: 使用 IoT Edge 代理运行时模块中的内置直接方法监视和管理 IoT Edge 部署
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57b9d46918414cef9e8cbcffb941b98c98f985ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240346"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>使用内置直接方法与边缘代理通信

使用 IoT Edge 代理模块中包含的直接方法监视和管理 IoT Edge 部署。 直接方法在设备上实现，然后可以从云调用。 IoT Edge 代理包括直接方法，可帮助您远程监视和管理 IoT Edge 设备。

有关直接方法、如何使用这些方法以及如何在您自己的模块中实现它们的详细信息，请参阅[从 IoT 中心了解和调用直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。

这些直接方法的名称不区分大小写。

## <a name="ping"></a>Ping

**ping**方法可用于检查 IoT Edge 是否在设备上运行，或者设备是否与 IoT 中心具有打开的连接。 使用此直接方法 ping IoT Edge 代理并获取其状态。 成功的 ping 返回空有效负载和 **"状态"：200**。

例如：

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

在 Azure 门户中，使用方法名称`ping`和空 JSON 负载`{}`调用 方法。

![在 Azure 门户中调用直接方法"ping"](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>重新启动模块

**RestartModule**方法允许远程管理在 IoT 边缘设备上运行的模块。 如果模块报告失败状态或其他不正常行为，则可以触发 IoT Edge 代理重新启动它。 成功的重新启动命令返回空有效负载和 **"状态"：200**。

重新启动模块方法在 IoT 边缘版本 1.0.9 及更高版本中可用。 

您可以在 IoT 边缘设备上运行的任何模块（包括边缘代理模块本身）上使用 RestartModule 直接方法。 但是，如果使用此直接方法关闭 EdgeAgent，则不会收到成功结果，因为在模块重新启动时连接中断。

例如：

```azurecli
az iot hub invoke-module-method --method-name 'RestartModule' -n <hub name> -d <device name> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "id": "<module name>"
    }
'
```

在 Azure 门户中，使用方法名称`RestartModule`和以下 JSON 负载调用方法：

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![在 Azure 门户中调用直接方法"重新启动模块"](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="experimental-methods"></a>实验方法

新的直接方法选项可作为实验功能进行测试，包括：

* [上载日志](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md)：检索模块日志并将其上载到 Azure Blob 存储。
* [获取任务状态](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#gettaskstatus)：检查上载日志请求的状态。
* [GetLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#getlogs)：在直接方法的响应中内联检索模块日志。

## <a name="next-steps"></a>后续步骤

[IoT Edge 代理和 IoT Edge 中心模块孪生的属性](module-edgeagent-edgehub.md)
