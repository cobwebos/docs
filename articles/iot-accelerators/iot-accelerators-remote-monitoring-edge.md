---
title: 教程：在解决方案中检测边缘异常情况 - Azure | Microsoft Docs
description: 本教程介绍如何使用远程监视解决方案加速器监视 IoT Edge 设备。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: a812155474b244682613b38b9b9379fa6cdcdcd8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "66117543"
---
# <a name="tutorial-detect-anomalies-at-the-edge-with-the-remote-monitoring-solution-accelerator"></a>教程：使用远程监视解决方案加速器检测边缘异常情况

在本教程中，我们将配置远程监视解决方案，以便对 IoT Edge 设备检测到的异常情况做出响应。 IoT Edge 设备可让你在边缘位置处理遥测数据，以减少发送到解决方案的遥测数据量并更快地对设备上的事件做出响应。 若要详细了解边缘处理的优势，请参阅[什么是 Azure IoT Edge](../iot-edge/about-iot-edge.md)。

为了介绍如何使用远程监视进行边缘处理，本教程使用了一个模拟油泵设备。 此油泵由一家称作 Contoso 的组织进行管理，已连接到远程监视解决方案加速器。 油泵上的传感器可测量温度和压力。 Contoso 操作员知道，温度异常升高可能会导致油泵工作速度下降。 当设备温度处于正常范围时，Contoso 操作员不需要监视温度。

Contoso 想在油泵上部署一个智能边缘模块用于检测温度异常情况。 另一个边缘模块将警报发送到远程监视解决方案。 收到警报后，Contoso 操作员可以派遣维护技术人员。 Contoso 还可以配置在解决方案收到警报时要运行的自动操作，例如发送电子邮件。

下图显示了教程方案中的关键组件：

![概述](media/iot-accelerators-remote-monitoring-edge/overview.png)

本教程介绍以下操作：

>[!div class="checklist"]
> * 将 IoT Edge 设备添加到解决方案
> * 创建 Edge 清单
> * 将清单导入为定义要在设备上运行的模块的包
> * 将该包部署到 IoT Edge 设备
> * 查看设备发来的警报

在 IoT Edge 设备上：

* 运行时接收包并安装模块。
* 流分析模块检测到泵中的温度异常，并发送命令解决该问题。
* 流分析模块将筛选过的数据转发到解决方案加速器。

本教程使用 Linux 虚拟机作为 IoT Edge 设备。 你还可以安装 Edge 模块来模拟油泵千斤顶装置。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-an-iot-edge-device"></a>添加 IoT Edge 设备

需要执行两个步骤将 IoT Edge 设备添加到远程监视解决方案加速器。 本部分介绍以下操作：

* 在远程监视 Web UI 中的“设备资源管理器”页上添加 IoT Edge 设备。 
* 在 Linux 虚拟机 (VM) 中安装 IoT Edge 运行时。

### <a name="add-an-iot-edge-device-to-your-solution"></a>将 IoT Edge 设备添加到解决方案

若要将 IoT Edge 设备添加到远程监视解决方案加速器，请在 Web UI 中导航到“设备资源管理器”页，然后单击“+ 新建设备”。  

在“新建设备”面板中选择“IoT Edge 设备”，然后输入 **oil-pump** 作为设备 ID。  对于其他设置，可以保留默认值。 然后单击“应用”： 

