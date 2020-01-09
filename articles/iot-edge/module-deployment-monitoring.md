---
title: 设备组的自动部署 - Azure IoT Edge | Microsoft Docs
description: 使用 Azure IoT Edge 中的自动部署来管理基于共享标记的设备组
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 13390de8d3008907a0b55bf3a61c931dfdcd84e6
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552349"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>了解单设备或大规模的 IoT Edge 自动部署

自动部署和分层部署可帮助你在大量 IoT Edge 设备上管理和配置模块。 

Azure IoT Edge 提供了两种方法来配置要在 IoT Edge 设备上运行的模块。 第一种方法是在每个设备上部署模块。 创建部署清单，并按名称将其应用到特定设备。 第二种方法是将模块自动部署到满足一组定义的条件的任何已注册设备。 你将创建一个部署清单，然后根据设备克隆中的[标记](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags)定义适用于它的设备。 

本文重点介绍设备群的配置和监视阶段，统称为 IoT Edge 自动部署。 总体部署步骤如下所示： 

1. 由操作员来定义部署，描述一组模块和目标设备。 每个部署都有一个反映此信息的部署清单。 
2. IoT 中心服务与所有目标设备通信，为其配置所需模块。 
3. IoT 中心服务从 IoT Edge 设备检索状态，然后将这些状态提供给操作员。  例如，操作员可以看到边缘设备未成功配置，或在运行时模块出现故障。 
4. 随时对新的符合目标条件的 IoT Edge 设备进行部署配置。 
 
本文将介绍配置和监视部署过程中涉及的每个组件。 如需创建和更新部署的详细介绍，请参阅[大规模部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)。

## <a name="deployment"></a>部署

loT Edge 自动部署会分配 IoT Edge 模块映像，这些映像在一组 IoT Edge 目标设备上作为实例运行。 操作方式是：配置一个 IoT Edge 部署清单，其中包括一系列模块和相应的初始化参数。 可以将部署分配到单个设备（基于设备 ID）或设备组（基于标记）。 IoT Edge 设备收到部署清单后，会从各自的容器存储库下载并安装容器映像，并对其进行相应配置。 创建部署后，操作员可以监视部署状态，以查看是否已正确配置目标设备。

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

在部署的整个生命周期内会持续评估目标条件。 将包括满足要求的任何新设备，并删除不再满足要求的任何现有设备。 如果服务检测到任何目标条件更改，则会重新激活部署。 

例如，部署 A 具有目标条件 tags.environment = 'prod'。 启动该部署时，共有 10 个生产设备。 这 10 个设备都成功安装了模块。 IoT Edge 代理状态显示为总共 10 个设备，10 个成功响应，0 个失败响应，以及 0 个挂起响应。 现在，又添加 5 个 tags.environment = 'prod' 的设备。 服务检测到更改，当它尝试部署到这 5 个新设备时，IoT Edge 代理状态变为总共 15 个设备，10 个成功响应，0 个失败响应，以及 5 个挂起响应。

使用设备孪生标记或 deviceId 上的任意布尔条件来选择目标设备。 如果想将条件与标记结合使用，则需在设备孪生中与属性相同的级别下添加 "tags":{} 节。 [深入了解设备孪生中的标记](../iot-hub/iot-hub-devguide-device-twins.md)

目标条件示例：

* deviceId ='linuxprod1'
* tags.environment ='prod'
* tags.environment = 'prod' AND tags.location = 'westus'
* tags.environment = 'prod' OR tags.location = 'westus'
* tags.operator = 'John' AND tags.environment = 'prod' NOT deviceId = 'linuxprod1'

下面是构造目标条件时的一些限制：

* 在设备孪生中，只能使用标记或 deviceId 生成目标条件。
* 目标条件的任何部分都不允许用双引号引起来。 请使用单引号。
* 单引号表示目标条件的值。 因此，如果某个单引号是设备名称的一部分，则必须使用另一个单引号对其转义。 若要以名为 `operator'sDevice` 的设备为目标，请编写 `deviceId='operator''sDevice'`。
* 目标条件值中允许使用数字、字母和以下字符：`-:.+%_#*?!(),=@;$`。

### <a name="priority"></a>优先级

优先级定义相对于其他部署，是否更应将某个部署应用到目标设备。 部署优先级是一个正整数，数字越大表示优先级越高。 如果多个部署均以某个 IoT Edge 设备为目标，则应用优先级最高的部署。  优先级较低的部署不会应用，也不会合并。  如果设备面向具有相同优先级的两个或多个部署，则会应用最近创建的部署（由创建时间戳确定）。

### <a name="labels"></a>标签 

标签是可用于对部署进行筛选和分组的字符串键/值对。 部署可以有多个标签。 标签是可选的，不会影响 IoT Edge 设备的实际配置。 

### <a name="metrics"></a>度量值

默认情况下，所有部署都按四个指标报告：

* **目标**显示与部署目标条件相匹配的 IoT Edge 设备。
* **应用**显示目标 IoT Edge 设备，这些设备不是以更高优先级的另一个部署为目标的设备。
* **报告成功**显示已报告回服务的 IoT Edge 设备已成功部署模块。 
* **报告失败**显示已报告回服务的 IoT Edge 设备，其中一个或多个模块未成功部署。 若要进一步调查此错误，请通过远程方式连接到这些设备并查看日志文件。

此外，还可以定义自己的自定义指标来帮助监视和管理部署。 

