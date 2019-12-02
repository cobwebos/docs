---
title: 大规模部署模块 Azure 门户-Azure IoT Edge
description: 使用 Azure 门户为 IoT Edge 设备组创建自动部署
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 068845bf8cda7ce6abf11eefad0ed176688b34c5
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665842"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>使用 Azure 门户大规模部署和监视 IoT Edge 模块

在 Azure 门户中创建**IoT Edge 自动部署**，以便一次管理多个设备的正在进行的部署。 IoT Edge 的自动部署是 IoT 中心[自动设备管理](/azure/iot-hub/iot-hub-automatic-device-management)功能的一部分。 部署是动态过程，使你能够将多个模块部署到多个设备、跟踪模块的状态和运行状况，并在必要时进行更改。 

有关详细信息，请参阅[了解 IoT Edge 单个设备的自动部署或大规模部署](module-deployment-monitoring.md)。

## <a name="identify-devices-using-tags"></a>使用标记标识设备

创建部署之前，必须能够指定想要影响的设备。 Azure IoT Edge 标识使用设备孪生中的标记标识设备。 每个设备都可以有多个标记，您可以使用这些标记来定义解决方案。 例如，如果管理有智能楼宇的校园，可能要将以下标记添加到设备：

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

1. 在 [Azure 门户](https://portal.azure.com)中，转到 IoT 中心。 
1. 选择“IoT Edge”。
1. 选择“添加 IoT Edge 部署”。

创建部署需要五个步骤。 下列各节将引导完成每个步骤。 

### <a name="step-1-name-and-label"></a>步骤 1：名称和标签

1. 为部署命名唯一名称（最多包含 128 个小写字母）。 避免空格和以下无效字符：`& ^ [ ] { } \ | " < > /`。
1. 可以将标签作为键/值对添加，以帮助跟踪部署。 例如，**HostPlatform** 和 **Linux**，或 **Version** 和 **3.0.1**。
1. 选择“下一步”，进入到步骤 2。 

### <a name="step-2-add-modules-optional"></a>步骤 2：添加模块（可选）

最多可以将20个模块添加到部署。 

如果创建的部署不包含任何模块，则会从目标设备中删除所有当前模块。 

要添加 Azure 流分析中的模块，请执行以下步骤：

1. 在页面的“部署模块”部分中，单击“添加”。
1. 选择“Azure 流分析模块”。
1. 选择下拉菜单中的“订阅”。
1. 从下拉菜单中选择 "IoT **Edge 作业**"。
1. 选择“保存”，将模块添加到部署。 

若要将自定义代码添加为模块，或手动添加 Azure 服务模块，请执行以下步骤：

1. 在页面的“容器注册表设置”部分中，为包含此部署模块映像的任何专用容器注册表提供名称和凭据。 如果 IoT Edge 代理找不到 Docker 映像的容器注册表凭据，则会报告错误500。
1. 在页面的“部署模块”部分中，单击“添加”。
1. 选择“IoT Edge 模块”。
1. 为模块提供一个名称。
1. 对于“映像 URI”字段，输入模块的容器映像。 
1. 指定应传递到容器的任意容器创建选项。 有关详细信息，请参阅 [docker create](https://docs.docker.com/engine/reference/commandline/create/)。
1. 使用下拉菜单选择“重启策略”。 从以下选项中选择： 
   * 始终 - 如果模块因任何原因关闭，该模块将始终重启。
   * **从不**-如果模块因任何原因而关闭，则不会重新启动。
   * **故障**-如果模块发生故障，则会重新启动，但不会重新启动。 
   * **在运行不正常**时-如果模块发生故障或者返回不正常状态，则会重新启动。 这取决于每个执行运行状况监控功能的模块。 
1. 使用下拉菜单选择模块的所需状态。 从以下选项中选择：
   * **运行**的是默认选项。 该模块在部署之后将立即开始运行。
   * **已停止**-在部署后，模块将保持空闲状态，直到你或另一个模块开始调用。
1. 如果想要将标记或其他属性添加到模块孪生中，请选择“设置模块孪生的所需属性”。
1. 输入此模块的“环境变量”。 环境变量向模块提供配置信息。
1. 选择“保存”，将模块添加到部署。 

配置完部署的所有模块之后，选择“下一步”，进入到步骤 3。

### <a name="step-3-specify-routes-optional"></a>步骤 3：指定路由（可选）

路由定义模块在部署中如何相互通信。 向导默认提供名为“route”且定义为FROM /\* INTO $upstream 的路由，这表示任何模块输出的任何消息都将发送到 IoT 中心。  

在路由中添加或更新[声明路由](module-composition.md#declare-routes)中的信息，再选择“下一步”继续转到评审部分。

### <a name="step-4-specify-metrics-optional"></a>步骤4：指定指标（可选）

指标提供应用配置内容后设备可能报告的各种状态的摘要计数。

1. 在“指标名称”中输入名称。

1. 在“指标条件”中输入查询。 该查询基于 IoT Edge 中心模块孪生[报告的属性](module-edgeagent-edgehub.md#edgehub-reported-properties)。 指标表示查询返回的行数。

   例如：

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

### <a name="step-5-target-devices"></a>步骤5：目标设备

使用设备中的标记属性将应接收此部署的特定设备定为目标。 

因为多个部署可能将同一个设备定为目标，所以应为每个部署提供优先级编号。 如果发生冲突，则具有最高优先级的部署（值越大，表示优先级越高）入选。 如果两个部署的优先级编号相同，则将最新创建的部署定为目标。 

1. 为部署优先级输入一个正整数。
1. 输入“目标条件”确定将作为此部署的目标的设备。 此条件基于设备克隆标记或设备克隆报告的属性，并且应与表达式格式匹配。 例如，`tags.environment='test'` 或 `properties.reported.devicemodel='4000x'`。 
1. 选择“下一步”，进入到最后一步。

### <a name="step-6-review-deployment"></a>步骤6：查看部署

审阅部署信息，然后选择“提交”。

## <a name="deploy-modules-from-azure-marketplace"></a>从 Azure 市场部署模块

Azure 市场是一个应用程序和服务在线市场，可在其中浏览各种企业应用程序和解决方案，这些应用程序和解决方案针对在 Azure 上运行进行了认证和优化，包括 [IoT Edge 模块](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)。 还可以在“创建资源”下通过 Azure 门户访问 Azure 市场。

你可以从 Azure Marketplace 或 Azure 门户部署 IoT Edge 模块：

1. 查找模块并开始部署过程。

   * Azure 门户：查找模块并选择 "**创建**"。

   * Azure 市场：

     1. 查找模块，然后选择“立即获取”。
     1. 通过选择“继续”，承认提供商的使用条款和隐私政策。

1. 选择你的订阅和要将目标设备附加到的 IoT 中心。

1. 选择“大规模部署”。

1. 选择是将模块添加到新的部署还是克隆现有部署；如果选择克隆，从列表中选择现有部署。

1. 选择“创建”以继续创建大规模部署的过程。 你将能够指定与任何部署一样的详细信息。

## <a name="monitor-a-deployment"></a>监视部署

若要查看部署的详细信息并监视运行它的设备，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。 
1. 选择“IoT Edge”。
1. 选择“IoT Edge 部署”。 

   ![查看 IoT Edge 部署](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. 检查部署列表。 对于每个部署，可以查看以下详细信息：
   * ID - 部署的名称。
   * 目标条件 - 用于定义目标设备的标记。
   * 优先级 - 分配到部署的优先级编号。
   * **系统指标** - “已定目标”指定 IoT 中心内与目标条件匹配的设备孪生数量，“已应用”指定在 IoT 中心对模块孪生应用部署内容的设备数量。 
   * **设备指标**-部署中报告成功或错误的 IoT Edge 设备数 IoT Edge 客户端运行时。
   * **自定义指标**-部署报告中为部署定义的任何指标的 IoT Edge 设备数。
   * 创建时间 - 开始创建部署的时间戳。 两个部署具有相同优先级，此时间戳用于消除它们的关系。 
1. 选择想要监视的部署。  
1. 检查部署详细信息。 可以使用选项卡查看部署的详细信息。

## <a name="modify-a-deployment"></a>修改部署

修改部署时，更改会立即复制到所有目标设备。 

如果更新目标条件，将发生以下更新：

* 如果设备虽然不满足旧的目标条件，但满足新的目标条件，且此部署是此设备的最高优先级，则此部署将应用到设备。 
* 如果当前运行此部署的设备不再满足目标条件，则它将卸载此部署并采用下一个最高优先级部署。 
* 如果当前运行此部署的设备不再满足目标条件且不满足任何其他部署的目标条件，则此设备上不会发生任何更改。 设备在当前状态下继续运行当前模块，但不再作为此部署的一部分被托管。 一旦它满足任何其他部署的目标条件，将卸载此部署并采用新的部署。 

若要修改部署，请执行以下步骤： 

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。 
1. 选择“IoT Edge”。
1. 选择“IoT Edge 部署”。 

   ![查看 IoT Edge 部署](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. 选择想要修改的部署。 
1. 更新以下字段： 
   * 目标条件
   * 指标 - 可以修改或删除定义的指标，或添加新的指标。
   * 标签
   * 优先级
1. 选择“保存”。
1. 执行[监视部署](#monitor-a-deployment)中的步骤，注意更改的推出。 

## <a name="delete-a-deployment"></a>删除部署

删除部署时，任何设备都将采用下一个最高优先级部署。 如果设备不满足任何其他部署的目标条件，则删除该部署时不会删除模块。 

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。 
1. 选择“IoT Edge”。
1. 选择“IoT Edge 部署”。 

   ![查看 IoT Edge 部署](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. 使用复选框选择想要删除的部署。 
1. 选择“删除”。
1. 提示将发出以下通知：此操作将删除此部署并还原为所有设备之前的状态。  这意味着将应用优先级较低的部署。  如果没有其他部署的目标，则不会删除任何模块。 若要从设备中删除所有模块，请创建零模块部署，并将它部署到相同设备。 选择 **"是"** 以继续。 

## <a name="next-steps"></a>后续步骤

了解有关[将模块部署到 IoT Edge 设备](module-deployment-monitoring.md)的详细信息。
