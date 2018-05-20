---
title: Azure IoT Edge 模块组合 | Microsoft 文档
description: 了解将哪些内容放入 Azure IoT Edge 模块以及如何重复使用这些模块
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/23/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 3d95a602815cd444fb4b062853d9d31b75993e6a
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
---
# <a name="understand-how-iot-edge-modules-can-be-used-configured-and-reused---preview"></a>了解如何使用、配置并重复使用 IoT Edge 模块 - 预览版

每个 IoT Edge 设备至少运行两个模块：$edgeAgent 和 $edgeHub，它们构成了 IoT Edge 运行时。 除这两个标准模块以外，任何 IoT Edge 设备都可以运行多个模块来执行任意数量的进程。 一次性将所有这些模块部署到设备时，需要通过某种方式来声明要包含哪些模块，以及这些模块的交互方式。 

部署清单是一个 JSON 文档，用于描述以下内容：

* 必须部署哪些 IoT Edge 模块，以及它们的创建和管理选项。
* Edge 中心的配置，描述消息如何在模块之间流动，并最终传送到 IoT 中心。
* 或者，在模块孪生的所需属性中设置的值，用于配置单个模块应用程序。

所有 IoT Edge 设备均先需要使用部署清单进行配置。 在使用有效清单进行配置前，新安装的 IoT Edge 运行时会报告错误代码。 

在 Azure IoT Edge 教程中，你将通过 Azure IoT Edge 门户中的向导生成部署清单。 此外，也可以使用 REST 或 IoT 中心服务 SDK 以编程方式应用部署清单。 有关 IoT Edge 部署的详细信息，请参阅[部署和监控][lnk-deploy]。

## <a name="create-a-deployment-manifest"></a>创建部署清单。

部署清单在较高级别为 IoT Edge 设备上部署的 IoT Edge 模块配置模块孪生的所需属性。 其中有两个模块始终存在：Edge 代理和 Edge 中心。

仅包含 IoT Edge 运行时（代理和中心）的部署清单是有效的。

清单将遵循此结构：

```json
{
    "moduleContent": {
        "$edgeAgent": {
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
            }
        },
        "$edgeHub": {
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "{module1}": {  // optional
            "properties.desired": {
                // desired properties of module with id {module1}
            }
        },
        "{module2}": {  // optional
            ...
        },
        ...
    }
}
```

## <a name="configure-modules"></a>配置模块

除了为要部署的所有模块建立所需属性以外，还需要告知 IoT Edge 运行时要如何安装这些模块。 所有模块的配置和管理信息将会传入 **$edgeAgent** 所需属性。 此信息包括 Edge 代理本身的配置参数。 

有关可以或必须包含的属性的完整列表，请参阅 [Edge 代理和 Edge 中心的属性](module-edgeagent-edgehub.md)。

$edgeAgent 属性遵循此结构：

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "{module1}": { //optional
                // configuration and management details
            },
            "{module2}": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>声明路由

Edge 中心提供了一种在模块之间，以及模块和 IoT 中心之间以声明方式路由消息的方法。 Edge 中心管理所有通信，因此，路由信息将会传入 **$edgeHub** 所需属性。 可以在同一个部署中创建多个路由。

使用以下语法在 **$edgeHub** 所需属性中声明路由：

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "{route1}": "FROM <source> WHERE <condition> INTO <sink>",
            "{route2}": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

每个路由需要源和接收器，但条件是可用于筛选消息的可选片断。 


### <a name="source"></a>Source
源指定消息来自何处。 可以是以下任一值：

| Source | 说明 |
| ------ | ----------- |
| `/*` | 来自任何设备或模块的所有设备到云的消息 |
| `/messages/*` | 由设备或模块通过某些输出或不借助输出发送的任何设备到云的消息 |
| `/messages/modules/*` | 由带部分输出或不带输出的模块发送的任何设备到云的消息 |
| `/messages/modules/{moduleId}/*` | 由不带输出的 {moduleId} 发送的任何设备到云的消息 |
| `/messages/modules/{moduleId}/outputs/*` | 由带部分输出的 {moduleId} 发送的任何设备到云的消息 |
| `/messages/modules/{moduleId}/outputs/{output}` | 由使用 {output} 的 {moduleId} 发送的任何设备到云的消息 |

### <a name="condition"></a>条件
条件在路由声明中是可选的。 若要将所有消息从接收器传递到源，完全省略 **WHERE** 子句即可。 或者，可以使用 [IoT 中心查询语言][lnk-iothub-query]来筛选满足条件的特定消息或消息类型。

