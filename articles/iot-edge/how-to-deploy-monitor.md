---
title: 大规模部署模块 Azure 门户-Azure IoT Edge
description: 使用 Azure 门户为 IoT Edge 设备组创建自动部署
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0a20ea4236683e26c51bc75309435c65e24271d7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271429"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>使用 Azure 门户大规模部署和监视 IoT Edge 模块

在 Azure 门户中创建**IoT Edge 自动部署**，以便一次管理多个设备的正在进行的部署。 IoT Edge 的自动部署是 IoT 中心[自动设备管理](/azure/iot-hub/iot-hub-automatic-device-management)功能的一部分。 部署是动态过程，使你能够将多个模块部署到多个设备、跟踪模块的状态和运行状况，并在必要时进行更改。

有关详细信息，请参阅[了解 IoT Edge 单个设备的自动部署或大规模部署](module-deployment-monitoring.md)。

## <a name="identify-devices-using-tags"></a>使用标记标识设备

创建部署之前，必须能够指定想要影响的设备。 Azure IoT Edge 标识使用设备孪生中的标记标识设备。 每个设备都可以有多个标记，您可以使用这些标记来定义解决方案。

例如，如果您管理一座智能建筑物，则可以将位置、房间类型和环境标记添加到设备中：

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

有关设备孪生和标记的详细信息，请参阅[了解和使用 IoT 中心的设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)。

## <a name="create-a-deployment"></a>创建部署

IoT Edge 提供了两种不同类型的自动部署，可用于自定义方案。 您可以创建标准*部署*，其中包括系统运行时模块以及任何其他模块和路由。 每个设备只能应用一个部署。 或者，你可以创建一个*分层部署*，它只包括自定义模块和路由，而不包括系统运行时。 在标准部署的基础上，可以将许多分层部署组合到一台设备上。 有关这两种类型的自动部署如何协同工作的详细信息，请参阅[了解 IoT Edge 单个设备的自动部署或大规模部署](module-deployment-monitoring.md)。

创建部署和分层部署的步骤非常类似。 在以下步骤中，将调用任何不同之处。

