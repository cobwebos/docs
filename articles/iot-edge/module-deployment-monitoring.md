---
title: 设备组的自动部署 - Azure IoT Edge | Microsoft Docs
description: 使用 Azure IoT Edge 中的自动部署来管理基于共享标记的设备组
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7540c5a82220eef61b8f1cf470697315496cd6bf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82127602"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>了解单设备或大规模的 IoT Edge 自动部署

自动部署和分层部署可帮助你在大量 IoT Edge 设备上管理和配置模块。

Azure IoT Edge 提供了两种方法来配置要在 IoT Edge 设备上运行的模块。 第一种方法是在每个设备上部署模块。 创建部署清单，并按名称将其应用到特定设备。 第二种方法是将模块自动部署到满足一组定义条件的任何已注册设备。 创建一个部署清单，然后根据设备孪生中的[标记](../iot-edge/how-to-deploy-at-scale.md#identify-devices-using-tags)来定义应用它的设备。

本文重点介绍如何配置和监视设备群（统称为 IoT Edge 自动部署）。  基本部署步骤如下所述：

1. 由操作员来定义部署，描述一组模块和目标设备。 每个部署都有一个反映此信息的部署清单。
2. IoT 中心服务与所有目标设备通信，为其配置声明的模块。
3. IoT 中心服务从 IoT Edge 设备检索状态，然后将这些状态提供给操作员。  例如，如果某个 Edge 设备配置不成功，或者某个模块在运行时发生故障，操作员就会看到。
4. 随时对新的符合目标条件的 IoT Edge 设备进行部署配置。

本文将介绍配置和监视部署过程中涉及的每个组件。 如需创建和更新部署的详细介绍，请参阅[大规模部署和监视 IoT Edge 模块](how-to-deploy-at-scale.md)。

## <a name="deployment"></a>部署

loT Edge 自动部署会分配 IoT Edge 模块映像，这些映像在一组 IoT Edge 目标设备上作为实例运行。 操作方式是：配置一个 IoT Edge 部署清单，其中包括一系列模块和相应的初始化参数。 部署可以分配给单个设备（根据设备 ID 分配），也可以分配给一组设备（根据标记）。 IoT Edge 设备在收到部署清单以后，就会从各个容器存储库下载并安装容器映像，并对其进行相应的配置。 创建部署以后，操作员可以监视部署状态，看目标设备是否已正确配置。

只能通过部署配置 IoT Edge 设备。 设备在接收部署之前，必须具备以下先决条件：

* 基础操作系统
* 容器管理系统，如 Moby 或 Docker
* 预配 IoT Edge 运行时

### <a name="deployment-manifest"></a>部署清单

部署清单是一个 JSON 文档，用于描述要在目标 IoT Edge 设备上配置的模块。 它包含所有模块的配置元数据，其中包括必需的系统模块（具体说来，就是 IoT Edge 代理和 IoT Edge 中心）。  

每个模块的配置元数据包括：

* 版本
* 类型
* 状态（例如，正在运行或已停止）
* 重启策略
* 映像和容器注册表
* 数据输入和输出的路由

如果模块映像存储在专用容器注册表中，则 IoT Edge 代理会保留注册表凭据。

### <a name="target-condition"></a>目标条件

在部署的整个生存期内会持续对目标条件进行评估。 将包括满足要求的任何新设备，并删除不再满足要求的任何现有设备。 如果服务检测到任何目标条件更改，则会重新激活部署。

例如，某个部署具有目标条件 tags.environment = 'prod'。 启动该部署时，共有 10 个生产设备。 这 10 个设备都成功安装了模块。 IoT Edge 代理状态显示总共有 10 个设备，10 个成功响应，0 个失败响应，以及 0 个挂起响应。 现在，又添加 5 个 tags.environment = 'prod' 的设备。 服务检测到更改，当它部署到 5 个新设备时，IoT Edge 代理状态变为总共 15 个设备，10 个成功响应，0 个失败响应，以及 5 个挂起响应。

在设备克隆标记、设备孪生报告属性或 deviceId 中使用任何布尔条件来选择目标设备。 如果想将条件与标记结合使用，则需在设备孪生中与属性相同的级别下添加 "tags":{} 节。 [深入了解设备孪生中的标记](../iot-hub/iot-hub-devguide-device-twins.md)

目标条件的示例：

* deviceId ='linuxprod1'
* tags.environment ='prod'
* tags.environment = 'prod' AND tags.location = 'westus'
* tags.environment = 'prod' OR tags.location = 'westus'
* tags.operator = 'John' AND tags.environment = 'prod' NOT deviceId = 'linuxprod1'
* properties.reported.devicemodel = '4000x'

构造目标条件时请注意以下约束：

* 在设备孪生中，只能使用标记、报告属性或 deviceId 生成目标条件。
* 目标条件的任何部分都不允许用双引号引起来。 请使用单引号。
* 单引号表示目标条件的值。 因此，如果某个单引号是设备名称的一部分，则必须使用另一个单引号对其转义。 若要以名为 `operator'sDevice` 的设备为目标，请编写 `deviceId='operator''sDevice'`。
* 目标条件值中允许使用数字、字母和以下字符：`-:.+%_#*?!(),=@;$`。

### <a name="priority"></a>优先级

优先级定义相对于其他部署，是否更应将某个部署应用到目标设备。 部署优先级是一个正整数，数字越大表示优先级越高。 如果多个部署均以某个 IoT Edge 设备为目标，则应用优先级最高的部署。  不会应用或合并优先级较低的部署。  如果两个或两个以上优先级相同的部署以某个设备为目标，则应用最近创建的部署（取决于创建时间戳）。

### <a name="labels"></a>标签

标签是字符串键/值对，可以用于部署的筛选和分组。 一个部署可能有多个标签。 标签是可选的，不影响 IoT Edge 设备的实际配置。

### <a name="metrics"></a>指标

默认情况下，所有部署都按四个指标进行报告：

* **目标**显示与部署目标条件匹配的 IoT Edge 设备。
* **已应用**显示的目标 IoT Edge 设备尚未成为另一优先级更高的部署的目标。
* “报告成功”显示已报告了成功部署模块的 IoT Edge 设备。 
* “报告失败”显示已报告未成功部署一个或多个模块的 IoT Edge 设备。  若要进一步调查此错误，请通过远程方式连接到这些设备并查看日志文件。

此外，还可以定义自己的自定义指标来帮助监视和管理部署。

指标提供各种状态的摘要计数，设备可能在应用部署配置后报告这些状态。 指标可以查询 [edgeHub 模块孪生报告属性](module-edgeagent-edgehub.md#edgehub-reported-properties)，例如 *lastDesiredStatus* 或 *lastConnectTime*。 例如：

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

可选择添加自己的指标，这不影响 IoT Edge 设备的实际配置。

## <a name="layered-deployment"></a>分层部署

分层部署是自动部署，可将其组合在一起以减少需要创建的唯一部署数量。 在许多自动部署的不同组合中重复使用相同的模块时，分层部署非常有用。

分层部署具有与所有自动部署相同的基本组件。 它们基于设备孪生中的标记定位设备，并在标签、指标和状态报告方面提供相同的功能。 分层部署也分配有优先级，但不会使用优先级来确定将哪个部署应用于设备，而是确定在设备上如何对多个部署进行排序。 例如，如果两个分层部署具有相同名称的模块或路由，则将应用优先级较高的分层部署，同时覆盖较低的优先级。

系统运行时模块 edgeAgent 和 edgeHub 未配置为分层部署的一部分。 对于分层部署所针对的任何 IoT Edge 设备，需要先向其应用标准自动部署。 自动部署提供添加分层部署的基础。

IoT Edge 设备只能应用一个标准自动部署，但它可以应用多个分层自动部署。 任何针对设备的分层部署都必须具有比该设备的自动部署更高的优先级。

例如，请考虑下面管理建筑物的公司的情况。 他们开发 IoT Edge 模块，用于收集监控摄像机、运动传感器和电梯的数据。 但是，并非所有建筑物都可以使用这三个模块。 对于标准自动部署，公司需要为建筑所需的所有模块组合创建单独的部署。

![标准自动部署需要容纳每个模块组合](./media/module-deployment-monitoring/standard-deployment.png)

但是，一旦公司切换到分层自动部署，他们就会发现，他们可以为建筑物创建相同的模块组合，而且需要管理的部署更少。 每个模块都有其自己的分层部署，设备标记用于识别添加到每个建筑中的模块。

![分层自动部署简化了以不同方式组合相同模块的方案](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>模块孪生配置

使用分层部署时，可能有意或无意地对针对设备的同一模块使用两个部署。 在这些情况下，可以决定较高优先级的部署是否应覆盖模块孪生或进行追加。 例如，你可能有一个将同一模块应用于 100 个不同设备的部署。 但是，其中 10 个设备位于安全设施中，需要额外配置才能通过代理服务器进行通信。 你可以使用分层部署来添加模块孪生属性，以便这 10 个设备可安全通信，而不会覆盖基本部署中现有的模块孪生信息。

可以在部署清单中追加模块孪生所需属性。 在标准部署中，你可以在模块孪生的 properties.desired 部分中添加属性，而在层部署中，你可以声明所需属性的新子集  。

例如，在标准部署中，可以添加具有以下所需属性的模拟温度传感器模块，这些属性指示在 5 秒的时间间隔内发送数据：

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

在针对一部分或全部相同设备的分层部署中，可以添加一个属性，用于告知模拟传感器发送 1000 条消息，然后停止。 如果你不希望覆盖现有的属性，可以在名为 `layeredProperties` 的所需属性（其中包含新属性）中创建一个新节：

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "StopAfterCount": 1000
  }
}
```

如果设备同时应用了这两个部署，则将在模拟温度传感器的模块孪生中反映以下属性：

```json
"properties": {
  "desired": {
    "SendData": true,
    "SendInterval": 5,
    "layeredProperties": {
      "StopAfterCount": 1000
    }
  }
}
```

如果在分层部署中设置模块孪生的 `properties.desired` 字段，它将覆盖任何较低优先级的部署中该模块的所需属性。

## <a name="phased-rollout"></a>分阶段推出

分阶段推出是指操作员将更改逐渐部署到更大范围内的 IoT Edge 设备这一整个过程。 这样做的目的是逐渐进行更改，降低进行大规模重大更改的风险。 自动部署有助于管理 IoT Edge 设备群中的分阶段推出。

分阶段推出按以下阶段和步骤执行：

1. 建立一个 IoT Edge 设备的测试环境，方法是对设备进行预配，并设置类似 `tag.environment='test'` 的设备孪生标记。 该测试环境应镜像最终会成为部署目标的生产环境。
2. 创建包含所需模块和配置的部署。 目标条件应针对测试型 IoT Edge 设备环境。
3. 在测试环境中验证新的模块配置。
4. 更新部署，使之包括部分生产型 IoT Edge 设备，方法是向目标条件添加新标记。 另请确保部署的优先级高于其他目前以这些设备为目标的部署。
5. 通过查看部署状态，验证部署是否已在目标 IoT 设备上成功完成。
6. 更新部署，使之以所有剩余的生产型 IoT Edge 设备为目标。

## <a name="rollback"></a>回退

在出现错误或配置不当的情况下，可以回退部署。 由于部署为 IoT Edge 设备定义绝对的模块配置，因此即使目的是删除所有模块，也必须有另一优先级较低的部署以该设备为目标。  

删除部署不会从目标设备中删除模块。 此外，还必须有另一个部署，用于为设备定义新配置，即使它是空部署也无所谓。

请按以下顺序执行回退：

1. 确认另一部署也以同一设备集为目标。 如果回退的目的是删除所有模块，则再次部署时不应包括任何模块。
2. 修改或删除要回退的部署的目标条件表达式，使设备不再符合目标条件。
3. 通过查看部署状态，验证回退是否成功。
   * 回退的部署不应再显示已回退设备的状态。
   * 现在，再次进行的部署应包含已回退设备的部署状态。

## <a name="next-steps"></a>后续步骤

* 阅读[大规模地部署和监视 IoT Edge 模块](how-to-deploy-at-scale.md)，详细了解创建、更新或删除部署的步骤。
* 详细了解其他 IoT Edge 概念，例如 [IoT Edge 运行时](iot-edge-runtime.md)和 [IoT Edge 模块](iot-edge-modules.md)。
