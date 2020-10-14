---
title: 在 Azure 门户中大规模部署模块 - Azure IoT Edge
description: 使用 Azure 门户为 IoT Edge 设备组创建自动部署
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9d03b6f4a512c22564480405ec0f0e0c0e62a958
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048417"
---
# <a name="deploy-iot-edge-modules-at-scale-using-the-azure-portal"></a>使用 Azure 门户大规模部署 IoT Edge 模块

在 Azure 门户中创建“IoT Edge 自动部署”，以便同时管理多个设备的正在进行的部署。 IoT Edge 的自动部署属于 IoT 中心的[自动设备管理](../iot-hub/iot-hub-automatic-device-management.md)功能。 部署是动态的过程，允许将多个模块部署到多台设备，跟踪这些模块的状态和运行状况，以及在必要时进行更改。

有关详细信息，请参阅[了解单个设备或大规模的 IoT Edge 自动部署](module-deployment-monitoring.md)。

## <a name="identify-devices-using-tags"></a>使用标记标识设备

创建部署之前，必须能够指定想要影响的设备。 Azure IoT Edge 标识使用设备孪生中的标记标识设备。 每个设备都可以具有多个标记，你可以采用适合你的解决方案的任何方式定义这些标记。

例如，如果管理有智能楼宇的校园，可以将位置、房间类型和环境标记添加到设备：

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

IoT Edge 提供两种不同类型的自动部署，可用于自定义你的方案。 可创建一个标准部署，其中包括系统运行时模块和任何其他模块和路由。 每个设备只能应用一个部署。 也可创建一个分层部署，使其仅包含自定义模块和路由，而不包含系统运行时。 在标准部署的基础上，可以在设备上组合许多分层部署。 有关这两种类型的自动部署如何协同工作的详细信息，请参阅[了解单设备的 IoT Edge 自动部署或大规模部署](module-deployment-monitoring.md)。

创建部署和创建分层部署的步骤非常相似。 任何差异都会在以下步骤中显示出来。

