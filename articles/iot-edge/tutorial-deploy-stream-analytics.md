---
title: "使用 Azure IoT Edge 部署 Azure 流分析 | Microsoft 文档"
description: "将 Azure 流分析作为模块部署到 Edge 设备"
services: iot-edge
keywords: 
author: msebolt
manager: timlt
ms.author: v-masebo
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0d19d1142cf15221f84692f7e613edd6b46b4083
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>将 Azure 流分析作为 IoT Edge 模块进行部署 - 预览版

IoT 设备可以生成大量的数据。 有时，这些数据必须在发送到云之前作先行分析或处理，以减少上传数据的大小，或者消除可操作见解的往返延迟。

[Azure 流分析][azure-stream] (ASA) 提供了一种丰富结构化的查询语法，可用于在云和 IoT Edge 设备上进行数据分析。 有关 IoT Edge 上 ASA 的详细信息，请参阅 [ASA 文档](../stream-analytics/stream-analytics-edge.md)。

本教程将指导你完成 Azure 流分析作业的创建，及其在 IoT Edge 设备上的部署，以便直接在设备上处理本地遥测数据流，并生成警报来驱动设备上的即时操作。  本教程涉及两个模块。 模拟温度传感器模块 (tempSensor) - 可生成范围介于 20 到 120 度的温度数据（每 5 秒递增 1 度）；以及筛选出温度高于 100 度的 ASA 模块。 当 30 秒平均值达到 100 时，ASA 模块也会重置 tempSensor。

学习如何：

> [!div class="checklist"]
> * 创建 ASA 作业以处理 Edge 设备上的数据
> * 将新的 ASA 作业与其他 IoT Edge 模块连接
> * 将 ASA 作业部署到 IoT Edge 设备

## <a name="prerequisites"></a>先决条件

* IoT 中心 
* 在快速入门中或在 [Windows][lnk-tutorial1-win] 和 [Linux][lnk-tutorial1-lin] 模拟设备上的 Deploy Azure IoT Edge 中创建和配置的设备。
* IoT Edge 设备上的 Docker
    * [在 Windows 上安装 Docker][lnk-docker-windows] 并确保其正在运行。
    * [在 Linux 上安装 Docker][lnk-docker-linux] 并确保其正在运行。
* IoT Edge 设备上的 Python 2.7.x
    * [在 Windows 上安装 Python 2.7][lnk-python]。
    * 大多数 Linux 发行版，包括 Ubuntu，均已安装 Python 2.7。  使用以下命令确保已安装 pip：`sudo apt-get install python-pip`。

> [!NOTE]
> 请注意，本教程将需要设备连接字符串和 IoT Edge 设备 ID。

IoT Edge 可利用预构建的 Azure Service IoT Edge 模块来进行快速部署，而 Azure 流分析 (ASA) 就是这样一个模块。 你可以从其门户中创建 ASA 作业，然后再到 IoT 中心门户将其部署为 IoT Edge 模块。  

有关 Azure 流分析的详细信息，请参阅[流分析文档][azure-stream]中的“概述”部分。

## <a name="create-an-asa-job"></a>创建 ASA 作业

在本部分中，你将创建 Azure 流分析作业，以从 IoT 中心提取数据、查询从设备发送的遥测数据，并将结果转发到 Azure 存储容器 (BLOB)。 有关详细信息，请参阅[流分析文档][azure-stream]中的“概述”部分。 

> [!NOTE]
> 需要有一个 Azure 存储帐户才能提供用作 ASA 作业输出的终结点。 下例使用的是 BLOB 存储类型。  有关详细信息，请参阅 [Azure 存储文档][azure-storage]中的“Blob”部分。

1. 在 Azure 门户中，导航到“创建资源 -> 存储”，单击“查看所有”，然后单击“存储帐户 - blob、文件、表、队列”。

2. 输入你的存储帐户名称，并选择存储 IoT 中心的相同位置。 单击“创建” 。 请记下名称以便稍后使用。

    ![新的存储帐户][1]

3. 在 Azure 门户中，导航到你刚创建的存储帐户。 在“Blob 服务”下单击“浏览 blob”。 
4. 为 ASA 模块创建一个新容器来存储数据。 将访问级别设置为“容器”。 单击 **“确定”**。

    ![存储设置][10]

5. 在 Azure 门户中，导航到“创建资源” > “物联网”，然后选择“流分析作业”。

