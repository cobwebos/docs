---
title: 检索 IoT Edge 日志-Azure IoT Edge
description: IoT Edge 模块日志检索和上传到 Azure Blob 存储。
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 09/14/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: f5f2a9800d3796d217294e757076d6ff706281d1
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044192"
---
# <a name="retrieve-logs-from-iot-edge-deployments"></a>检索 IoT Edge 部署中的日志

检索 IoT Edge 部署中的日志，无需使用 IoT Edge 代理模块中包含的直接方法对设备进行物理或 SSH 访问。 直接方法在设备上实现，然后可从云中调用。 IoT Edge 代理包括直接方法，可帮助你远程监视和管理你的 IoT Edge 设备。 本文中所述的直接方法在1.0.10 版本中公开提供。

有关直接方法、如何使用它们以及如何在自己的模块中实现这些方法的详细信息，请参阅 [了解和从 IoT 中心调用直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。

这些直接方法的名称是区分大小写的。

## <a name="recommended-logging-format"></a>建议的日志记录格式

虽然不是必需的，但为了与此功能实现最佳兼容性，建议的日志记录格式为：

```
<{Log Level}> {Timestamp} {Message Text}
```

`{Log Level}` 应遵循 [Syslog 严重性级别格式](https://wikipedia.org/wiki/Syslog#Severity_lnevel) ，且 `{Timestamp}` 格式应为 `yyyy-mm-dd hh:mm:ss.fff zzz` 。

[IoT Edge 中的记录器类](https://github.com/Azure/iotedge/blob/master/edge-util/src/Microsoft.Azure.Devices.Edge.Util/Logger.cs)充当规范实现。

## <a name="retrieve-module-logs"></a>检索模块日志

使用 **GetModuleLogs** 直接方法检索 IoT Edge 模块的日志。

此方法接受具有以下架构的 JSON 有效负载：

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": int,
                "since": int,
                "until": int,
                "loglevel": int,
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| 名称 | 类型 | 说明 |
|-|-|-|
| schemaVersion | 字符串 | 设置为 `1.0` |
| items | JSON 数组 | 具有 `id` 和 `filter` 元组的数组。 |
| ID | 字符串 | 提供模块名称的正则表达式。 它可以匹配边缘设备上的多个模块。 需要[.Net 正则表达式](/dotnet/standard/base-types/regular-expressions)格式。 |
| filter | JSON 部分 | 要应用于与 `id` 元组中的正则表达式匹配的模块的日志筛选器。 |
| 侧 | 整型 | 过去要检索的日志行数（从最新开始）。 可选。 |
| since | 整型 | 从此时起，只返回日志，持续时间 (1 d，90 m，2天3小时2分钟) ，rfc3339 时间戳，或 UNIX 时间戳。  如果同时 `tail` 指定了和 `since` ，则将首先使用值检索日志 `since` 。 然后，将 `tail` 值应用于结果，并返回最终结果。 可选。 |
| until | 整型 | 仅在指定的时间之前返回日志，rfc3339 时间戳、UNIX 时间戳或持续时间 (1 d，90 m，2天3小时2分钟) 。 可选。 |
| 日志级别 | 整型 | 筛选日志行小于或等于指定的日志级别。 日志行应遵循建议的日志记录格式，并使用 [Syslog 严重性级别](https://en.wikipedia.org/wiki/Syslog#Severity_level) 标准。 可选。 |
| regex | 字符串 | 使用 [.Net 正则表达式](/dotnet/standard/base-types/regular-expressions) 格式筛选具有与指定的正则表达式匹配的内容的日志行。 可选。 |
| encoding | 字符串 | `gzip` 或 `none`。 默认值为 `none`。 |
| contentType | 字符串 | `json` 或 `text`。 默认值为 `text`。 |

> [!NOTE]
> 如果日志内容超出了直接方法的响应大小限制（目前为 128 KB），则响应将返回错误。

日志检索成功后，将返回 **"status"： 200** 后跟一个有效负载，其中包含从模块检索到的日志，并按你在请求中指定的设置进行筛选。

例如：

```azurecli
az iot hub invoke-module-method --method-name 'GetModuleLogs' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
'
```

在 Azure 门户中，用方法名称 `GetModuleLogs` 和以下 JSON 有效负载调用方法：

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![在 Azure 门户中调用直接方法 "GetModuleLogs"](./media/how-to-retrieve-iot-edge-logs/invoke-get-module-logs.png)

还可以通过管道将 CLI 输出传递给 Linux 实用程序（如 [gzip](https://en.wikipedia.org/wiki/Gzip)）来处理压缩的响应。 例如：

```azurecli
az iot hub invoke-module-method \
  --method-name 'GetModuleLogs' \
  -n <hub name> \
  -d <device id> \
  -m '$edgeAgent' \
  --method-payload '{"contentType": "text","schemaVersion": "1.0","encoding": "gzip","items": [{"id": "edgeHub","filter": {"since": "2d","tail": 1000}}],}' \
  -o tsv --query 'payload[0].payloadBytes' \
  | base64 --decode \
  | gzip -d
```

## <a name="upload-module-logs"></a>上载模块日志

使用 **UploadModuleLogs** 直接方法将请求的日志发送到指定的 Azure Blob 存储容器。

此方法接受类似于 **GetModuleLogs**的 JSON 有效负载，并添加 "sasUrl" 键：

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "Full path to SAS URL",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": int,
                "since": int,
                "until": int,
                "loglevel": int,
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| 名称 | 类型 | 说明 |
|-|-|-|
| sasURL | string (URI)  | [对 Azure Blob 存储容器具有写入权限的共享访问签名 URL](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer)。 |

成功的上载日志请求将返回 **"status"： 200** 后跟具有以下架构的有效负载：

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| 名称 | 类型 | 说明 |
|-|-|-|
| status | 字符串 | 、、、或中的一个 `NotStarted` `Running` `Completed` `Failed` `Unknown` 。 |
| message | 字符串 | 如果错误，则为消息; 否则为空字符串。 |
| correlationId | 字符串   | 用于查询上载请求状态的 ID。 |

例如：

以下调用将以压缩 JSON 格式从所有模块上传最后100日志行：

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": ".*",
                "filter": {
                    "tail": 100
                }
            }
        ],
        "encoding": "gzip",
        "contentType": "json"
    }
'
```

以下调用将从 edgeAgent 和 edgeHub 中的最后一个100日志行上传 tempSensor 模块中最后1000个日志行，其中包含未压缩的文本格式：

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": "edge",
                "filter": {
                    "tail": 100
                }
            },
            {
                "id": "tempSensor",
                "filter": {
                    "tail": 1000
                }
            }
        ],
        "encoding": "none",
        "contentType": "text"
    }
'
```

在 Azure 门户中，在 `UploadModuleLogs` 用您的信息填充 sasURL 后，通过方法名称和以下 JSON 有效负载调用方法：

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "<sasUrl>",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![在 Azure 门户中调用直接方法 "UploadModuleLogs"](./media/how-to-retrieve-iot-edge-logs/invoke-upload-module-logs.png)

## <a name="upload-support-bundle-diagnostics"></a>上传支持捆绑包诊断

使用 **UploadSupportBundle** 直接方法捆绑 IoT Edge 模块日志的 zip 文件并将其上传到可用的 Azure Blob 存储容器。 此直接方法 [`iotedge support-bundle`](./troubleshoot.md#gather-debug-information-with-support-bundle-command) 在 IoT Edge 设备上运行命令，以获取日志。

此方法接受具有以下架构的 JSON 有效负载：

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

| 名称 | 类型 | 说明 |
|-|-|-|
| schemaVersion | 字符串 | 设置为 `1.0` |
| sasURL | string (URI)  | [对 Azure Blob 存储容器具有写入权限的共享访问签名 URL](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer) |
| since | 整型 | 从此时起，只返回日志，持续时间 (1 d，90 m，2天3小时2分钟) ，rfc3339 时间戳，或 UNIX 时间戳。 可选。 |
| until | 整型 | 仅在指定的时间之前返回日志，rfc3339 时间戳、UNIX 时间戳或持续时间 (1 d，90 m，2天3小时2分钟) 。 可选。 |
| edgeRuntimeOnly | boolean | 如果为 true，则仅从边缘代理、边缘中心和边缘安全守护程序返回日志。 默认值：false。  可选。 |

> [!IMPORTANT]
> IoT Edge 支持捆绑包可能包含个人身份信息。

成功的上载日志请求将返回 **"status"： 200** ，后跟与 **UploadModuleLogs** 响应具有相同架构的有效负载：

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| 名称 | 类型 | 说明 |
|-|-|-|
| status | 字符串 | 、、、或中的一个 `NotStarted` `Running` `Completed` `Failed` `Unknown` 。 |
| message | 字符串 | 如果错误，则为消息; 否则为空字符串。 |
| correlationId | 字符串   | 用于查询上载请求状态的 ID。 |

例如：

```azurecli
az iot hub invoke-module-method --method-name 'UploadSupportBundle' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
'
```

在 Azure 门户中，在 `UploadSupportBundle` 用您的信息填充 sasURL 后，通过方法名称和以下 JSON 有效负载调用方法：

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

![在 Azure 门户中调用直接方法 "UploadSupportBundle"](./media/how-to-retrieve-iot-edge-logs/invoke-upload-support-bundle.png)

## <a name="get-upload-request-status"></a>获取上传请求状态

使用 **GetTaskStatus** 直接方法可以查询上载日志请求的状态。 **GetTaskStatus**请求负载使用 `correlationId` 上传日志请求的来获取任务的状态。 `correlationId`返回以响应**UploadModuleLogs**直接方法调用。

此方法接受具有以下架构的 JSON 有效负载：

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

成功的上载日志请求将返回 **"status"： 200** ，后跟与 **UploadModuleLogs** 响应具有相同架构的有效负载：

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| 名称 | 类型 | 说明 |
|-|-|-|
| status | 字符串 | 、、、或中的一个 `NotStarted` `Running` `Completed` `Failed` `Unknown` 。 |
| message | 字符串 | 如果错误，则为消息; 否则为空字符串。 |
| correlationId | 字符串   | 用于查询上载请求状态的 ID。 |

例如：

```azurecli
az iot hub invoke-module-method --method-name 'GetTaskStatus' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
'
```

在 Azure 门户中，用 `UploadModuleLogs` 您的信息填充 GUID 后，调用方法名称和以下 JSON 有效负载的方法：

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

![在 Azure 门户中调用直接方法 "GetTaskStatus"](./media/how-to-retrieve-iot-edge-logs/invoke-get-task-status.png)

## <a name="next-steps"></a>后续步骤

[IoT Edge 代理和 IoT Edge 中心模块孪生的属性](module-edgeagent-edgehub.md)