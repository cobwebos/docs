---
title: 内置 edgeAgent 直接方法 - Azure IoT Edge
description: 使用 IoT Edge 代理运行时模块中的内置直接方法监视和管理 IoT Edge 部署
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 42c79526288fb7e05959ac60cddc6f468656ffd4
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972537"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>使用内置直接方法来与 edgeAgent 通信

使用 IoT Edge 代理模块中包含的直接方法监视和管理 IoT Edge 部署。 直接方法在设备上实现，然后可从云中调用。 IoT Edge 代理包含一些可以帮助你远程监视和管理 IoT Edge 设备的直接方法。

有关直接方法及其用法，以及如何在自己的模块中实现这些方法的详细信息，请参阅[了解直接方法以及从 IoT 中心调用直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。

处理这些直接方法时，其名称不区分大小写。

## <a name="ping"></a>Ping

 ping 方法可用于检查 IoT Edge 是否在设备上运行，或者设备是否与 IoT 中心建立了连接。 使用此直接方法可以 ping IoT Edge 代理并获取其状态。 成功的 ping 将返回空有效负载和“状态”：  200。

例如：

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

在 Azure 门户中，调用名为 `ping`、包含空 JSON 有效负载 `{}` 的方法。

![在 Azure 门户中调用直接方法“ping”](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>重启模块

使用 RestartModule 方法可以远程管理 IoT Edge 设备上运行的模块。  如果某个模块报告故障状态或其他不正常行为，你可以触发 IoT Edge 代理来重启该模块。 成功的 restart 命令将返回空有效负载和“状态”：  200。

RestartModule 方法可在 IoT Edge 1.0.9 和更高版本中使用。 

可对 IoT Edge 设备上运行的任何模块（包括 edgeAgent 模块本身）使用 RestartModule 直接方法。 但是，如果使用此直接方法来关闭 edgeAgent，将不会收到成功结果，因为在模块重启时会中断连接。

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

在 Azure 门户中，调用名为 `RestartModule`、包含以下 JSON 有效负载的方法：

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![在 Azure 门户中调用直接方法“RestartModule”](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="diagnostic-direct-methods"></a>诊断直接方法

* [GetModuleLogs](how-to-retrieve-iot-edge-logs.md#retrieve-module-logs)：在直接方法的响应中检索内联模块日志。
* [UploadModuleLogs](how-to-retrieve-iot-edge-logs.md#upload-module-logs)：检索模块日志，并将其上传到 Azure Blob 存储。
* [UploadSupportBundle](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)：使用支持捆绑检索模块日志，并将 zip 文件上传到 Azure Blob 存储。
* [GetTaskStatus](how-to-retrieve-iot-edge-logs.md#get-upload-request-status)：检查上传日志或支持捆绑请求的状态。

1.0.10 版本提供这些诊断直接方法。

## <a name="next-steps"></a>后续步骤

[IoT Edge 代理和 IoT Edge 中心模块孪生的属性](module-edgeagent-edgehub.md)