2. 输入一个名称，选择“Edge”作为宿主环境，并使用剩余的默认值。  单击“创建” 。

    >[!NOTE]
    >目前，在美国西部 2 区域中不支持 IoT Edge 上的 ASA 作业。 请另选一个位置。

    ![ASA 创建][5]

2. 进入创建的作业，在“作业拓扑”下选择“输入”，单击“添加”。

3. 输入名称 `temperature`，选择“数据流”作为源类型，并对其他参数使用默认值。 单击“创建” 。

    ![ASA 输入][2]

    > [!NOTE]
    > 其他输入可以包括 IoT Edge 特定终结点。

4. 在“作业拓扑”下，选择“输出”，单击“添加”。

5. 输入名称 `alert` 并使用默认值。 单击“创建” 。

    ![ASA 输出][3]

6. 在“作业拓扑”下，选择“查询”，并输入以下内容：

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 100
    ```

## <a name="deploy-the-job"></a>部署作业

现已准备好在 IoT Edge 设备上部署 ASA 作业。

1. 在 Azure 门户的 IoT 中心，导航到“IoT Edge (预览版)”并打开“{deviceId}”的边栏选项卡。

1. 选择“设置模块”，然后选择“导入 Azure Service IoT Edge 模块”。

1. 选择订阅和你创建的 ASA Edge 作业。 然后选择存储帐户。 单击“保存” 。

    ![设置模块][6]

1. 单击“添加 IoT Edge 模块”，以添加温度传感器模块。 输入名称“tempSensor”，并输入图像 URL `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`。 将其他设置保留不变，然后单击“保存”。

    ![温度模块][11]

1. 复制 ASA 模块的名称。 单击“下一步”以配置路由。

1. 将以下内容复制到“路由”。  将 {moduleName} 替换为你复制的模块名称：

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

1. 单击“下一步”。

1. 在“审阅模板”步骤中，单击“提交”。

1. 返回到“设备详细信息”页，并单击“刷新”。  应看到新的 {moduleName} 模块与 IoT Edge 代理模块和 IoT Edge 中心在同时运行。

    ![模块输出][7]

## <a name="view-data"></a>查看数据

现在可以转到 IoT Edge 设备，以检查 ASA 模块和 tempSensor 模块之间的交互。

1. 在命令提示符处，使用 IoT Edge 设备连接字符串配置运行时：

    ```cmd/sh
    iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords  
    ```

1. 运行命令以启动运行时：

    ```cmd/sh
    iotedgectl start  
    ```

1. 运行命令以查看运行的模块：

    ```cmd/sh
    docker ps  
    ```

    ![docker 输出][8]

1. 运行命令以查看所有系统日志和指标数据。 使用上面提供的模块名称：

    ```cmd/sh
    docker logs -f {moduleName}  
    ```

    ![docker 日志][9]

1. 在 Azure 门户存储帐户的“Blob 服务”下，单击“浏览 blob”，选择你的容器，然后选择新创建的 JSON 文件。

1. 单击“下载”并查看结果。

## <a name="next-steps"></a>后续步骤

在本教程中，你配置了 Azure 存储容器和流分析作业来分析 IoT Edge 设备中的数据。  然后，你加载了自定义 ASA 模块，通过流将数据从设备移动到 BLOB 以供下载。  接下来你可以继续学习其他教程，进一步了解 Azure IoT Edge 如何为业务创建解决方案。

> [!div class="nextstepaction"] 
> [将 Azure 机器学习模型作为一个模块部署][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[2]: ./media/tutorial-deploy-stream-analytics/asa_input.png
[3]: ./media/tutorial-deploy-stream-analytics/asa_output.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output.png
[8]: ./media/tutorial-deploy-stream-analytics/docker_output.png
[9]: ./media/tutorial-deploy-stream-analytics/docker_log.png
[10]: ./media/tutorial-deploy-stream-analytics/storage_settings.png
[11]: ./media/tutorial-deploy-stream-analytics/temp_module.png


<!-- Links -->
[lnk-what-is-iot-edge]: what-is-iot-edge.md
[lnk-module-dev]: module-development.md
[iot-hub-get-started-create-hub]: ../../includes/iot-hub-get-started-create-hub.md
[azure-iot]: https://docs.microsoft.com/en-us/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/en-us/azure/storage/
[azure-stream]: https://docs.microsoft.com/en-us/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-linux]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
[lnk-python]: https://www.python.org/downloads/