[![添加 IoT Edge 设备](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-expanded.png#lightbox)

记下设备连接字符串，因为本教程的下一部分需要用到。

将设备注册到远程监视解决方案加速器中的 IoT 中心后，该设备将列在 Web UI 的“设备资源管理器”页上： 

[![新建 IoT Edge 设备](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-expanded.png#lightbox)

为了便于在解决方案中管理 IoT Edge 设备，请创建一个设备组并添加 IoT Edge 设备：

1. 在“设备资源管理器”页上的列表中选择“oil-pump”设备，然后单击“作业”。   

1. 使用以下设置创建一个作业，用于将 **IsEdge** 标记添加到设备：

    | 设置 | 值 |
    | ------- | ----- |
    | 作业     | Tags  |
    | 作业名称 | AddEdgeTag |
    | 密钥     | IsOilPump |
    | 值   | Y     |
    | 类型    | 文本  |

    [![添加标记](./media/iot-accelerators-remote-monitoring-edge/addtag-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addtag-expanded.png#lightbox)

1. 依次单击“应用”、“关闭”。  

1. 在“设备资源管理器”页上，单击“管理设备组”。  

1. 单击“创建新的设备组”。  使用以下设置创建新的设备组：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称    | OilPumps |
    | 字段   | Tags.IsOilPump |
    | 操作员 | =（等于） |
    | 值    | Y |
    | 类型     | 文本 |

    [![创建设备组](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-expanded.png#lightbox)

1. 单击“保存”  。

现在，上述 IoT Edge 设备会出现在 **OilPumps** 组中。

### <a name="install-the-edge-runtime"></a>安装 Edge 运行时

需要为 Edge 设备安装 Edge 运行时。 在本教程中，我们将在 Azure Linux VM 中安装 Edge 运行时来测试方案。 以下步骤使用 Azure Cloud Shell 安装并配置 VM：

1. 若要在 Azure 中创建 Linux VM，请运行以下命令。 可以使用靠近你的位置：

    ```azurecli-interactive
    az group create \
      --name IoTEdgeDevices \
      --location eastus
    az vm create \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest \
      --admin-username azureuser \
      --generate-ssh-keys \
      --size Standard_B1ms
    ```

1. 若要使用设备连接字符串配置 Edge 运行时，请使用之前记下的设备连接字符串运行以下命令：

    ```azurecli-interactive
    az vm run-command invoke \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --command-id RunShellScript \
      --scripts 'sudo /etc/iotedge/configedge.sh "YOUR_DEVICE_CONNECTION_STRING"'
    ```

    请务必在双引号内包含连接字符串。

现已在 Linux 设备上安装并配置 IoT Edge 运行时。 稍后在本教程中，我们要使用远程监视解决方案将 IoT Edge 模块部署到此设备。

## <a name="create-an-edge-manifest"></a>创建 Edge 清单

若要模拟油泵设备，需将以下模块添加到 Edge 设备：

* 温度模拟模块。
* Azure 流分析异常情况检测。

以下步骤说明如何创建包含这些模块的 Edge 部署清单。 在本教程稍后，我们将在远程监视解决方案加速器中以包的形式导入此清单。

### <a name="create-the-azure-stream-analytics-job"></a>创建 Azure 流分析作业

先在门户中定义流分析作业，然后将其打包成 Edge 模块。

1. 在 Azure 门户中，使用“IoTEdgeDevices”资源组中的默认选项创建 Azure 存储帐户。  记下所选的名称。

1. 在 Azure 门户上的“IoTEdgeDevices”资源组中创建一个**流分析作业**。  使用以下配置值：

    | 选项 | 值 |
    | ------ | ----- |
    | 作业名称 | EdgeDeviceJob |
    | 订阅 | Azure 订阅 |
    | 资源组 | IoTEdgeDevices |
    | 位置 | 美国东部 |
    | 宿主环境 | Microsoft Edge |
    | 流式处理单位 | 1 |

1. 在门户中打开“EdgeDeviceJob”流分析作业，单击“输入”，然后添加名为 **telemetry** 的 **Edge 中心**流输入。

1. 在门户上的“EdgeDeviceJob”流分析作业中，单击“输出”，然后添加名为 **output** 的 **Edge 中心**输出。 

1. 在门户上的“EdgeDeviceJob”流分析作业中，单击“输出”，然后添加名为 **alert** 的另一个 **Edge 中心**输出。 

1. 在门户上的“EdgeDeviceJob”流分析作业中，单击“查询”，然后添加以下 **select** 语句： 

    ```sql
    SELECT  
    avg(machine.temperature) as temperature, 
    'F' as temperatureUnit 
    INTO output 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 5) 
    SELECT 'reset' as command 
    INTO alert 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 3) 
    HAVING avg(machine.temperature) > 400
    ```

1. 在门户上的“EdgeDeviceJob”流分析作业中，单击“存储帐户设置”。   添加在本部分开头添加到“IoTEdgeDevices”资源组的存储帐户。  创建名为 **edgeconfig** 的新容器。

以下屏幕截图显示保存的流分析作业：

[![流分析作业](./media/iot-accelerators-remote-monitoring-edge/streamjob-inline.png)](./media/iot-accelerators-remote-monitoring-edge/streamjob-expanded.png#lightbox)

现已定义要在 Edge 设备上运行的流分析作业。 该作业计算 5 秒时间范围内的平均温度。 如果 3 秒时间范围内的平均温度超过 400，该作业还会发送警报。

### <a name="create-the-iot-edge-deployment"></a>创建 IoT Edge 部署

接下来，创建一个 IoT Edge 部署清单，用于定义要在 Edge 设备上运行的模块。 在下一部分，我们将在远程监视解决方案中以包的形式导入此清单。

1. 在 Azure 门户中，导航到远程监视解决方案中的 IoT 中心。 可在与远程监视解决方案同名的资源组中找到 IoT 中心。

1. 在 IoT 中心，单击“自动设备管理”部分中的“IoT Edge”。   单击“添加 IoT Edge 部署”。 

1. 在“创建部署”>“名称和标签”页上，输入名称 **oil-pump-device**。 单击“下一步”。 

1. 在“创建部署”>“添加模块”页上，单击“+ 添加”。   选择“IoT Edge 模块”  。

1. 在“IoT Edge 自定义模块”面板中，输入 **temperatureSensor** 作为名称，输入 **asaedgedockerhubtest/asa-edge-test-module:sensor-ad-linux-amd64** 作为映像 URI。 单击“保存”  。

1. 在“创建部署”>“添加模块”页上，单击“+ 添加”以添加另一个模块。   选择“Azure 流分析模块”  。

1. 在“Edge 部署”面板中选择你的订阅以及在上一部分创建的 **EdgeDeviceJob**。 单击“保存”  。

1. 在“创建部署”>“添加模块”页上，单击“下一步”。  

1. 在“创建部署”>“指定路由”页上添加以下代码： 

    ```sql
    {
      "routes": { 
        "alertsToReset": "FROM /messages/modules/EdgeDeviceJob/outputs/alert INTO BrokeredEndpoint(\"/modules/temperatureSensor/inputs/control\")", 
        "telemetryToAsa": "FROM /messages/modules/temperatureSensor/* INTO BrokeredEndpoint(\"/modules/EdgeDeviceJob/inputs/telemetry\")", 
        "ASAToCloud": "FROM /messages/modules/EdgeDeviceJob/* INTO $upstream" 
      } 
    }
    ```

    此代码将流分析模块的输出路由到正确的位置。

    单击“下一步”。 

1. 在“创建部署”>“指定指标”页上，单击“下一步”。  

1. 在“创建部署”>“目标设备”页上，输入 10 作为优先级。  单击“下一步”。 

1. 在“创建部署”>“检查部署”页上，单击“提交”：  

    [![检查部署](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-expanded.png#lightbox)

1. 在“IoT Edge”主页上，单击“IoT Edge 部署”。   在部署列表中可以看到“oil-pump-device”。 

1. 单击“oil-pump-device”部署，然后单击“下载 IoT Edge 清单”。   将该文件作为 **oil-pump-device.json** 保存到本地计算机上的适当位置。 在本教程的下一部分需要用到此文件。

现已创建一个可以包的形式导入到远程监视解决方案的 IoT Edge 清单。 通常，开发人员会创建 IoT Edge 模块和清单文件。

## <a name="import-a-package"></a>导入包

在本部分，我们将在远程监视解决方案中以包的形式导入 Edge 清单。

1. 在远程监视 Web UI 中，导航到“包”页并单击“+ 新建包”：  

    [![新建包](./media/iot-accelerators-remote-monitoring-edge/newpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newpackage-expanded.png#lightbox)

1. 在“新建包”面板中，选择“Edge 清单”作为包类型，单击“浏览”在本地计算机上找到“oil-pump-device.json”文件，然后单击“上传”：     

    [![上传包](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-expanded.png#lightbox)

    现在，包列表包含“oil-pump-device.json”包。 

在下一部分，我们将创建一个将该包应用到 Edge 设备的部署。

## <a name="deploy-a-package"></a>部署包

现已准备好将该包部署到设备。

1. 在远程监视 Web UI 中，导航到“部署”页并单击“+ 新建部署”：  

    [![新建部署](./media/iot-accelerators-remote-monitoring-edge/newdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newdeployment-expanded.png#lightbox)

1. 在“新建部署”面板中，使用以下设置创建部署： 

    | 选项 | 值 |
    | ------ | ----- |
    | 名称   | OilPumpDevices |
    | 包类型 | Edge 清单 |
    | 程序包 | oil-pump-device.json |
    | 设备组 | OilPumps |
    | 优先度 | 10 |

    [![创建部署](./media/iot-accelerators-remote-monitoring-edge/createdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdeployment-expanded.png#lightbox)

    单击“应用”  。

需要等待几分钟，以便将包部署到设备，并使遥测数据开始流出设备。

[![活动的部署](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-inline.png)](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-expanded.png#lightbox)

“部署”页显示以下指标： 

* “目标”显示设备组中的设备数。 
* “已应用”显示已应用部署内容的设备数。 
* “成功”显示部署中报告 IoT Edge 客户端运行时成功的 Edge 设备数。 
* “失败”显示部署中报告 IoT Edge 客户端运行时失败的 Edge 设备数。 

## <a name="monitor-the-device"></a>监视设备

可在远程监视 Web UI 中查看油泵设备发出的温度遥测数据：

1. 导航到“设备资源管理器”页并选择油泵设备。 
1. 在“设备详细信息”面板中的“遥测”部分，单击“温度”：   

    [![查看遥测数据](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-expanded.png#lightbox)

可以看到温度不断上升，最终达到阈值。 当温度达到此阈值时，流分析 Edge 模块可以检测到这种状况，并向设备发送立即降低温度的命令。 所有这些处理都是在设备上发生的，无需与云通信。

若要在达到阈值时通知操作员，可在远程监视 Web UI 中创建一个规则：

1. 导航到“规则”页并单击“+ 新建规则”。  
1. 使用以下设置创建新规则：

    | 选项 | 值 |
    | ------ | ----- |
    | 规则名称 | 油泵温度 |
    | 说明 | 油泵温度超过 300 |
    | 设备组 | OilPumps |
    | 计算 | 即时 |
    | 字段 | 温度 |
    | 操作员 | > |
    | 值 | 300 |
    | 严重性级别 | 信息 |

    [![创建规则](./media/iot-accelerators-remote-monitoring-edge/newrule-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newrule-expanded.png#lightbox)

    单击“应用”  。

1. 导航到“仪表板”页。  当 **oil-pump** 设备的温度超过 300 时，“警报”面板中会显示一条警报。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何在远程监视解决方案加速器中添加和配置 IoT Edge 设备。 若要详细了解如何在远程监视解决方案中使用 IoT Edge 包，请参阅以下操作指南：

> [!div class="nextstepaction"]
> [将 IoT Edge 包导入到远程监视解决方案加速器](iot-accelerators-remote-monitoring-import-edge-package.md)

若要详细了解如何安装 IoT Edge 运行时，请参阅[在 Linux (x64) 上安装 Azure IoT Edge 运行时](../iot-edge/how-to-install-iot-edge-linux.md)。

若要详细了解 Edge 设备上的 Azure 流分析，请参阅[将 Azure 流分析部署为 IoT Edge 模块](../iot-edge/tutorial-deploy-stream-analytics.md)。
