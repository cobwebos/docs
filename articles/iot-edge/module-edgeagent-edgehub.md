---
title: Azure IoT EdgeAgent 和 EdgeHub 参考 | Microsoft Docs
description: 查看 edgeAgent 和 edgeHub 模块孪生的特定属性及其值
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0b9e7421bb09e619b4a820910db5faa9edfcc5d5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632901"
---
# <a name="properties-of-the-edge-agent-and-edge-hub-module-twins"></a>Edge 代理和 Edge 中心模块孪生的属性

Edge 代理和 Edge 中心是构成 IoT Edge 运行时的两个模块。 有关每个模块的职责的详细信息，请参阅[了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。 

本文提供运行时模块孪生的所需属性和报告属性。 有关如何在 IoT Edge 设备上部署模块的详细信息，请参阅[部署和监控][lnk-deploy]。

## <a name="edgeagent-desired-properties"></a>EdgeAgent 所需属性

Edge 代理的模块孪生被称为 `$edgeAgent`，用于协调在设备与 IoT 中心上运行的 Edge 代理之间的通信。 在特定设备上将部署清单作为单一设备或规模部署的一部分应用时，将会设置所需的属性。 

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

## <a name="edgeagent-reported-properties"></a>EdgeAgent 报告属性

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
| lastDesiredVersion | 此整数指的是由 Edge 代理处理的所需属性的最后一个版本。 |
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

## <a name="edgehub-desired-properties"></a>EdgeHub 所需属性

Edge 中心的模块孪生被称为 `$edgeHub`，用于协调设备与 IoT 中心上运行的 Edge 中心之间的通信。 在特定设备上将部署清单作为单一设备或规模部署的一部分应用时，将会设置所需的属性。 

| 属性 | 说明 | 部署清单中的必备项 |
| -------- | ----------- | -------- |
| schemaVersion | 必须为“1.0” | 是 |
| routes.{routeName} | 表示 Edge 中心路由的字符串。 | `routes` 元素可以存在但为空。 |
| storeAndForwardConfiguration.timeToLiveSecs | 在断开路由终结点连接的情况下（例如从 IoT 中心或本地模块断开连接），Edge 中心保留消息的时间（以秒为单位） | 是 |

## <a name="edgehub-reported-properties"></a>EdgeHub 报告属性

| 属性 | 说明 |
| -------- | ----------- |
| lastDesiredVersion | 此整数指的是由 Edge 中心处理的所需属性的最后一个版本。 |
| lastDesiredStatus.code | 此为状态代码，指的是 Edge 中心所看到的最后一个所需属性。 允许的值：`200` 成功、`400` 配置无效、`500` 失败 |
| lastDesiredStatus.description | 状态的文本说明 |
| clients.{device or module identity}.status | 此设备或模块的连接状态。 可能值：{"connected" \| "disconnected"}。 仅模块标识可以处于断开连接状态。 仅在连接时才会显示连接到 Edge 中心的下游设备。 |
| clients.{device or module identity}.lastConnectTime | 设备或模块的上次连接时间 |
| clients.{device or module identity}.lastDisconnectTime | 设备或模块上次断开连接的时间 |

## <a name="next-steps"></a>后续步骤

若要了解如何使用这些属性构建部署清单，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。

<!--links -->
[lnk-deploy]: module-deployment-monitoring.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