1. 在[Azure 门户](https://portal.azure.com)中转到 IoT 中心。
1. 在左侧窗格中的菜单上，选择 "**自动设备管理**" 下的 " **IoT Edge** "。
1. 在上部栏中，选择 "**创建部署**" 或 "**创建分层部署**"。

创建部署需要五个步骤。 下列各节将引导完成每个步骤。

### <a name="step-1-name-and-label"></a>步骤1：名称和标签

1. 为部署提供唯一名称（最多包含 128 个小写字母）。 避免空格和以下无效字符：`& ^ [ ] { } \ | " < > /`。
1. 可以将标签作为键/值对添加，以帮助跟踪部署。 例如，**HostPlatform** 和 **Linux**，或 **Version** 和 **3.0.1**。
1. 选择 "**下一步：** 要移动到步骤2的模块"。

### <a name="step-2-modules"></a>步骤2：模块

最多可以将20个模块添加到部署。 如果创建的部署不包含任何模块，则会从目标设备中删除所有当前模块。

在部署中，你可以管理 IoT Edge 代理和 IoT Edge 中心模块的设置。 选择 "**运行时设置**"，以配置两个运行时模块。 在分层部署中，不包括运行时模块，因此无法对其进行配置。

可以添加三种类型的模块：

* IoT Edge 模块
* Marketplace 模块
* Azure 流分析模块

#### <a name="add-an-iot-edge-module"></a>添加 IoT Edge 模块

若要将自定义代码添加为模块，或手动添加 Azure 服务模块，请执行以下步骤：

1. 在页面的 "**容器注册表凭据**" 部分中，为包含此部署的模块映像的任何专用容器注册表提供名称和凭据。 如果 IoT Edge 代理找不到 Docker 映像的容器注册表凭据，则会报告错误500。
1. 在页面的“IoT Edge 模块”部分中，单击“添加”。
1. 从下拉菜单中选择 " **IoT Edge 模块**"。
1. 为模块指定**IoT Edge 模块名称**。
1. 对于“映像 URI”字段，输入模块的容器映像。
1. 使用下拉菜单选择“重启策略”。 从以下选项中选择：
   * **始终**-如果模块因任何原因而关闭，则始终会重新启动。
   * **从不**-如果模块因任何原因而关闭，则不会重新启动。
   * **故障**-如果模块发生故障，则会重新启动，但不会重新启动。
   * **在运行不正常**时-如果模块发生故障或者返回不正常状态，则会重新启动。 这取决于每个执行运行状况监控功能的模块。
1. 使用下拉菜单选择模块的所需状态。 从以下选项中选择：
   * **运行**的是默认选项。 该模块在部署之后将立即开始运行。
   * **已停止**-在部署后，模块将保持空闲状态，直到你或另一个模块开始调用。
1. 指定应传递到容器的任意容器创建选项。 有关详细信息，请参阅 [docker create](https://docs.docker.com/engine/reference/commandline/create/)。
1. 如果要向模块克隆添加标记或其他属性，请选择 "**模块" 设置**。
1. 输入此模块的“环境变量”。 环境变量向模块提供配置信息。
1. 选择 "**添加**"，将模块添加到部署。

#### <a name="add-a-module-from-the-marketplace"></a>从 Marketplace 添加模块

若要从 Azure Marketplace 添加模块，请执行以下步骤：

1. 在页面的“IoT Edge 模块”部分中，单击“添加”。
1. 从下拉菜单中选择 " **Marketplace 模块**"。
1. 从 " **IoT Edge 模块市场**" 页中选择模块。 系统会自动为您的订阅、资源组和设备配置所选的模块。 然后，它将显示在 IoT Edge 模块列表中。 某些模块可能需要其他配置。 有关详细信息，请参阅[从 Azure Marketplace 部署模块](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace)。

#### <a name="add-a-stream-analytics-module"></a>添加流分析模块

要添加 Azure 流分析中的模块，请执行以下步骤：

1. 在页面的“IoT Edge 模块”部分中，单击“添加”。
1. 从下拉菜单中选择 " **Azure 流分析模块**"。
1. 在右侧窗格中，选择你的**订阅**。
1. 选择 IoT **Edge 作业**。
1. 选择“保存”，将模块添加到部署。

#### <a name="configure-module-settings"></a>配置模块设置

向部署中添加模块后，可以选择其名称，以打开 "**更新 IoT Edge 模块**" 页。 在此页上，您可以编辑 "模块设置"、"环境变量"、"创建选项" 和 "模块克隆"。 如果从 marketplace 添加了模块，则它可能已填充了其中一些参数。

如果创建的是分层部署，则可能会配置一个模块，该模块在面向相同设备的其他部署中存在。 若要在不覆盖其他版本的情况下更新模块内键，请打开 "**模块克隆设置**" 选项卡。使用模块克隆的所需属性中子节的唯一名称创建新的**模块克隆属性**，例如 `properties.desired.settings`。 如果只在 `properties.desired` 字段中定义属性，则它将覆盖在任何较低优先级部署中定义的模块的所需属性。

![设置分层部署的模块克隆属性](./media/how-to-deploy-monitor/module-twin-property.png)

有关分层部署中的模块克隆配置的详细信息，请参阅[分层部署](module-deployment-monitoring.md#layered-deployment)。

配置完部署的所有模块后，选择 "**下一步：路由**" 转到步骤3。

### <a name="step-3-routes"></a>步骤3：路由

路由定义模块在部署中如何相互通信。 默认情况下，该向导提供一个名为**上游**的路由，并将其定义为**从/messages/\* 到 $upstream**中，这意味着任何模块输出的所有消息都将发送到 IoT 中心。  

在路由中添加或更新[声明路由](module-composition.md#declare-routes)中的信息，再选择“下一步”继续转到评审部分。

选择 "**下一步：度量值**"。

### <a name="step-4-metrics"></a>步骤4：度量值

指标提供应用配置内容后设备可能报告的各种状态的摘要计数。

1. 在“指标名称”中输入名称。

1. 在“指标条件”中输入查询。 该查询基于 IoT Edge 中心模块孪生[报告的属性](module-edgeagent-edgehub.md#edgehub-reported-properties)。 指标表示查询返回的行数。

   例如：

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

选择 "**下一步：目标设备**"。

### <a name="step-5-target-devices"></a>步骤5：目标设备

使用设备中的标记属性将应接收此部署的特定设备定为目标。

因为多个部署可能将同一个设备定为目标，所以应为每个部署提供优先级编号。 如果发生冲突，则具有最高优先级的部署（值越大，表示优先级越高）入选。 如果两个部署的优先级编号相同，则将最新创建的部署定为目标。

如果多个部署面向同一设备，则仅应用优先级较高的部署。 如果多个分层部署以相同的设备为目标，则会应用它们。 但是，如果存在任何重复的属性（例如，如果有两个具有相同名称的路由），则来自较高优先级分层部署的将覆盖其余的路由。

面向设备的任何分层部署的优先级必须高于基本部署，才能应用。

1. 为部署优先级输入一个正整数。
1. 输入“目标条件”确定将作为此部署的目标的设备。 此条件基于设备克隆标记或设备克隆报告的属性，并且应与表达式格式匹配。 例如，`tags.environment='test'` 或 `properties.reported.devicemodel='4000x'`。

选择 "**下一步"：查看 + 创建**以转到最后一步。

### <a name="step-6-review-and-create"></a>步骤6：查看和创建

查看部署信息，然后选择 "**创建**"。

## <a name="monitor-a-deployment"></a>监视部署

若要查看部署的详细信息并监视运行它的设备，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)并导航到 IoT 中心。
1. 选择“IoT Edge”。
1. 选择 " **IoT Edge 部署**" 选项卡。

   ![查看 IoT Edge 部署](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. 检查部署列表。 对于每个部署，可以查看以下详细信息：
   * ID - 部署的名称。
   * **类型** **-部署的**类型，部署或**分层部署**。
   * **目标条件**-用于定义目标设备的标记。
   * 优先级 - 分配到部署的优先级编号。
   * **系统指标** - “已定目标”指定 IoT 中心内与目标条件匹配的设备孪生数量，“已应用”指定在 IoT 中心对模块孪生应用部署内容的设备数量。
   * **设备指标**-部署中报告成功或错误的 IoT Edge 设备数 IoT Edge 客户端运行时。
   * **自定义指标**-部署报告中为部署定义的任何指标的 IoT Edge 设备数。
   * **创建时间**-创建部署时的时间戳。 两个部署具有相同优先级，此时间戳用于消除它们的关系。
1. 选择想要监视的部署。  
1. 检查部署详细信息。 可以使用选项卡查看部署的详细信息。

## <a name="modify-a-deployment"></a>修改部署

修改部署时，更改会立即复制到所有目标设备。

如果更新目标条件，将发生以下更新：

* 如果设备虽然不满足旧的目标条件，但满足新的目标条件，且此部署是此设备的最高优先级，则此部署将应用到设备。
* 如果当前运行此部署的设备不再满足目标条件，则它将卸载此部署并采用下一个最高优先级部署。
* 如果当前运行此部署的设备不再满足目标条件且不满足任何其他部署的目标条件，则此设备上不会发生任何更改。 设备在当前状态下继续运行当前模块，但不再作为此部署的一部分被托管。 一旦它满足任何其他部署的目标条件，将卸载此部署并采用新的部署。

若要修改部署，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)并导航到 IoT 中心。
1. 选择“IoT Edge”。
1. 选择 " **IoT Edge 部署**" 选项卡。

   ![查看 IoT Edge 部署](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. 选择想要修改的部署。
1. 在以下选项卡上进行更新：
   * **目标条件**
   * **指标**-您可以修改或删除您定义的度量值，也可以添加新的度量值。
   * **标签**
   * **模块**
   * **路由**
   * **部署**

1. 选择“保存”。
1. 执行[监视部署](#monitor-a-deployment)中的步骤，注意更改的推出。

## <a name="delete-a-deployment"></a>删除部署

当你删除某个部署时，任何部署的设备都会采用其下一个优先级最高的部署。 如果设备不满足任何其他部署的目标条件，则删除该部署时不会删除模块。

1. 登录 [Azure 门户](https://portal.azure.com)并导航到 IoT 中心。
1. 选择“IoT Edge”。
1. 选择 " **IoT Edge 部署**" 选项卡。

   ![查看 IoT Edge 部署](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. 使用复选框选择想要删除的部署。
1. 选择“删除”。
1. 提示将发出以下通知：此操作将删除此部署并还原为所有设备之前的状态。  这意味着将应用优先级较低的部署。 如果没有其他部署的目标，则不会删除任何模块。 若要从设备中删除所有模块，请创建零模块部署，并将它部署到相同设备。 选择 **"是"** 以继续。

## <a name="next-steps"></a>后续步骤

了解有关[将模块部署到 IoT Edge 设备](module-deployment-monitoring.md)的详细信息。
