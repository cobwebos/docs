---
title: "Azure IoT Edge 模块组合 | Microsoft 文档"
description: "了解将哪些内容放入 Azure IoT Edge 模块以及如何重复使用这些模块"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 5de67b6f1ce79934a3a6aab623d2e77a56a8ce76
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="understand-how-iot-edge-modules-can-be-used-configured-and-reused---preview"></a>了解如何使用、配置并重复使用 IoT Edge 模块 - 预览版

通过 Azure IoT Edge，可以在将多个 IoT Edge 模块部署到 IoT Edge 设备之前对其进行组合。 本文将介绍在部署之前有关组合多个 IoT Edge 模块的几个最重要的概念。 

## <a name="the-deployment-manifest"></a>部署清单
部署清单是一个 JSON 文档，用于描述以下内容：

* 必须部署哪些 IoT Edge 模块，以及它们的创建和管理选项；
* Edge 中心的配置，描述如何在模块之间，以及模块和 loT 中心之间传输消息；
* 或者，在模块孪生的所需属性中设置的值，用于配置单个模块应用程序。

在 Azure IoT Edge 教程中，你将通过 Azure IoT Edge 门户中的向导生成部署清单。 此外，也可以使用 REST 或 IoT 中心服务 SDK 以编程方式应用部署清单。 有关 IoT Edge 部署的详细信息，请参阅[部署和监控][lnk-deploy]。

部署清单在较高级别为 IoT Edge 设备上部署的 IoT Edge 模块配置模块孪生的所需属性。 其中有两个模块始终存在：Edge 代理和 Edge 中心。