在 IoT Edge 中的模块之间传递的消息与在设备和 Azure IoT 中心之间传递的消息的格式是一样的。 所有消息都是 JSON 格式的，并具备 systemProperties、appProperties 和 body 参数。 

可以使用下列语法围绕这三个参数生成查询： 

* 系统属性：`$<propertyName>` 或 `{$<propertyName>}`
* 应用程序属性：`<propertyName>`
* 正文属性：`$body.<propertyName>` 

请参考[设备到云消息路由查询表达式](../iot-hub/iot-hub-devguide-query-language.md#device-to-cloud-message-routes-query-expressions)，查看针对消息属性创建查询的示例。

有一个 IoT Edge 特定的示例，即筛选从叶设备到网关设备的消息的情况。 来自模块的消息包含名为 connectionModuleId 的系统属性。 因此，若要将消息从叶设备直接路由到 IoT 中心，请使用以下路由来排除模块消息：

```sql
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>接收器
接收器定义消息发送到的位置。 可以是以下任一值：

| 接收器 | 说明 |
| ---- | ----------- |
| `$upstream` | 将消息发送到 IoT 中心 |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | 将消息发送到模块 `{moduleId}` 的输入 `{input}` |

请务必注意，Edge 中心将提供至少一次保证，这意味着，在路由无法将消息传送到其接收器的情况下（例如 Edge 中心无法连接到 IoT 中心或未连接目标模块），则会本地存储消息。

Edge 中心会一直存储消息，直到达到在 [Edge 中心所需属性](module-edgeagent-edgehub.md)的 `storeAndForwardConfiguration.timeToLiveSecs` 属性中指定的时间。

## <a name="define-or-update-desired-properties"></a>定义或更新所需属性 

部署清单可以指定部署到 IoT Edge 设备的每个模块的模块孪生所需属性。 当在部署清单中指定所需属性后，它们将覆盖当前模块孪生中的所需属性。

如果未在部署清单中指定模块孪生的所需属性，则 IoT 中心将不会以任何方式修改模块孪生，并且你将能够以编程方式设置所需属性。

将使用用来修改设备孪生的相同机制来修改模块孪生。 有关更多信息，请参阅[设备孪生开发人员指南](../iot-hub/iot-hub-devguide-device-twins.md)。   

## <a name="deployment-manifest-example"></a>部署清单示例

这是部署清单 JSON 文档的一个示例。

```json
{
"moduleContent": {
    "$edgeAgent": {
        "properties.desired": {
            "schemaVersion": "1.0",
            "runtime": {
                "type": "docker",
                "settings": {
                    "minDockerVersion": "v1.25",
                    "loggingOptions": ""
                }
            },
            "systemModules": {
                "edgeAgent": {
                    "type": "docker",
                    "settings": {
                    "image": "microsoft/azureiotedge-agent:1.0-preview",
                    "createOptions": ""
                    }
                },
                "edgeHub": {
                    "type": "docker",
                    "status": "running",
                    "restartPolicy": "always",
                    "settings": {
                    "image": "microsoft/azureiotedge-hub:1.0-preview",
                    "createOptions": ""
                    }
                }
            },
            "modules": {
                "tempSensor": {
                    "version": "1.0",
                    "type": "docker",
                    "status": "running",
                    "restartPolicy": "always",
                    "settings": {
                    "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
                    "createOptions": "{}"
                    }
                },
                "filtermodule": {
                    "version": "1.0",
                    "type": "docker",
                    "status": "running",
                    "restartPolicy": "always",
                    "settings": {
                    "image": "myacr.azurecr.io/filtermodule:latest",
                    "createOptions": "{}"
                    }
                }
            }
        }
    },
    "$edgeHub": {
        "properties.desired": {
            "schemaVersion": "1.0",
            "routes": {
                "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
                "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
            },
            "storeAndForwardConfiguration": {
                "timeToLiveSecs": 10
            }
        }
    }
}
}
```

## <a name="next-steps"></a>后续步骤

* 有关在 $edgeAgent 和 $edgeHub 中可以或必须包含的属性的完整列表，请参阅 [Edge 代理和 Edge 中心的属性](module-edgeagent-edgehub.md)。

* 至此，你已了解如何使用 IoT Edge 模块，接下来请继续[了解开发 IoT Edge 模块的要求和工具][lnk-module-dev]。

[lnk-deploy]: module-deployment-monitoring.md
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
