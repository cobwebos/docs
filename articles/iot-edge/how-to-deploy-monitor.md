---
title: 从 Azure 门户创建自动部署 - Azure IoT Edge | Microsoft Docs
description: 使用 Azure 门户为 IoT Edge 设备组创建自动部署
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 286bab7b7fdbe42190c32dabb42c59d6fc094b2a
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457368"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>使用 Azure 门户大规模部署和监视 IoT Edge 模块

Create an **IoT Edge automatic deployment** in the Azure portal to manage ongoing deployments for many devices at once. Automatic deployments for IoT Edge are part of the [automatic device management](/azure/iot-hub/iot-hub-automatic-device-management) feature of IoT Hub. Deployments are dynamic processes that enable you to deploy multiple modules to multiple devices, track the status and health of the modules, and make changes when necessary. 

For more information, see [Understand IoT Edge automatic deployments for single devices or at scale](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>使用标记标识设备

创建部署之前，必须能够指定想要影响的设备。 Azure IoT Edge 标识使用设备孪生中的标记标识设备。 Each device can have multiple tags that you define in any way that makes sense for your solution. 例如，如果管理有智能楼宇的校园，可能要将以下标记添加到设备：

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

You can add up to 20 modules to a deployment. 

If you create a deployment with no modules, it removes any current modules from the target devices. 

要添加 Azure 流分析中的模块，请执行以下步骤：

1. 在页面的“部署模块”部分中，单击“添加”。
1. 选择“Azure 流分析模块”。
1. 选择下拉菜单中的“订阅”。
1. Choose your IoT **Edge job** from the drop-down menu.
1. 选择“保存”，将模块添加到部署。 

若要将自定义代码添加为模块，或手动添加 Azure 服务模块，请执行以下步骤：

1. 在页面的“容器注册表设置”部分中，为包含此部署模块映像的任何专用容器注册表提供名称和凭据。 The IoT Edge Agent will report error 500 if it can't find the container registry credential for a Docker image.
1. 在页面的“部署模块”部分中，单击“添加”。
1. 选择“IoT Edge 模块”。
1. 为模块提供一个名称。
1. 对于“映像 URI”字段，输入模块的容器映像。 
1. 指定应传递到容器的任意容器创建选项。 有关详细信息，请参阅 [docker create](https://docs.docker.com/engine/reference/commandline/create/)。
1. 使用下拉菜单选择“重启策略”。 从以下选项中选择： 
   * 始终 - 如果模块因任何原因关闭，该模块将始终重启。
   * **never** - The module never restarts if it shuts down for any reason.
   * **on-failure** - The module restarts if it crashes, but not if it shuts down cleanly. 
   * **on-unhealthy** - The module restarts if it crashes or returns an unhealthy status. 这取决于每个执行运行状况监控功能的模块。 
1. 使用下拉菜单选择模块的所需状态。 从以下选项中选择：
   * **running** - Running is the default option. 该模块在部署之后将立即开始运行。
   * **stopped** - After being deployed, the module will remain idle until called upon to start by you or another module.
1. 如果想要将标记或其他属性添加到模块孪生中，请选择“设置模块孪生的所需属性”。
1. 输入此模块的“环境变量”。 Environment variables provide configuration information to a module.
1. 选择“保存”，将模块添加到部署。 

配置完部署的所有模块之后，选择“下一步”，进入到步骤 3。

### <a name="step-3-specify-routes-optional"></a>步骤 3：指定路由（可选）

路由定义模块在部署中如何相互通信。 向导默认提供名为“route”且定义为FROM /\* INTO $upstream 的路由，这表示任何模块输出的任何消息都将发送到 IoT 中心。  

在路由中添加或更新[声明路由](module-composition.md#declare-routes)中的信息，再选择“下一步”继续转到评审部分。

### <a name="step-4-specify-metrics-optional"></a>Step 4: Specify Metrics (optional)

指标提供应用配置内容后设备可能报告的各种状态的摘要计数。

1. 在“指标名称”中输入名称。

1. 在“指标条件”中输入查询。 该查询基于 IoT Edge 中心模块孪生[报告的属性](module-edgeagent-edgehub.md#edgehub-reported-properties)。 指标表示查询返回的行数。

   例如：

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

### <a name="step-5-target-devices"></a>Step 5: Target Devices

使用设备中的标记属性将应接收此部署的特定设备定为目标。 

因为多个部署可能将同一个设备定为目标，所以应为每个部署提供优先级编号。 If there's ever a conflict, the deployment with the highest priority (larger values indicate higher priority) wins. 如果两个部署的优先级编号相同，则将最新创建的部署定为目标。 

1. 为部署优先级输入一个正整数。
1. 输入“目标条件”确定将作为此部署的目标的设备。 The condition is based on device twin tags or device twin reported properties and should match the expression format. For example, `tags.environment='test'` or `properties.reported.devicemodel='4000x'`. 
1. 选择“下一步”，进入到最后一步。

### <a name="step-6-review-deployment"></a>Step 6: Review Deployment

审阅部署信息，然后选择“提交”。

## <a name="deploy-modules-from-azure-marketplace"></a>从 Azure 市场部署模块

Azure 市场是一个应用程序和服务在线市场，可在其中浏览各种企业应用程序和解决方案，这些应用程序和解决方案针对在 Azure 上运行进行了认证和优化，包括 [IoT Edge 模块](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)。 还可以在“创建资源”下通过 Azure 门户访问 Azure 市场。

You can deploy an IoT Edge module from either Azure Marketplace or the Azure portal:

1. 查找模块并开始部署过程。

   * Azure portal: Find a module and select **Create**.

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

1. 检查部署列表。 For each deployment, you can view the following details:
   * ID - 部署的名称。
   * 目标条件 - 用于定义目标设备的标记。
   * 优先级 - 分配到部署的优先级编号。
   * **系统指标** - “已定目标”指定 IoT 中心内与目标条件匹配的设备孪生数量，“已应用”指定在 IoT 中心对模块孪生应用部署内容的设备数量。 
   * **Device metrics** - the number of IoT Edge devices in the deployment reporting success or errors from the IoT Edge client runtime.
   * **Custom metrics** - the number of IoT Edge devices in the deployment reporting data for any metrics that you defined for the deployment.
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
1. 提示将发出以下通知：此操作将删除此部署并还原为所有设备之前的状态。  This means that a deployment with a lower priority will apply.  If no other deployment is targeted, no modules will be removed. 若要从设备中删除所有模块，请创建零模块部署，并将它部署到相同设备。 Select **Yes** to continue. 

## <a name="next-steps"></a>后续步骤

Learn more about [Deploying modules to IoT Edge devices](module-deployment-monitoring.md).
