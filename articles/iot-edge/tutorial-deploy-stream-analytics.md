---
title: 教程 - 将 ASA 作业部署到 Azure IoT Edge 设备 | Microsoft Docs
description: 将 Azure 流分析作为模块部署到 IoT Edge 设备
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/25/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: ad2895a457a20632823260f2429ac95fad82089c
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060189"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>教程：将 Azure 流分析作为 IoT Edge 模块进行部署 - 预览版

许多 IoT 解决方案使用分析服务来了解从 IoT 设备到达云的数据。 使用 Azure IoT Edge 时，可以获取 [Azure 流分析][azure-stream]逻辑，将其转到设备中。 在边缘处理遥测流可以减少上传数据量，缩短对可操作见解进行响应的时间。

Azure IoT Edge 和 Azure 流分析已集成，因此可以在 Azure 门户中创建一项 Azure 流分析作业，然后将其作为 IoT Edge 模块进行部署，不需额外的代码。  

Azure 流分析提供一种丰富结构化的查询语法，可用于在云和 IoT Edge 设备上进行数据分析。 有关 IoT Edge 上的 Azure 流分析的详细信息，请参阅 [Azure 流分析文档](../stream-analytics/stream-analytics-edge.md)。

本教程中的流分析模块在一个滚动的 30 秒时段内计算平均温度。 当平均温度达到 70 时，模块会发送一个警报，以便设备采取操作。 在这种情况下，该操作是重置模拟温度传感器。 在生产环境中，当温度达到危险级别时，可以使用此功能关闭机器或采取预防措施。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Edge 上创建 Azure 流分析作业用于处理数据。
> * 将新的 Azure 流分析作业与其他 IoT Edge 模块相连接。
> * 将 Azure 流分析作业从 Azure 门户部署到 IoT Edge 设备。

