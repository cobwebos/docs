---
title: 用于重新预配 Azure IoT 中心设备预配服务的设备概念 | Microsoft Docs
description: 介绍 Azure IoT 中心设备预配服务的设备重新预配概念
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: fa8cb29f145c7658227f93d08a990c98563a0cfc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729940"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>IoT 中心设备重新预配概念

在 IoT 解决方案的生命周期中，设备在 IoT 中心之间频繁移动。 此项移动的原因可能包括以下情况：

* **地理位置/GeoLatency**：当设备在两位置之间移动时，通过将设备迁移到更近的 IoT 中心来改善网络延迟。

* **多租户**：可以在同一 IoT 解决方案中使用设备并将其重新分配给新客户或客户站点。 可使用不同的 IoT 中心为这位新客户提供服务。

* **解决方案更改**：可将设备移到新版或更新后的 IoT 解决方案中。 这种重新分配可能需要设备与连接到其他后端组件的新 IoT 中心通信。

* **隔离**：类似于解决方案更改。 出现故障、被盗用或已过时的设备可能会重新分配到仅可更新和恢复其符合性的 IoT 中心。 一旦设备正常运行，它就会迁移回主中心。

在设备预配服务中重新预配支持可满足这些需求。 设备可以根据设备注册项上配置的重新预配策略自动重新分配到新的 IoT 中心。

## <a name="device-state-data"></a>设备状态数据

设备状态数据由[设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)和设备功能组成。 此数据存储在设备预配服务实例和设备所分配到的 IoT 中心中。

![使用设备预配服务进行预配](./media/concepts-device-reprovisioning/dps-provisioning.png)

设备初次使用设备预配服务实例进行预配时，将执行以下步骤：

1. 设备向设备预配服务实例发送预配请求。 服务实例基于注册项对设备标识进行身份验证，并创建设备状态数据的初始配置。 服务实例根据注册配置将设备分配给 IoT 中心，并将该 IoT 中心分配返回给设备。

2. 预配服务实例将任何初始设备状态数据的副本提供给所分配的 IoT 中心。 设备连接到已分配的 IoT 中心，并开始进行操作。

随着时间推移，IoT 中心的设备状态数据可能通过[设备操作](../iot-hub/iot-hub-devguide-device-twins.md#device-operations)和[后端操作](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations)进行更新。 存储在设备预配服务实例中的初始设备状态信息保持不变。 此未动的设备状态数据为初始配置。

![使用设备预配服务进行预配](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

根据情况的不同，当设备在 IoT 中心之间移动时，可能还需要将先前 IoT 中心上更新的设备状态迁移到新的 IoT 中心。 设备预配服务中的重新预配策略支持此迁移。

## <a name="reprovisioning-policies"></a>重新预配策略

根据具体情况，设备通常会在重启时向预配服务实例发送请求。 还支持按需手动触发预配的方法。 注册项上的重新预配策略将确定设备预配服务实例处理这些预配请求的方式。 该策略还确定是否应在重新预配期间迁移设备状态数据。 单个注册和注册组可使用相同的策略：

* **重新预配并迁移数据**：此策略是新注册项的默认策略。 当与注册项关联的设备提交新的请求时，此策略将执行操作 (1)。 根据注册项配置，可将设备重新分配给其他 IoT 中心。 如果设备正在更改 IoT 中心，则将删除初始 IoT 中心内的设备注册。 来自该初始 IoT 中心的已更新设备状态信息将迁移到新的 IoT 中心 (2)。 迁移期间，设备的状态将报告为“正在分配”。

    ![使用设备预配服务进行预配](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **重新预配并重置为初始配置**：当与注册项关联的设备提交新的预配请求时，此策略将执行操作 (1)。 根据注册项配置，设备可以重新分配给另一个 IoT 中心。 如果设备正在更改 IoT 中心，则将删除初始 IoT 中心内的设备注册。 将向新的 IoT 中心提供预配服务实例在预配设备时接收到的初始配置数数据 (2)。 迁移期间，设备的状态将报告为“正在分配”。

    此策略通常用于恢复出厂设置而无需更改 IoT 中心。

    ![使用设备预配服务进行预配](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **从不重新预配**：设备从不重新分配到不同的中心。 此策略用于管理后向兼容性。

### <a name="managing-backwards-compatibility"></a>管理后向兼容性

2018 年 9 月之前，设备分配到 IoT 中心具有粘性行为。 当设备经由预配过程返回时，它只会分配回同一个 IoT 中心。

对于已依赖此行为的解决方案，预配服务包括后向兼容性。 目前，根据以下标准对设备维护此行为：

1. 在设备预配服务中提供本机重新预配支持之前，设备与某 API 版本相连。 请参阅下面的 API 表。

2. 设备的注册项没有在设备上设置重新预配策略。

此兼容性可确保先前部署的设备经历与初始测试期间相同的行为。 要保留以前的行为，请勿将重新预配策略保存到这些注册中。 如果设置了重新预配策略，则重新预配策略优先于该行为。 通过允许重新预配策略优先，客户可以更新设备行为，而无需重置设备映像。

以下流程图有助于显示行为出现时的情况：

![后向兼容性流程图](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

下表显示了在设备预配服务中提供本机重新预配支持之前的 API 版本：

| REST API | C SDK | Python SDK |  Node SDK | Java SDK | .NET SDK |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 及更早版本](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 及更早版本](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 及更早版本](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 及更早版本](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 及更早版本](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 及更早版本](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> 这些值和链接可能会有所更改。 这只是占位符，尝试确定客户可以在何处确定版本以及预期的版本是什么。

## <a name="next-steps"></a>后续步骤

* [如何重新预配设备](how-to-reprovision.md)
