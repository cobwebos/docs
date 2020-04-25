---
title: 在 Azure 门户中大规模部署模块 - Azure IoT Edge
description: 使用 Azure 门户为 IoT Edge 设备组创建自动部署
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/21/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e55d3f704c76d2783c3e442a90c829448129a4d0
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82134411"
---
# <a name="deploy-iot-edge-modules-at-scale-using-the-azure-portal"></a>使用 Azure 门户大规模部署 IoT Edge 模块

在 Azure 门户中创建“IoT Edge 自动部署”****，以便同时管理多个设备的正在进行的部署。 IoT Edge 的自动部署属于 IoT 中心的[自动设备管理](/azure/iot-hub/iot-hub-automatic-device-management)功能。 部署是动态的过程，允许将多个模块部署到多台设备，跟踪这些模块的状态和运行状况，以及在必要时进行更改。

有关详细信息，请参阅[了解单个设备或大规模的 IoT Edge 自动部署](module-deployment-monitoring.md)。

## <a name="identify-devices-using-tags"></a>使用标记标识设备

创建部署之前，必须能够指定想要影响的设备。 Azure IoT Edge 标识使用设备孪生中的标记**** 标识设备。 每个设备都可以具有多个标记，你可以采用适合你的解决方案的任何方式定义这些标记。

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

IoT Edge 提供两种不同类型的自动部署，可用于自定义你的方案。 可创建一个标准部署，其中包括系统运行时模块和任何其他模块和路由**。 每个设备只能应用一个部署。 也可创建一个分层部署，使其仅包含自定义模块和路由，而不包含系统运行时**。 在标准部署的基础上，可以在设备上组合许多分层部署。 有关这两种类型的自动部署如何协同工作的详细信息，请参阅[了解单设备的 IoT Edge 自动部署或大规模部署](module-deployment-monitoring.md)。

创建部署和创建分层部署的步骤非常相似。 任何差异都会在以下步骤中显示出来。