1. 在 [Azure 门户](https://portal.azure.com)中，转到 IoT 中心。
1. 在左窗格的菜单上，选择“自动设备管理”下的“IoT Edge”。
1. 在上面的栏中，选择“创建部署”或“创建分层部署” 。

创建部署需要五个步骤。 下列各节将引导完成每个步骤。

>[!NOTE]
>本文中的步骤反映了 IoT Edge 代理和中心的最新架构版本。 架构版本1.1 与 IoT Edge 版本1.0.10 一起发布，并启用模块启动顺序和路由优先级功能。
>
>如果要部署到运行1.0.9 或更早版本的设备，请在向导的 "**模块**" 步骤中编辑**运行时设置**，以使用架构版本1.0。

### <a name="step-1-name-and-label"></a>步骤 1：名称和标签

1. 为部署提供唯一名称（最多包含 128 个小写字母）。 避免空格和以下无效字符：`& ^ [ ] { } \ | " < > /`。
1. 可以将标签作为键/值对添加，以帮助跟踪部署。 例如，**HostPlatform** 和 **Linux**，或 **Version** 和 **3.0.1**。
1. 在完成时选择“下一步:模块”，进入到步骤 2。

### <a name="step-2-modules"></a>步骤 2：模块

最多可以向一个部署添加 50 个模块。 如果创建不含模块的部署，就会从目标设备中删除任何当前模块。

在部署中，你可以管理 IoT Edge 代理和 IoT Edge 中心模块的设置。 选择“运行时设置”可配置两个运行时模块。 在分层部署中，不包括运行时模块，因此无法对其进行配置。

若要将自定义代码添加为模块，或手动添加 Azure 服务模块，请执行以下步骤：

1. 在此页的“容器注册表设置”部分，提供用于访问包含模块映像的任何专用容器注册表的凭据。
1. 在此页的“IoT Edge 模块”部分，选择“添加” 。
1. 从下拉菜单中选择三种类型的模块之一：

   * **IoT Edge 模块** - 提供模块名称和容器映像 URI。 例如，示例 SimulatedTemperatureSensor 模块的映像 URI 为 `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`。 如果模块映像存储在专用容器注册表中，则在此页面上添加凭据来访问该映像。
   * **市场模块** - Azure 市场中托管的模块。 某些市场模块需要其他配置，因此请查看 [Azure 市场 IoT Edge 模块](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)列表中的模块详细信息。
   * **Azure 流分析模块** - 通过 Azure 流分析工作负载生成的模块。

1. 如果需要，请重复步骤2和3，将其他模块添加到部署。

将模块添加到部署后，选择模块名称，打开“更新 IoT Edge 模块”页面。 在此页上，您可以编辑 "模块设置"、"环境变量"、"创建选项"、"启动顺序" 和 "模块克隆"。 如果你从市场添加了模块，则该模块可能已经填充了其中的一些参数。 有关可用模块设置的详细信息，请参阅 [模块配置和管理](module-composition.md#module-configuration-and-management)。

如果要创建分层部署，则可能需要配置针对相同设备的其他部署中的模块。 若要更新模块孪生但不覆盖其他版本，请打开“模块孪生设置”选项卡。创建新的“模块孪生属性”，使其具有模块孪生所需属性中某个子节的唯一名称，例如 `properties.desired.settings`。 如果只在 `properties.desired` 字段中定义属性，它将覆盖在任何较低优先级部署中定义的模块的所需属性。

![为分层部署设置模块孪生属性](./media/how-to-deploy-monitor/module-twin-property.png)

如需深入了解分层部署中的模块孪生配置信息，请参阅[分层部署](module-deployment-monitoring.md#layered-deployment)。

配置部署的所有模块后，选择“下一步: 路由”，移动到步骤 3。

### <a name="step-3-routes"></a>步骤 3：路由

在“路由”选项卡中，定义消息在模块和 IoT 中心之间传递的方式。 使用名称/值对构造消息。

例如，具有名称 **路由** 的路由和 **从/messages/ \* 到 $upstream** 的值将使用任何模块输出的所有消息并将其发送到 IoT 中心。  

**优先级**和**生存时间**参数是可在路由定义中包含的可选参数。 Priority 参数允许你选择应该首先处理其消息的路由，或最后处理哪些路由。 优先级是通过设置数字0-9 来确定的，其中0是最高优先级。 通过生存时间参数，您可以声明该路由中的消息在被处理或从队列中删除之前应保留多长时间。

有关如何创建路由的详细信息，请参阅 [声明路由](module-composition.md#declare-routes)。

在完成时选择“下一步: 指标”。

### <a name="step-4-metrics"></a>步骤 4：指标

指标提供应用配置内容后设备可能报告的各种状态的摘要计数。

1. 在“指标名称”中输入名称。

1. 在“指标条件”中输入查询。 该查询基于 IoT Edge 中心模块孪生[报告的属性](module-edgeagent-edgehub.md#edgehub-reported-properties)。 指标表示查询返回的行数。

   例如：

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

在完成时选择“下一步: 目标设备”。

### <a name="step-5-target-devices"></a>步骤 5：目标设备

使用设备中的标记属性将应接收此部署的特定设备定为目标。

因为多个部署可能将同一个设备定为目标，所以应为每个部署提供优先级编号。 若有冲突，优先级最高（值越大表示优先级越高）的部署胜出。 如果两个部署的优先级编号相同，则将最新创建的部署定为目标。

如果多个部署将同一设备定为目标，则只应用优先级较高的部署。 如果多个分层部署将同一设备定为目标，则应用全部部署。 但如果任何属性都是重复的，例如有两个同名的路由，则来自高优先级分层部署的路由将覆盖其余路由。

任何将设备定为目标的分层部署都必须具有比基本部署更高的优先级才能被应用。

1. 为部署优先级输入一个正整数。
1. 输入“目标条件”确定将作为此部署的目标的设备。 该条件基于设备孪生标记或设备孪生报告的属性，应与表达式格式相匹配。 例如 `tags.environment='test'` 或 `properties.reported.devicemodel='4000x'`。

在完成时选择“下一步: 查看 + 创建”，转到最后一步。

### <a name="step-6-review-and-create"></a>步骤 6：查看并创建

查看部署信息，然后选择“创建”。

若要监视部署，请参阅[监视 IoT Edge 部署](how-to-monitor-iot-edge-deployments.md)。

## <a name="modify-a-deployment"></a>修改部署

修改部署时，更改会立即复制到所有目标设备。 可以修改现有部署的以下设置和功能：

* 目标条件
* 自定义指标
* 标签
* Tags
* 所需属性

### <a name="modify-target-conditions-custom-metrics-and-labels"></a>修改目标条件、自定义指标和标签

1. 从 IoT 中心的左窗格菜单中选择“IoT Edge”。
1. 选择“IoT Edge 部署”选项卡，然后选择要配置的部署。
1. 选择“目标条件”选项卡。更改“目标条件”，将预定设备作为目标。 还可以调整“优先级”。  选择“保存” 。

    如果更新目标条件，将发生以下更新：

    * 如果设备虽然不满足旧的目标条件，但满足新的目标条件，且此部署是此设备的最高优先级，则此部署将应用到设备。
    * 如果当前运行此部署的设备不再满足目标条件，则它将卸载此部署并采用下一个最高优先级部署。
    * 如果当前运行此部署的设备不再满足目标条件且不满足任何其他部署的目标条件，则此设备上不会发生任何更改。 设备在当前状态下继续运行当前模块，但不再作为此部署的一部分被托管。 一旦它满足任何其他部署的目标条件，将卸载此部署并采用新的部署。

1. 选择“指标”选项卡，然后单击“编辑指标”按钮。 以示例语法作为指南，添加或修改自定义指标。 选择“保存” 。

    ![在部署中编辑自定义指标](./media/how-to-deploy-monitor/metric-list.png)

1. 选择“标签”选项卡并进行所需的更改，然后选择“保存”。

## <a name="delete-a-deployment"></a>删除部署

删除部署时，任何已部署的设备都将执行其下一个具有最高优先级的部署。 如果设备不满足任何其他部署的目标条件，则删除该部署时不会删除模块。

1. 登录 [Azure 门户](https://portal.azure.com)并导航到 IoT 中心。
1. 选择“IoT Edge”。
1. 选择“IoT Edge 部署”选项卡。

   ![查看 IoT Edge 部署](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. 使用复选框选择想要删除的部署。
1. 选择“删除” 。
1. 提示将发出以下通知：此操作将删除此部署并还原为所有设备之前的状态。 将应用优先级较低的部署。 如果没有将其他任何部署定为目标，则不会删除任何模块。 若要从设备中删除所有模块，请创建零模块部署，并将它部署到相同设备。 选择“是”继续。

## <a name="next-steps"></a>后续步骤

详细了解[将模块部署到 IoT Edge 设备](module-deployment-monitoring.md)。