指标提供设备可能会因为应用部署配置而报告回来的各种状态的汇总计数。 度量值可以查询[edgeHub 模块克隆的报告属性](module-edgeagent-edgehub.md#edgehub-reported-properties)，如上一个所需状态或上次连接时间。 例如： 

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

添加自己的度量值是可选的，并且不会影响 IoT Edge 设备的实际配置。 

## <a name="layered-deployment"></a>分层部署

分层部署是可组合在一起以减少需要创建的唯一部署数的自动部署。 在许多自动部署的不同组合中重复使用相同的模块时，分层部署非常有用。 

分层部署与任何自动部署具有相同的基本组件。 它们基于设备孪生中的标记定位设备，并提供与标签、指标和状态报告相同的功能。 分层部署也分配有优先级，但不使用优先级来确定应用到设备的部署，优先级决定了如何在设备上对多个部署进行排序。 例如，如果两个分层部署具有相同名称的模块或路由，则将应用优先级较高的分层部署，同时覆盖较低的优先级。 

系统运行时模块 edgeAgent 和 edgeHub 未配置为分层部署的一部分。 分层部署所面向的任何 IoT Edge 设备都需要首先应用标准自动部署来提供可添加分层部署的基础。 

IoT Edge 设备只能应用一个标准自动部署，但它可以应用多个分层自动部署。 面向设备的任何分层部署的优先级必须高于该设备的自动部署。 

例如，请考虑公司的以下方案来管理建筑物。 它们开发 IoT Edge 用于从安全摄像机、运动传感器和电梯收集数据的模块。 但是，并非所有的建筑物都可以使用所有这三个模块。 对于标准自动部署，公司需要为大楼所需的所有模块组合创建单独的部署。 

![标准自动部署需要容纳每个模块组合](./media/module-deployment-monitoring/standard-deployment.png)

但是，一旦公司切换到分层的自动部署，他们就会发现，他们可以为建筑物创建相同的模块组合，且部署更少。 每个模块都有其自己的分层部署，设备标记用于识别哪些模块已添加到每个建筑中。 

![分层自动部署简化了相同模块以不同方式组合的方案](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>模块克隆配置

使用分层部署时，您可以有意或其他方式将两个部署与面向设备的模块一起使用。 在这些情况下，你可以决定较高优先级的部署是否应覆盖模块克隆或追加到它上面。 例如，你可能有一个将同一模块应用于100不同设备的部署。 但是，其中10个设备位于安全设备中，需要额外配置才能通过代理服务器进行通信。 你可以使用分层部署来添加模块克隆属性，这两个设备可安全通信，而不会覆盖基本部署中现有的模块克隆信息。 

可以在部署清单中追加模块克隆所需属性。 在标准部署中，你将在 "模块" 中的 "**所需的属性**" 部分中添加属性。在分层部署中，你可以声明所需属性的新子集。 

例如，在标准部署中，你可以添加具有以下所需属性的模拟温度传感器模块，这些属性指示在5秒的时间间隔内发送数据：

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

在面向相同设备或同一设备的子集的分层部署中，你可能想要添加一个附加属性，该属性告知模拟传感器发送1000消息，然后停止。 您不希望覆盖现有的属性，因此您可以在被称为 `layeredProperties` 包含新属性的所需属性中创建一个新部分：

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "StopAfterCount": 1000
  }
}
```

应用了两个部署的设备将在模拟温度传感器的模块克隆中反映以下内容： 

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

如果在分层部署中设置模块的 "`properties.desired`" 字段，它将在任何较低优先级的部署中覆盖该模块的所需属性。 

## <a name="phased-rollout"></a>分阶段推出 

分阶段推出是指操作员将更改逐渐部署到更大范围内的 IoT Edge 设备这一整个过程。 这样做的目的是逐渐进行更改，降低进行大规模重大更改的风险。 自动部署有助于跨一 IoT Edge 设备管理分阶段推出。 

分阶段推出按以下阶段和步骤执行： 

1. 建立一个 IoT Edge 设备的测试环境，方法是对设备进行预配，并设置类似 `tag.environment='test'` 的设备孪生标记。 测试环境应镜像部署最终将面向的生产环境。 
2. 创建包含所需模块和配置的部署。 目标条件应针对测试型 IoT Edge 设备环境。   
3. 在测试环境中验证新的模块配置。
4. 更新部署，使之包括部分生产型 IoT Edge 设备，方法是向目标条件添加新标记。 另请确保部署的优先级高于其他目前以这些设备为目标的部署。 
5. 通过查看部署状态，验证部署是否已在目标 IoT 设备上成功完成。
6. 更新部署，使之以所有剩余的生产型 IoT Edge 设备为目标。

## <a name="rollback"></a>回滚

在出现错误或配置不当的情况下，可以回退部署。 由于部署定义了 IoT Edge 设备的绝对模块配置，因此还必须以较低的优先级将额外的部署目标设置为相同的设备，即使目标是删除所有模块。  

删除部署不会从目标设备中删除这些模块。 还必须有一个定义设备新配置的部署，即使它是空部署。 

请按以下顺序执行回退： 

1. 确认另一部署也以同一设备集为目标。 如果回退的目的是删除所有模块，则再次部署时不应包括任何模块。 
2. 修改或删除要回退的部署的目标条件表达式，使设备不再符合目标条件。
3. 通过查看部署状态，验证回退是否成功。
   * 回退的部署不应再显示已回退设备的状态。
   * 现在，再次进行的部署应包含已回退设备的部署状态。


## <a name="next-steps"></a>后续步骤

* 阅读[大规模地部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)，详细了解创建、更新或删除部署的步骤。
* 详细了解其他 IoT Edge 概念，例如 [IoT Edge 运行时](iot-edge-runtime.md)和 [IoT Edge 模块](iot-edge-modules.md)。