>[!NOTE]
>IoT Edge 的 Azure 流分析模块为[公开预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

* IoT 中心
* 在 [Windows][lnk-quickstart-win] 或 [Linux][lnk-quickstart-lin] 快速入门中创建并配置的 IoT Edge 设备。 

## <a name="create-an-azure-stream-analytics-job"></a>创建 Azure 流分析作业

本部分的步骤将创建一个 Azure 流分析作业，用于从 IoT 中心提取数据、查询从设备发送的遥测数据，然后将结果转发到 Azure Blob 存储容器。 有关详细信息，请参阅[流分析文档][azure-stream]中的“概述”部分。 

### <a name="create-a-storage-account"></a>创建存储帐户

对于 Azure 流分析作业来说，Azure 存储帐户是必需的，其作用是充当作业输出的终结点。 本部分中的示例使用 Blob 存储类型。 有关详细信息，请参阅 [Azure 存储文档][azure-storage]中的“Blob”部分。

1. 在 Azure 门户中，转到“创建资源”，在搜索框输入“存储帐户”，选择“存储帐户 - Blob、文件、表、队列”。

2. 在“创建存储帐户”窗格中，输入存储帐户的名称，选择存储 IoT 中心的同一位置，选择 IoT 中心所在的资源组，然后选择“创建”。 请记下该名称供稍后使用。

    ![创建存储帐户][1]


### <a name="create-a-stream-analytics-job"></a>创建流分析作业

1. 在 Azure 门户中，转到“创建资源” > “物联网”，然后选择“流分析作业”。

2. 在“新建流分析作业”窗格中执行以下步骤：

   1. 在“作业名称”框中键入作业名称。
   
   2. 与 IoT 中心使用同一**资源组**和**位置**。 

      > [!NOTE]
      > 目前，美国西部 2 区域不支持 IoT Edge 上的 Azure 流分析作业。 

   3. 在“宿主环境”下，选择“Edge”。
    
3. 选择**创建**。

4. 在创建的作业中，在“作业拓扑”下，打开“输入”。

   ![Azure 流分析输入](./media/tutorial-deploy-stream-analytics/asa_input.png)

5. 选择“添加流输入”，然后选择“Edge 中心”。

6. 在“新建输入”窗格中，输入 **temperature** 作为输入别名。 

7. 选择“保存”。

8. 在“作业拓扑”下，打开“输出”。

   ![Azure 流分析输出](./media/tutorial-deploy-stream-analytics/asa_output.png)

9. 选择“添加”，然后选择“Edge 中心”。

10. 在“新建输出”窗格中，输入 **alert** 作为输出别名。 

11. 选择“保存”。

12. 在“作业拓扑”下选择“查询”，然后将默认文本替换为以下查询（如果计算机的平均温度在 30 秒的时限内达到 70 度，该查询会创建警报）：

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

13. 选择“保存”。

14. 在“配置”下选择“IoT Edge 设置”。

15. 从下拉菜单中选择自己的**存储帐户**。

16. 对于“容器”字段，请选择“新建”，然后为存储容器提供一个名称。 

17. 选择“保存”。 


## <a name="deploy-the-job"></a>部署作业

现已准备好在 IoT Edge 设备上部署 Azure 流分析作业。

1. 在 Azure 门户的 IoT 中心转到“IoT Edge”，然后打开 IoT Edge 设备的详细信息页。

2. 选择“设置模块”。  

   如果以前在此设备上部署了 tempSensor 模块，则它可以自动填充数据。 如果该模块不存在，请通过以下步骤来添加该模块：

   1. 单击“添加”，然后选择“IoT Edge 模块”。
   2. 键入 **tempsensor** 作为名称。
   3. 输入 **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0** 作为映像 URI。 
   4. 将其他设置保留不变。
   5. 选择“保存”。

3. 通过以下步骤添加 Azure 流分析 Edge 作业：

   1. 单击“添加”，然后选择“Azure 流分析模块”。
   2. 选择创建的订阅和 Azure 流分析 Edge 作业。 
   3. 选择“保存”。

4. 选择“**下一步**”。

5. 将“Routes”中的默认值替换为以下代码。 将 _{moduleName}_ 更新为 Azure 流分析模块的名称。 此模块的名称应该与创建模块时所依据的作业的名称相同。 

    ```json
    {
        "routes": {
            "telemetryToCloud": "FROM /messages/modules/tempSensor/* INTO $upstream",
            "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream",
            "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")",
            "telemetryToAsa": "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")"
        }
    }
    ```

6. 选择“**下一步**”。

7. 在“复查部署”步骤中，选择“提交”。

8. 返回到“设备详细信息”页，并选择“刷新”。  

    应会看到新的流分析模块与 IoT Edge 代理模块和 IoT Edge 中心在同时运行。

    ![模块输出][7]

## <a name="view-data"></a>查看数据

现在可以转到 IoT Edge 设备，检查 Azure 流分析模块与 tempSensor 模块之间的交互。

1. 检查所有模块是否在 Docker 中运行：

   ```cmd/sh
   iotedge list  
   ```
<!--
   ![Docker output][8]
-->
2. 查看所有系统日志和指标数据。 使用流分析模块名称：

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

应该可以看到，机器温度逐渐升高，在 30 秒内达到了 70 度。 然后，流分析模块触发重置，机器温度下降到 21 度。 

   ![Docker 日志][9]

## <a name="clean-up-resources"></a>清理资源 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

如果想要继续学习下一篇建议的文章，可以保留已创建的资源和配置，以便重复使用。

否则，可删除本文中创建的本地配置和 Azure 资源，避免收费。 

> [!IMPORTANT]
> 删除 Azure 资源和资源组的操作不可逆。 资源组以及包含在其中的所有资源一旦删除就会被永久删除。 请确保不会意外删除错误的资源组或资源。 如果在现有的包含要保留资源的资源组中创建了 IoT 中心，则只删除 IoT 中心资源本身，而不要删除资源组。
>

若要只删除 IoT 中心，请使用中心名称和资源组名称执行以下命令：

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


若要按名称删除整个资源组，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)，并单击“资源组”。

2. 在“按名称筛选...”文本框中键入包含 IoT 中心的资源组的名称。 

3. 在结果列表中的资源组右侧，单击“...”，然后单击“删除资源组”。

<!--
   ![Delete](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)
-->
4. 系统会要求确认是否删除资源组。 再次键入资源组的名称进行确认，然后单击“删除”。 片刻之后，将会删除该资源组及其包含的所有资源。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何配置 Azure 流分析作业来分析 IoT Edge 设备中的数据， 然后介绍了如何将此 Azure 流分析模块加载到 IoT Edge 设备，以便处理并响应本地出现的温度增高情况，并将聚合的数据流发送到云。 若要了解 Azure IoT Edge 如何为企业创建更多解决方案，请继续学习其他教程。

> [!div class="nextstepaction"] 
> [将 Azure 机器学习模型作为一个模块部署][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output2.png
[8]: ./media/tutorial-deploy-stream-analytics/docker_output.png
[9]: ./media/tutorial-deploy-stream-analytics/docker_log.png
[10]: ./media/tutorial-deploy-stream-analytics/storage_settings.png
[11]: ./media/tutorial-deploy-stream-analytics/temp_module.png


<!-- Links -->
[lnk-what-is-iot-edge]: what-is-iot-edge.md
[lnk-module-dev]: module-development.md
[iot-hub-get-started-create-hub]: ../../includes/iot-hub-get-started-create-hub.md
[azure-iot]: https://docs.microsoft.com/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/azure/storage/
[azure-stream]: https://docs.microsoft.com/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-quickstart-win]: quickstart.md
[lnk-quickstart-lin]: quickstart-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

