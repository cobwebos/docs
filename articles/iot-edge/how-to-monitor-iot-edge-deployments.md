---
title: 监视 IoT Edge 部署-Azure IoT Edge
description: 高级监视，包括 edgeHub 和 edgeAgent 报告的属性和自动部署指标。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cc7c1fd1dff85908c96e2fd7b2276df3d833e37f
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82134307"
---
# <a name="monitor-iot-edge-deployments"></a>监视 IoT Edge 部署

Azure IoT Edge 提供了报表，可用于监视部署到 IoT Edge 设备的模块的实时信息。 IoT 中心服务从设备检索状态，并使它们可供操作员使用。 对于[大规模的部署](module-deployment-monitoring.md)（包括自动部署和分层部署），监视也很重要。

设备和模块都具有相似的数据，例如连接性，因此根据设备 ID 或模块 ID 获取值。

IoT 中心服务收集设备和模块孪生报告的数据，并提供设备可能具有的各种状态的计数。 IoT 中心服务将此数据组织成四组度量值：

| 类型 | 说明 |
| --- | ---|
| 目标 | 显示与部署目标条件相匹配 IoT Edge 设备。 |
| 已应用 | 显示目标 IoT Edge 设备，这些设备不是以更高优先级的另一个部署为目标的设备。 |
| 报告成功 | 显示已报告这些模块已成功部署 IoT Edge 设备。 |
| 报告失败 | 显示已报告一个或多个模块未成功部署的 IoT Edge 设备。 若要进一步调查此错误，请通过远程方式连接到这些设备并查看日志文件。 |

IoT 中心服务使此数据可供你在 Azure 门户和 Azure CLI 中监视。

## <a name="monitor-a-deployment-in-the-azure-portal"></a>监视 Azure 门户中的部署

若要查看部署的详细信息并监视运行它的设备，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)，并导航到 IoT 中心。
1. 从左窗格菜单中选择 " **IoT Edge** "。
1. 选择 " **IoT Edge 部署**" 选项卡。
1. 检查部署列表。对于每个部署，可以查看以下详细信息：

    | 列 | 说明 |
    | --- | --- |
    | ID | 部署的名称。 |
    | 类型 | **部署类型，部署或****分层部署**。 |
    | 目标条件 | 用于定义目标设备的标记。 |
    | Priority | 分配给部署的优先级编号。 |
    | 系统指标 | IoT 中心的设备孪生数与目标条件匹配。 已**应用**：指定已将部署内容应用到 IoT 中心的模块孪生的设备数。 |
    | 设备指标 | IoT Edge 客户端运行时报告成功或错误的 IoT Edge 设备数。 |
    | 自定义指标 | 为部署定义的任何指标报告数据的 IoT Edge 设备数。 |
    | 创建时间 | 创建部署时的时间戳。 两个部署具有相同优先级，此时间戳用于消除它们的关系。 |

1. 选择想要监视的部署。  
1. 在 "**部署详细信息**" 页上，向下滚动到底部，然后选择 "**目标条件**" 选项卡。选择 "**查看**" 以列出符合目标条件的设备。 你可以更改条件和**优先级**。 如果进行了更改，请选择 "**保存**"。

   ![查看部署的目标设备](./media/how-to-monitor-iot-edge-deployments/target-devices.png)

1. 选择 "**指标**" 选项卡。如果从 "**选择指标**" 下拉对话框中选择一个度量值，则会显示 "**查看**" 按钮以显示结果。 还可以选择 "**编辑度量值**" 来调整定义的任何自定义指标的条件。 如果进行了更改，请选择 "**保存**"。

   ![查看部署的指标](./media/how-to-monitor-iot-edge-deployments/deployment-metrics-tab.png)


若要更改部署，请参阅[修改部署](how-to-deploy-at-scale.md#modify-a-deployment)。

## <a name="monitor-a-deployment-with-azure-cli"></a>使用 Azure CLI 监视部署

使用[az IoT Edge deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show)命令显示单个部署的详细信息：

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

部署显示命令采用以下参数：

* **--deployment-id** - IoT 中心存在的部署的名称。 必需参数。
* **--hub-name** -部署所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅

在命令窗口中检查部署。**metrics** 属性列出由每个中心评估的每个指标的计数：

* **targetedCount** - 一个系统指标，根据目标条件指定 IoT 中心的设备孪生数。
* **appliedCount** - 一个系统指标，指定已在 IoT 中心将部署内容应用到其模块孪生的设备数。
* **reportedSuccessfulCount** - 一个设备指标，用于指定通过 IoT Edge 客户端运行时报告成功的部署中的 IoT Edge 设备数。
* **reportedFailedCount** - 一个设备指标，用于指定通过 IoT Edge 客户端运行时报告失败的部署中的 IoT Edge 设备数。

您可以使用[az IoT Edge deployment show-公制](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric)命令显示每个指标的设备 id 或对象的列表：

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

部署显示指标命令采用以下参数：

* **--deployment-id** - IoT 中心存在的部署的名称。
* **--公制**-要查看其设备 id 列表的指标的名称，例如`reportedFailedCount`。
* **--hub-name** -部署所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅。

若要更改部署，请参阅[修改部署](how-to-deploy-cli-at-scale.md#modify-a-deployment)。

## <a name="next-steps"></a>后续步骤

了解如何[使用内置直接方法与 EdgeAgent 通信](how-to-edgeagent-direct-method.md)。