清单将遵循此结构：

    {
        "moduleContent": {
            "$edgeAgent": {
                "properties.desired": {
                    // desired properties of the Edge agent
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    // desired properties of the Edge hub
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

在此部分的末尾将报告一个示例部署清单。

> [!IMPORTANT]
> 所有 IoT Edge 设备均先需要使用部署清单进行配置。 在使用有效清单进行配置前，新安装的 IoT Edge 运行时会报告错误代码。 

### <a name="specify-the-modules"></a>指定模块
Edge 代理模块孪生的所需属性包含：所需模块、模块的配置和管理选项，以及 Edge 代理的配置参数。

在高级别中，清单的这一部分包含对 IoT Edge 运行时模块（Edge 代理和 Edge 中心）和用户指定模块的模块映像和管理选项的引用。

有关清单这一个部分的详细说明，请参阅 [Edge 代理的所需属性][lnk-edgeagent-desired]。

> [!NOTE]
> 仅包含 IoT Edge 运行时（代理和中心）的部署清单是有效的。


### <a name="specify-the-routes"></a>指定路由
Edge 中心提供了一种在模块之间，以及模块和 IoT 中心之间以声明方式路由消息的方法。

路由具有以下语法：

        FROM <source>
        [WHERE <condition>]
        INTO <sink>

源可以是以下任何内容：

| Source | 说明 |
| ------ | ----------- |
| `/*` | 来自任何设备或模块的所有设备到云的消息 |
| `/messages/*` | 由设备或模块通过某些输出或不借助输出发送的任何设备到云的消息 |
| `/messages/modules/*` | 由带部分输出或不带输出的模块发送的任何设备到云的消息 |
| `/messages/modules/{moduleId}/*` | 由不带输出的 {moduleId} 发送的任何设备到云的消息 |
| `/messages/modules/{moduleId}/outputs/*` | 由带部分输出的 {moduleId} 发送的任何设备到云的消息 |
| `/messages/modules/{moduleId}/outputs/{output}` | 由使用 {output} 的 {moduleId} 发送的任何设备到云的消息 |

条件可以是 IoT 中心路由规则的 [IoT 中心查询语言][lnk-iothub-query]所支持的任何条件。

接收器可以是以下之一：

| 接收器 | 说明 |
| ---- | ----------- |
| `$upstream` | 将消息发送到 IoT 中心 |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | 将消息发送到模块 `{moduleId}` 的输入 `{input}` |

请务必注意，Edge 中心将提供至少一次保证，这意味着，在路由无法将消息传送到其接收器的情况下（例如 Edge 中心无法连接到 IoT 中心或未连接目标模块），则会本地存储消息。

Edge 中心会一直存储消息，直到达到在 Edge 中心所需属性的 `storeAndForwardConfiguration.timeToLiveSecs` 属性中所指定的时间。

### <a name="specifying-the-desired-properties-of-the-module-twin"></a>指定模块孪生所需的属性

部署清单可以指定 Edge 代理部分中所指定的每个用户模块的模块孪生的所需属性。

当在部署清单中指定所需属性后，它们将覆盖当前模块孪生中的所需属性。

如果未在部署清单中指定模块孪生的所需属性，则 IoT 中心将不会以任何方式修改模块孪生，并且你将能够以编程方式设置所需属性。

将使用用来修改设备孪生的相同机制来修改模块孪生。 有关进一步信息，请参阅[设备孪生开发人员指南](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-device-twins)。   

### <a name="deployment-manifest-example"></a>部署清单示例

这是部署清单 JSON 文档的一个示例。

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

## <a name="reference-edge-agent-module-twin"></a>参考：Edge 代理模块孪生

Edge 代理的模块孪生被称为 `$edgeAgent`，用于协调在设备与 IoT 中心上运行的 Edge 代理之间的通信。
在特定设备上将部署清单作为单一设备或规模部署的一部分应用时，将会设置所需的属性。 有关如何在 IoT Edge 设备上部署模块的详细信息，请参阅[部署和监控][lnk-deploy]。

### <a name="edge-agent-twin-desired-properties"></a>Edge 代理孪生所需属性

| 属性 | 说明 | 必选 |
| -------- | ----------- | -------- |
| schemaVersion | 必须为“1.0” | 是 |
| runtime.type | 必须为“docker” | 是 |
| runtime.settings.minDockerVersion | 设置为此部署清单所需的最小 Docker 版本 | 是 |
| runtime.settings.loggingOptions | 字符串化的 JSON 包含 Edge 代理容器的日志记录选项。 [Docker 日志记录选项][lnk-docker-logging-options] | 否 |
| systemModules.edgeAgent.type | 必须为“docker” | 是 |
| systemModules.edgeAgent.settings.image | Edge 代理的图像 URI。 目前，Edge 代理不能自行更新。 | 是 |
| systemModules.edgeAgent.settings.createOptions | 字符串化的 JSON 包含 Edge 代理容器的创建选项。 [Docker 创建选项][lnk-docker-create-options] | 否 |
| systemModules.edgeAgent.configuration.id | 部署此模块的部署 ID。 | 这是在使用部署应用此清单时，由 IoT 中心进行设置的。 不是部署清单的一部分。 |
| systemModules.edgeHub.type | 必须为“docker” | 是 |
| systemModules.edgeHub.status | 必须为“running” | 是 |
| systemModules.edgeHub.restartPolicy | 必须为“always” | 是 |
| systemModules.edgeHub.settings.image | Edge 中心的图像 URI。 | 是 |
| systemModules.edgeHub.settings.createOptions | 字符串化的 JSON 包含 Edge 中心容器的创建选项。 [Docker 创建选项][lnk-docker-create-options] | 否 |
| systemModules.edgeHub.configuration.id | 部署此模块的部署 ID。 | 这是在使用部署应用此清单时，由 IoT 中心进行设置的。 不是部署清单的一部分。 |
| modules.{moduleId}.version | 用户定义的字符串，表示此模块的版本。 | 是 |
| modules.{moduleId}.type | 必须为“docker” | 是 |
| modules.{moduleId}.restartPolicy | {"never" \| "on-failed" \| "on-unhealthy" \| "always"} | 是 |
| modules.{moduleId}.settings.image | 模块映像的 URI。 | 是 |
| modules.{moduleId}.settings.createOptions | 字符串化的 JSON 包含模块容器的创建选项。 [Docker 创建选项][lnk-docker-create-options] | 否 |
| modules.{moduleId}.configuration.id | 部署此模块的部署 ID。 | 这是在使用部署应用此清单时，由 IoT 中心进行设置的。 不是部署清单的一部分。 |

### <a name="edge-agent-twin-reported-properties"></a>Edge 代理孪生报告属性

Edge 代理报告属性包括三个主要信息：

1. 上一次出现的所需属性的应用程序状态；
2. 根据 Edge 代理的报告，目前在设备上运行的模块的状态；以及
3. 当前在设备上运行的所需属性的副本。

如果运行时未成功应用最新的所需属性，而且设备仍在运行以前的部署清单，则最后一条信息将很有用。

> [!NOTE]
> Edge 代理的报告属性非常有用，因为可以使用 [IoT 中心查询语言][lnk-iothub-query]对其进行查询，进而调查规模部署的状态。 有关如何使用此功能的详细信息，请参阅[部署][lnk-deploy]。

下表不包括从所需属性中复制的信息。

| 属性 | 说明 |
| -------- | ----------- |
| lastDesiredVersion | 此 int 指的是由 Edge 代理处理的所需属性的最后一个版本。 |
| lastDesiredStatus.code | 此为状态代码，指的是 Edge 代理所看到的最后一个所需属性。 允许的值：`200` 成功、`400` 配置无效、`412` 架构版本无效、`417` 所需属性为空、`500` 失败 |
| lastDesiredStatus.description | 状态的文本说明 |
| deviceHealth | 如果所有模块的运行时状态为 `running` 或 `stopped`，则为 `healthy`，否则为 `unhealthy` |
| configurationHealth.{deploymentId}.health | 如果部署 {deploymentId} 设置的所有模块的运行时状态为 `running` 或 `stopped`，则为 `healthy`，否则为 `unhealthy` |
| runtime.platform.OS | 报告在设备上运行的 OS |
| runtime.platform.architecture | 报告设备上的 CPU 体系结构 |
| systemModules.edgeAgent.runtimeStatus | Edge 代理的报告状态： {"running" \| "unhealthy"} |
| systemModules.edgeAgent.statusDescription | Edge 代理报告状态的文本说明。 |
| systemModules.edgeHub.runtimeStatus | Edge 中心的当前状态：{ "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | 运行状况不佳的情况下 Edge 中心当前状态的文本说明。 |
| systemModules.edgeHub.exitCode | 退出时，Edge 中心容器报告的退出代码 |
| systemModules.edgeHub.startTimeUtc | 上一次启动 Edge 中心的时间 |
| systemModules.edgeHub.lastExitTimeUtc | 上一次退出 Edge 中心的时间 |
| systemModules.edgeHub.lastRestartTimeUtc | 上一次重新启动 Edge 中心的时间 |
| systemModules.edgeHub.restartCount | 作为重启策略的一部分，此模块重新启动的次数。 |
| modules.{moduleId}.runtimeStatus | 模块的当前状态：{ "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| modules.{moduleId}.statusDescription | 运行状况不佳的情况下模块心当前状态的文本说明。 |
| modules.{moduleId}.exitCode | 退出时，模块容器报告的退出代码 |
| modules.{moduleId}.startTimeUtc | 模块的上次启动时间 |
| modules.{moduleId}.lastExitTimeUtc | 模块的上次退出时间 |
| modules.{moduleId}.lastRestartTimeUtc | 模块的上次重启时间 |
| modules.{moduleId}.restartCount | 作为重启策略的一部分，此模块重新启动的次数。 |

## <a name="reference-edge-hub-module-twin"></a>参考：Edge 中心模块孪生

Edge 中心的模块孪生被称为 `$edgeHub`，用于协调设备与 IoT 中心上运行的 Edge 中心之间的通信。
在特定设备上将部署清单作为单一设备或规模部署的一部分应用时，将会设置所需的属性。 有关如何在 IoT Edge 设备上部署模块的详细信息，请参阅[部署][lnk-deploy]。

### <a name="edge-hub-twin-desired-properties"></a>Edge 中心孪生的所需属性

| 属性 | 说明 | 部署清单中的必备项 |
| -------- | ----------- | -------- |
| schemaVersion | 必须为“1.0” | 是 |
| routes.{routeName} | 表示 Edge 中心路由的字符串。 | `routes` 元素可以存在但为空。 |
| storeAndForwardConfiguration.timeToLiveSecs | 在断开连接路由终结点的情况下（例如从 IoT 中心或本地模块断开连接），Edge 中心保留消息的时间（以秒为单位） | 是 |

### <a name="edge-hub-twin-reported-properties"></a>Edge 中心孪生报告属性

| 属性 | 说明 |
| -------- | ----------- |
| lastDesiredVersion | 此 int 指的是由 Edge 中心处理的所需属性的最后一个版本。 |
| lastDesiredStatus.code | 此为状态代码，指的是 Edge 中心所看到的最后一个所需属性。 允许的值：`200` 成功、`400` 配置无效、`500` 失败 |
| lastDesiredStatus.description | 状态的文本说明 |
| clients.{device or module identity}.status | 此设备或模块的连接状态。 可能值：{"connected" \| "disconnected"}。 仅模块标识可以处于断开连接状态。 仅在连接时才会显示连接到 Edge 中心的下游设备。 |
| clients.{device or module identity}.lastConnectTime | 设备或模块的上次连接时间 |
| clients.{device or module identity}.lastDisconnectTime | 设备或模块上次断开连接的时间 |

## <a name="next-steps"></a>后续步骤

至此，你已了解如何使用 IoT Edge 模块，接下来请继续[了解开发 IoT Edge 模块的要求和工具][lnk-module-dev]。

[lnk-deploy]: module-deployment-monitoring.md
[lnk-edgeagent-desired]: #edge-agent-twin-desired-properties
[lnk-edgeagent-reported]: #edge-agent-twin-reported-properties
[lnk=edgehub-desired]: #edge-hub-twin-desired-properties
[lnk-edgehub-reported]: #edge-hub-twin-reported-properties
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
