---
title: EdgeAgent 和 EdgeHub 所需属性参考 - Azure IoT Edge | Microsoft Docs
description: 查看 edgeAgent 和 edgeHub 模块孪生的特定属性及其值
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1ab45a6bde9ead69a7ea23dd095de84b8ff01334
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456707"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>IoT Edge 代理和 IoT Edge 中心模块孪生的属性

IoT Edge 代理和 IoT Edge 中心是构成 IoT Edge 运行时的两个模块。 有关每个模块的职责的详细信息，请参阅[了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。 

本文提供运行时模块孪生的所需属性和报告属性。 For more information on how to deploy modules on IoT Edge devices, see [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md).

A module twin includes: 

* **所需的属性**。 The solution backend can set desired properties, and the module can read them. The module can also receive notifications of changes in the desired properties. Desired properties are used along with reported properties to synchronize module configuration or conditions.

* **报告的属性**。 The module can set reported properties, and the solution backend can read and query them. Reported properties are used along with desired properties to synchronize module configuration or conditions. 

## <a name="edgeagent-desired-properties"></a>EdgeAgent 所需属性

IoT Edge 代理的模块孪生称为 `$edgeAgent`，用于协调设备上运行的 IoT Edge 代理与 IoT 中心之间的通信。 在特定设备上将部署清单作为单一设备或规模部署的一部分应用时，将会设置所需的属性。 

| properties | 描述 | 需要 |
| -------- | ----------- | -------- |
| schemaVersion | 必须为“1.0” | 是 |
| runtime.type | 必须为“docker” | 是 |
| runtime.settings.minDockerVersion | 设置为此部署清单所需的最小 Docker 版本 | 是 |
| runtime.settings.loggingOptions | 字符串化的 JSON 包含 IoT Edge 代理容器的日志记录选项。 [Docker 日志记录选项](https://docs.docker.com/engine/admin/logging/overview/) | No |
| runtime.settings.registryCredentials<br>.{registryId}.username | 容器注册表的用户名。 对于 Azure 容器注册表，用户名通常是注册表名称。<br><br> 对于任何未公开的模块图像，注册表凭据是必需的。 | No |
| runtime.settings.registryCredentials<br>.{registryId}.password | 容器注册表的密码。 | No |
| runtime.settings.registryCredentials<br>.{registryId}.address | 容器注册表的地址。 对于 Azure 容器注册表，地址通常为 {registry name}.azurecr.io。 | No |  
| systemModules.edgeAgent.type | 必须为“docker” | 是 |
| systemModules.edgeAgent.settings.image | IoT Edge 代理的映像 URI。 目前，IoT Edge 代理不能自行更新。 | 是 |
| systemModules.edgeAgent.settings<br>.createOptions | 字符串化的 JSON 包含用于创建 IoT Edge 代理容器的选项。 [Docker 创建选项](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeAgent.configuration.id | 部署此模块的部署 ID。 | 使用部署应用清单时，IoT 中心会设置此属性。 不是部署清单的一部分。 |
| systemModules.edgeHub.type | 必须为“docker” | 是 |
| systemModules.edgeHub.status | 必须为“running” | 是 |
| systemModules.edgeHub.restartPolicy | 必须为“always” | 是 |
| systemModules.edgeHub.settings.image | IoT Edge 中心的映像 URI。 | 是 |
| systemModules.edgeHub.settings<br>.createOptions | 字符串化的 JSON 包含用于创建 IoT Edge 中心容器的选项。 [Docker 创建选项](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeHub.configuration.id | 部署此模块的部署 ID。 | 使用部署应用清单时，IoT 中心会设置此属性。 不是部署清单的一部分。 |
| modules.{moduleId}.version | 用户定义的字符串，表示此模块的版本。 | 是 |
| modules.{moduleId}.type | 必须为“docker” | 是 |
| modules.{moduleId}.status | {"running" \| "stopped"} | 是 |
| modules.{moduleId}.restartPolicy | {"never" \| "on-failure" \| "on-unhealthy" \| "always"} | 是 |
| modules.{moduleId}.imagePullPolicy | {"on-create" \| "never"} | No |
| modules.{moduleId}.settings.image | 模块映像的 URI。 | 是 |
| modules.{moduleId}.settings.createOptions | 字符串化的 JSON 包含模块容器的创建选项。 [Docker 创建选项](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| modules.{moduleId}.configuration.id | 部署此模块的部署 ID。 | 使用部署应用清单时，IoT 中心会设置此属性。 不是部署清单的一部分。 |

## <a name="edgeagent-reported-properties"></a>EdgeAgent 报告属性

IoT Edge 代理报告属性包括三个主要信息：

1. 上一次出现的所需属性的应用程序状态；
2. 根据 IoT Edge 代理的报告，目前在设备上运行的模块的状态；以及
3. 当前在设备上运行的所需属性的副本。

This last piece of information, a copy of the current desired properties, is useful to tell whether the device has applied the latest desired properties or is still running a previous deployment manifest.

> [!NOTE]
> IoT Edge 代理的报告属性非常有用，因为可以使用 [IoT 中心查询语言](../iot-hub/iot-hub-devguide-query-language.md)对其进行查询，进而调查大规模部署的状态。 有关如何使用 IoT Edge 代理属性来获取状态的详细信息，请参阅[了解单设备 IoT Edge 部署或大规模 IoT Edge 部署](module-deployment-monitoring.md)。

下表不包括从所需属性中复制的信息。

| properties | 描述 |
| -------- | ----------- |
| lastDesiredVersion | 此整数指的是由 IoT Edge 代理处理的所需属性的最后一个版本。 |
| lastDesiredStatus.code | This status code refers to the last desired properties seen by the IoT Edge agent. 允许的值：`200` 成功、`400` 配置无效、`412` 架构版本无效、`417` 所需属性为空、`500` 失败 |
| lastDesiredStatus.description | 状态的文本说明 |
| deviceHealth | 如果所有模块的运行时状态为 `running` 或 `stopped`，则为 `healthy`，否则为 `unhealthy` |
| configurationHealth.{deploymentId}.health | 如果部署 {deploymentId} 设置的所有模块的运行时状态为 `running` 或 `stopped`，则为 `healthy`，否则为 `unhealthy` |
| runtime.platform.OS | 报告在设备上运行的 OS |
| runtime.platform.architecture | 报告设备上的 CPU 体系结构 |
| systemModules.edgeAgent.runtimeStatus | IoT Edge 代理的报告状态：{"running" \| "unhealthy"} |
| systemModules.edgeAgent.statusDescription | IoT Edge 代理报告状态的文本说明。 |
| systemModules.edgeHub.runtimeStatus | IoT Edge 中心的状态：{ "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | 运行不正常的情况下 IoT Edge 中心状态的文本说明。 |
| systemModules.edgeHub.exitCode | The exit code reported by the IoT Edge hub container if the container exits |
| systemModules.edgeHub.startTimeUtc | 上一次启动 IoT Edge 中心的时间 |
| systemModules.edgeHub.lastExitTimeUtc | 上一次退出 IoT Edge 中心的时间 |
| systemModules.edgeHub.lastRestartTimeUtc | 上一次重新启动 IoT Edge 中心的时间 |
| systemModules.edgeHub.restartCount | 作为重启策略的一部分，此模块重新启动的次数。 |
| modules.{moduleId}.runtimeStatus | 模块的状态：{ "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| modules.{moduleId}.statusDescription | 运行不正常的情况下模块状态的文本说明。 |
| modules.{moduleId}.exitCode | The exit code reported by the module container if the container exits |
| modules.{moduleId}.startTimeUtc | 模块的上次启动时间 |
| modules.{moduleId}.lastExitTimeUtc | 模块的上次退出时间 |
| modules.{moduleId}.lastRestartTimeUtc | 模块的上次重启时间 |
| modules.{moduleId}.restartCount | 作为重启策略的一部分，此模块重新启动的次数。 |

## <a name="edgehub-desired-properties"></a>EdgeHub 所需属性

IoT Edge 中心的模块孪生称为 `$edgeHub`，用于协调设备上运行的 IoT Edge 中心与 IoT 中心之间的通信。 在特定设备上将部署清单作为单一设备或规模部署的一部分应用时，将会设置所需的属性。 

| properties | 描述 | 部署清单中的必备项 |
| -------- | ----------- | -------- |
| schemaVersion | 必须为“1.0” | 是 |
| routes.{routeName} | 表示 IoT Edge 中心路由的字符串。 For more information, see [Declare routes](module-composition.md#declare-routes). | `routes` 元素可以存在但为空。 |
| storeAndForwardConfiguration.timeToLiveSecs | The time in seconds that IoT Edge hub keeps messages if disconnected from routing endpoints, whether IoT Hub or a local module. The value can be any positive integer. | 是 |

## <a name="edgehub-reported-properties"></a>EdgeHub 报告属性

| properties | 描述 |
| -------- | ----------- |
| lastDesiredVersion | 此整数指的是由 IoT Edge 中心处理的所需属性的最后一个版本。 |
| lastDesiredStatus.code | The status code referring to last desired properties seen by the IoT Edge hub. 允许的值：`200` 成功、`400` 配置无效、`500` 失败 |
| lastDesiredStatus.description | Text description of the status. |
| clients.{device or moduleId}.status | 此设备或模块的连接状态。 可能值：{"connected" \| "disconnected"}。 仅模块标识可以处于断开连接状态。 仅在连接时才会显示连接到 IoT Edge 中心的下游设备。 |
| clients.{device or moduleId}.lastConnectTime | Last time the device or module connected. |
| clients.{device or moduleId}.lastDisconnectTime | Last time the device or module disconnected. |

## <a name="next-steps"></a>后续步骤

若要了解如何使用这些属性构建部署清单，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。