1. 在[Azure 门户](https://portal.azure.com)中转到 IoT 中心。
1. 在左窗格的菜单上，选择“自动设备管理”**** 下的“IoT Edge”****。
1. 在上面的栏中，选择“创建部署”或“创建分层部署”********。

创建部署需要五个步骤。 下列各节将引导完成每个步骤。

### <a name="step-1-name-and-label"></a>步骤1：名称和标签

1. 为部署提供唯一名称（最多包含 128 个小写字母）。 避免空格和以下无效字符：`& ^ [ ] { } \ | " < > /`。
1. 可以将标签作为键/值对添加，以帮助跟踪部署。 例如，**HostPlatform** 和 **Linux**，或 **Version** 和 **3.0.1**。
1. 选择 "**下一步：** 要移动到步骤2的模块"。

### <a name="step-2-modules"></a>步骤2：模块

最多可以向一个部署添加 20 个模块。 如果创建不含模块的部署，就会从目标设备中删除任何当前模块。

在部署中，你可以管理 IoT Edge 代理和 IoT Edge 中心模块的设置。 选择“运行时设置”可配置两个运行时模块****。 在分层部署中，不包括运行时模块，因此无法对其进行配置。

可以添加三种类型的模块：

* IoT Edge 模块
* 市场模块
* Azure 流分析模块

#### <a name="add-an-iot-edge-module"></a>添加 IoT Edge 模块

若要将自定义代码添加为模块，或手动添加 Azure 服务模块，请执行以下步骤：

1. 在页面的“容器注册表凭据”部分中，为包含此部署模块映像的任何专用容器注册表提供名称和凭据****。 如果找不到 Docker 映像的容器注册表凭据，IoT Edge 代理会报告错误 500。
1. 在页面的“IoT Edge 模块”部分中，单击“添加”********。
1. 从下拉菜单中选择“IoT Edge 模块”****。
1. 为模块指定“IoT Edge 模块名称”****。
1. 对于“映像 URI”**** 字段，输入模块的容器映像。
1. 使用下拉菜单选择“重启策略”****。 从以下选项中选择：
   * **始终**-如果模块因任何原因而关闭，则始终会重新启动。
   * **从不**-如果模块因任何原因而关闭，则不会重新启动。
   * **运行故障时** - 如果模块发生故障，它将重启，但如果完全关闭，则不重启。
   * **在运行不正常**时-如果模块发生故障或者返回不正常状态，则会重新启动。 这取决于每个执行运行状况监控功能的模块。
1. 使用下拉菜单选择模块的所需状态****。 从以下选项中选择：
   * **正在运行** -“正在运行”是默认选项。 该模块在部署之后将立即开始运行。
   * **已停止**-在部署后，模块将保持空闲状态，直到你或另一个模块开始调用。
1. 指定应传递到容器的任意容器创建选项****。 有关详细信息，请参阅 [docker create](https://docs.docker.com/engine/reference/commandline/create/)。
1. 如果要向模块孪生添加标记或其他属性，请选择“设置模块孪生的所需属性”****。
1. 输入此模块的“环境变量”****。 环境变量为模块提供配置信息。
1. 选择“添加”，将模块添加到部署中****。

#### <a name="add-a-module-from-the-marketplace"></a>从市场添加模块

若要从 Azure 市场添加模块，请执行以下步骤：

1. 在页面的“IoT Edge 模块”部分中，单击“添加”********。
1. 从下拉菜单中选择“市场模块”****。
1. 从“IoT Edge 模块市场”页面选择一个模块****。 系统会自动为你的订阅、资源组和设备配置所选的模块。 然后，所选模块将显示在 IoT Edge 模块列表中。 某些模块可能需要其他配置。 有关详细信息，请参阅[从 Azure Marketplace 部署模块](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace)。

#### <a name="add-a-stream-analytics-module"></a>添加流分析模块

要添加 Azure 流分析中的模块，请执行以下步骤：

1. 在页面的“IoT Edge 模块”部分中，单击“添加”********。
1. 从下拉菜单中选择“Azure 流分析模块”****。
1. 在右窗格中，选择你的**订阅**。
1. 选择你的 IoT Edge 作业****。
1. 选择“保存”****，将模块添加到部署。

#### <a name="configure-module-settings"></a>配置模块设置

将模块添加到部署后，选择模块名称，打开“更新 IoT Edge 模块”页面****。 可在此页面编辑模块设置、环境变量、创建选项和模块孪生。 如果你从市场添加了模块，则该模块可能已经填充了其中的一些参数。

如果要创建分层部署，则可能需要配置针对相同设备的其他部署中的模块。 若要在不覆盖其他版本的情况下更新模块内键，请打开 "**模块克隆设置**" 选项卡。为模块克隆的所需属性中的子节创建新的模块`properties.desired.settings`克隆**属性**（例如）。 如果只在 `properties.desired` 字段中定义属性，它将覆盖在任何较低优先级部署中定义的模块的所需属性。

![为分层部署设置模块孪生属性](./media/how-to-deploy-monitor/module-twin-property.png)

如需深入了解分层部署中的模块孪生配置信息，请参阅[分层部署](module-deployment-monitoring.md#layered-deployment)。

配置完部署的所有模块后，选择 "**下一步：路由**" 转到步骤3。

### <a name="step-3-routes"></a>步骤3：路由

路由定义模块在部署中如何相互通信。 默认情况下，向导会提供名为“上游”的路由，并将其定义为“从 /messages/\* 到 $upstream”，这意味着任何模块输出的任何消息都将发送到 IoT 中心********。  

在路由中添加或更新[声明路由](module-composition.md#declare-routes)中的信息，再选择“下一步”**** 继续转到评审部分。

选择 "**下一步：度量值**"。

### <a name="step-4-metrics"></a>步骤4：度量值

指标提供应用配置内容后设备可能报告的各种状态的摘要计数。

1. 在“指标名称”中输入名称****。

1. 在“指标条件”中输入查询。**** 该查询基于 IoT Edge 中心模块孪生[报告的属性](module-edgeagent-edgehub.md#edgehub-reported-properties)。 指标表示查询返回的行数。

   例如：

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

选择 "**下一步：目标设备**"。

### <a name="step-5-target-devices"></a>步骤5：目标设备

使用设备中的标记属性将应接收此部署的特定设备定为目标。

因为多个部署可能将同一个设备定为目标，所以应为每个部署提供优先级编号。 若有冲突，优先级最高（值越大表示优先级越高）的部署胜出。 如果两个部署的优先级编号相同，则将最新创建的部署定为目标。

如果多个部署面向同一设备，则仅应用优先级较高的部署。 如果多个分层部署将同一设备定为目标，则应用全部部署。 但如果任何属性都是重复的，例如有两个同名的路由，则来自高优先级分层部署的路由将覆盖其余路由。

任何将设备定为目标的分层部署都必须具有比基本部署更高的优先级才能被应用。

1. 为部署优先级**** 输入一个正整数。
1. 输入“目标条件”**** 确定将作为此部署的目标的设备。该条件基于设备孪生标记或设备孪生报告的属性，应与表达式格式相匹配。例如，`tags.environment='test'` 或 `properties.reported.devicemodel='4000x'`。

选择 "**下一步"：查看 + 创建**以转到最后一步。

### <a name="step-6-review-and-create"></a>步骤6：查看和创建

查看部署信息，然后选择“创建”****。

若要监视部署，请参阅[监视 IoT Edge 部署](how-to-monitor-iot-edge-deployments.md)。

## <a name="modify-a-deployment"></a>修改部署

修改部署时，更改会立即复制到所有目标设备。 您可以修改现有部署的以下设置和功能：

* 目标条件
* 自定义指标
* 标签
* 标记
* 所需的属性

### <a name="modify-target-conditions-custom-metrics-and-labels"></a>修改目标条件、自定义指标和标签

1. 在 IoT 中心中，从左窗格菜单中选择 " **IoT Edge** "。
1. 选择 " **IoT Edge 部署**" 选项卡，然后选择要配置的部署。
1. 选择 "**目标条件**" 选项卡。更改**目标条件**以面向目标设备。 你还可以调整**优先级**。  选择“保存”。 

    如果更新目标条件，将发生以下更新：

    * 如果设备虽然不满足旧的目标条件，但满足新的目标条件，且此部署是此设备的最高优先级，则此部署将应用到设备。
    * 如果当前运行此部署的设备不再满足目标条件，则它将卸载此部署并采用下一个最高优先级部署。
    * 如果当前运行此部署的设备不再满足目标条件且不满足任何其他部署的目标条件，则此设备上不会发生任何更改。 设备在当前状态下继续运行当前模块，但不再作为此部署的一部分被托管。 一旦它满足任何其他部署的目标条件，将卸载此部署并采用新的部署。

1. 选择 "**指标**" 选项卡，然后单击 "**编辑指标**" 按钮。 使用示例语法作为指南，添加或修改自定义度量值。 选择“保存”。 

    ![在部署中编辑自定义指标](./media/how-to-deploy-monitor/metric-list.png)

1. 选择 "**标签**" 选项卡并进行任何所需的更改，然后选择 "**保存**"。

## <a name="delete-a-deployment"></a>删除部署

删除部署时，任何已部署的设备都将执行其下一个具有最高优先级的部署。 如果设备不满足任何其他部署的目标条件，则删除该部署时不会删除模块。

1. 登录 [Azure 门户](https://portal.azure.com)，并导航到 IoT 中心。
1. 选择“IoT Edge”****。
1. 选择“IoT Edge 部署”选项卡****。

   ![查看 IoT Edge 部署](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. 使用复选框选择想要删除的部署。
1. 选择“删除”。 
1. 提示将发出以下通知：此操作将删除此部署并还原为所有设备之前的状态。将应用优先级较低的部署。如果没有将其他任何部署定为目标，则不会删除任何模块。 若要从设备中删除所有模块，请创建零模块部署，并将它部署到相同设备。选择“是”继续。****

## <a name="next-steps"></a>后续步骤

详细了解[将模块部署到 IoT Edge 设备](module-deployment-monitoring.md)。
