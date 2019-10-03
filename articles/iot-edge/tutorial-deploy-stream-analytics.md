---
title: 在边缘部署流分析 - Azure IoT Edge | Microsoft Docs
description: 在本教程中，请将 Azure 流分析作为模块部署到 IoT Edge 设备
author: kgremban
ms.author: kgremban
ms.date: 08/07/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 0128574ff0ef9db1c5a4326e3ebce25fbba0c2e7
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845119"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>教程：将 Azure 流分析作为 IoT Edge 模块进行部署

许多 IoT 解决方案使用分析服务来了解从 IoT 设备到达云的数据。 使用 Azure IoT Edge 时，可以获取 [Azure 流分析](https://docs.microsoft.com/azure/stream-analytics/)逻辑，将其转到设备中。 在边缘处理遥测流可以减少上传数据量，缩短对可操作见解进行响应的时间。

Azure IoT Edge 和 Azure 流分析已集成，因此可以在 Azure 门户中创建一项 Azure 流分析作业，然后将其作为 IoT Edge 模块进行部署，不需额外的代码。  

Azure 流分析提供一种丰富结构化的查询语法，可用于在云和 IoT Edge 设备上进行数据分析。 有关详细信息，请参阅 [Azure 流分析文档](../stream-analytics/stream-analytics-edge.md)。

本教程中的流分析模块在一个滚动的 30 秒时段内计算平均温度。 当平均温度达到 70 时，模块会发送一个警报，以便设备采取操作。 在这种情况下，该操作是重置模拟温度传感器。 在生产环境中，当温度达到危险级别时，可以使用此功能关闭机器或采取预防措施。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Edge 上创建 Azure 流分析作业用于处理数据。
> * 将新的 Azure 流分析作业与其他 IoT Edge 模块相连接。
> * 将 Azure 流分析作业从 Azure 门户部署到 IoT Edge 设备。

<center>

![关系图 - 教程体系结构、阶段和部署 ASA 作业](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

Azure IoT Edge 设备：

* 可以按照适用于 [Linux](quickstart-linux.md) 或 [Windows 设备](quickstart.md)的快速入门中的步骤，将 Azure 虚拟机用作 IoT Edge 设备。

云资源：

* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。 


## <a name="create-an-azure-stream-analytics-job"></a>创建 Azure 流分析作业

在本部分，你将创建一个 Azure 流分析作业用于执行以下步骤：
* 从 IoT Edge 设备接收数据。
* 查询遥测数据中超出设置的范围的值。
* 根据查询结果对 IoT Edge 设备采取措施。 

### <a name="create-a-storage-account"></a>创建存储帐户

创建可以在 IoT Edge 设备上运行的 Azure 流分析作业时，需采用特定的存储方式，以便能够从设备进行调用。 可以使用现有的 Azure 存储帐户，也可以现在就创建一个新的。 

1. 在 Azure 门户中，转到“创建资源” > “存储” > “存储帐户”。    

1. 提供以下值来创建存储帐户：

   | 字段 | 值 |
   | ----- | ----- |
   | Subscription | 选择与 IoT 中心相同的订阅。 |
   | 资源组 | 建议对在 IoT Edge 快速入门和教程中创建的所有测试资源使用同一资源组。 例如，**IoTEdgeResources**。 |
   | Name | 为存储帐户提供唯一的名称。 | 
   | 位置 | 选择靠近你的位置。 |


1. 将其他字段保留默认值，然后选择“查看 + 创建”。 

1. 查看设置，然后选择“创建”  。

### <a name="create-a-new-job"></a>创建新的作业

1. 在 Azure 门户中，转到“创建资源” > “物联网” > “流分析作业”。   

1. 提供以下值来创建作业：

   | 字段 | 值 |
   | ----- | ----- |
   | 作业名称 | 为作业提供一个名称。 例如，**IoTEdgeJob** | 
   | 订阅 | 选择与 IoT 中心相同的订阅。 |
   | 资源组 | 建议对在 IoT Edge 快速入门和教程中创建的所有测试资源使用同一资源组。 例如，**IoTEdgeResources**。 |
   | 位置 | 选择靠近你的位置。 | 
   | 宿主环境 | 选择“边缘”。  |
 
1. 选择**创建**。

### <a name="configure-your-job"></a>配置作业

在 Azure 门户中创建流分析作业以后，即可使用输入、输出以及要在流过的数据上运行的查询对其进行配置。 

本部分使用三个元素（输入、输出和查询）创建一个可以从 IoT Edge 设备接收温度数据的作业。 本部分在一个滚动的 30 秒窗口中分析该数据。 如果该窗口中的平均温度超出 70 度，则会向 IoT Edge 设备发送警报。 在下一部分部署作业时，需指定数据的具体来源和目标。  

1. 导航到 Azure 门户中的流分析作业。 

1. 在“作业拓扑”下选择“输入”，然后选择“添加流输入”。   

   ![Azure 流分析 - 添加输入](./media/tutorial-deploy-stream-analytics/asa-input.png)

1. 从下拉列表中选择“边缘中心”。 

1. 在“新建输入”  窗格中，输入 **temperature** 作为输入别名。 

1. 其他字段保留默认值，然后选择“保存”。 

1. 在“作业拓扑”下  打开“输出”  ，然后选择“添加”。 

   ![Azure 流分析 - 添加输出](./media/tutorial-deploy-stream-analytics/asa-output.png)

1. 从下拉列表中选择“边缘中心”。 

1. 在“新建输出”  窗格中，输入 **alert** 作为输出别名。 

1. 其他字段保留默认值，然后选择“保存”。 

1. 在“作业拓扑”下选择“查询”。  

1. 将默认文本替换为以下查询。 如果计算机的平均温度在 30 秒的时限内达到 70 度，SQL 代码会向警报输出发送重置命令。 重置命令已作为可执行的操作预先编程到传感器中。 

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 70
    ```

1. 选择“保存”。 

### <a name="configure-iot-edge-settings"></a>配置 IoT Edge 设置

若要准备需部署到 IoT Edge 设备的流分析作业，需将作业与存储帐户中的容器关联起来。 准备部署作业时，作业定义会导出到存储容器。 

1. 到“配置”下，依次选择“存储帐户设置”、“添加存储帐户”。    

   ![Azure 流分析 - 添加存储帐户](./media/tutorial-deploy-stream-analytics/add-storage-account.png)

1. 从下拉菜单中选择在本教程开头创建的**存储帐户**。

1. 对于“容器”字段，请选择“新建”，然后为存储容器提供一个名称。   

1. 选择“保存”。  

## <a name="deploy-the-job"></a>部署作业

现已准备好在 IoT Edge 设备上部署 Azure 流分析作业。 

在本部分，请使用 Azure 门户中的**设置模块**向导来创建部署清单。  部署清单是一个 JSON 文件，该文件描述将要部署到设备的所有模块、用于存储模块映像的容器注册表、模块的管理方式，以及模块的相互通信方式。 IoT Edge 设备从 IoT 中心检索其部署清单，任何使用其中的信息来部署和配置所有已分配的模块。 

就本教程来说，请部署两个模块。 第一个模块是 **SimulatedTemperatureSensor**：一个模拟温度和湿度传感器的模块。 第二个是流分析作业。 传感器模块提供的数据流是作业查询将要分析的。 

1. 在 Azure 门户中导航到 IoT 中心。

1. 转到“IoT Edge”，然后打开 IoT Edge 设备的详细信息页。 

1. 选择“设置模块”  。  

1. 如果以前在此设备上部署了 SimulatedTemperatureSensor 模块，则它可以自动填充数据。 否则，请通过以下步骤来添加模块：

   1. 单击“添加”，然后选择“IoT Edge 模块”。  
   1. 对于名称，请键入 **SimulatedTemperatureSensor**。
   1. 输入 **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0** 作为映像 URI。 
   1. 将其他设置保留不变，然后选择“保存”  。

1. 通过以下步骤添加 Azure 流分析 Edge 作业：

   1. 单击“添加”，然后选择“Azure 流分析模块”。  
   1. 选择创建的订阅和 Azure 流分析 Edge 作业。 
   1. 选择“保存”。 

   保存更改后，流分析作业的详细信息将发布到创建的存储容器。 

1. 将流分析模块添加到模块列表后，选择“配置”以查看其构建方式。  

   映像 URI 指向标准的 Azure 流分析映像。 此映像用于每个部署到 IoT Edge 设备的流分析模块。 

   模块孪生使用名为 **ASAJobInfo** 的所需属性配置。 该属性的值指向存储容器中的作业定义。 此属性说明如何根据特定的作业详细信息配置流分析映像。 

   默认情况下，流分析模块名称与它基于的作业的名称相同。 如果需要，可在此页上更改模块名称，但不必要这样做。 

1. 关闭模块配置页。

1. 请记下流分析模块的名称，因为需要在下一步使用它，然后选择“下一步”以继续操作。 

1. 将“Routes”中的默认值替换为以下代码。  将 _{moduleName}_ 的所有三个实例更新为 Azure 流分析模块的名称。 

    ```json
    {
        "routes": {
            "telemetryToCloud": "FROM /messages/modules/SimulatedTemperatureSensor/* INTO $upstream",
            "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream",
            "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/SimulatedTemperatureSensor/inputs/control\")",
            "telemetryToAsa": "FROM /messages/modules/SimulatedTemperatureSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")"
        }
    }
    ```

   在此处声明的路由定义流经 IoT Edge 设备的数据流。 来自 SimulatedTemperatureSensor 的遥测数据先发送到 IoT 中心，然后发送到在流分析作业中配置的“温度”输入。  **警报**输出消息先发送到 IoT 中心，然后发送到 SimulatedTemperatureSensor 模块以触发重置命令。 

1. 选择“**下一步**”。

1. 在“查看部署”步骤中，可以查看在向导中提供的信息如何转换为 JSON 部署清单。  查看完清单后，选择“提交”。 

1. 返回到“设备详细信息”页，并选择“刷新”  。  

    应会看到，新的流分析模块与 IoT Edge 代理模块和 IoT Edge 中心模块都在运行。 此信息可能需要在几分钟后才能抵达 IoT Edge 设备，然后新模块将会启动。 如果暂时未看到模块运行，请继续刷新页面。

    ![设备报告的 SimulatedTemperatureSensor 和 ASA 模块](./media/tutorial-deploy-stream-analytics/module-output2.png)

## <a name="view-data"></a>查看数据

现在可以转到 IoT Edge 设备，检查 Azure 流分析模块与 SimulatedTemperatureSensor 模块之间的交互。

1. 检查所有模块是否在 Docker 中运行：

   ```cmd/sh
   iotedge list  
   ```
   <!--
   ![Docker output](./media/tutorial-deploy-stream-analytics/docker_output.png)
   -->
1. 查看所有系统日志和指标数据。 使用流分析模块名称：

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

1. 查看传感器日志来了解重置命令对 SimulatedTemperatureSensor 的影响：

   ```cmd/sh
   iotedge logs SimulatedTemperatureSensor
   ```

   可以看到，机器温度逐渐升高，在 30 秒内达到了 70 度。 然后，流分析模块触发重置，机器温度下降到 21 度。 

   ![将命令输出重置到模块日志中](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>清理资源 

如果打算继续学习下一篇建议的文章，可以保留已创建的资源和配置，以便重复使用。 还可以继续使用相同的 IoT Edge 设备作为测试设备。 

否则，可以删除本文中使用的本地配置和 Azure 资源，以免产生费用。 
 
[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>后续步骤

本教程介绍了如何配置 Azure 流分析作业来分析 IoT Edge 设备中的数据， 然后介绍了如何将此 Azure 流分析模块加载到 IoT Edge 设备，以便处理并响应本地出现的温度增高情况，并将聚合的数据流发送到云。 若要了解 Azure IoT Edge 如何为企业创建更多解决方案，请继续学习其他教程。

> [!div class="nextstepaction"] 
> [将 Azure 机器学习模型作为一个模块部署](tutorial-deploy-machine-learning.md)
