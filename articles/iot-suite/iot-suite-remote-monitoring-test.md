---
title: "远程监视解决方案中的设备模拟 - Azure | Microsoft Docs"
description: "本教程介绍如何在远程监视预配置解决方案中使用设备模拟器。"
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/15/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 4bdcd6d57989df3d1b67c87d56b8c57035ef2f63
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="test-your-solution-with-simulated-devices"></a>使用模拟设备测试解决方案

本教程介绍如何在远程监视预配置解决方案中自定义设备模拟器微服务。 为了演示设备模拟器的功能，本教程在 Contoso IoT 应用程序中使用了两个方案。

在第一个方案中，Contoso 想要测试新的智能灯泡设备。 为了执行测试，我们创建了具有以下特征的新模拟设备：

*属性*

| 名称                     | 值                      |
| ------------------------ | --------------------------- |
| 颜色                    | 白、红、蓝            |
| 亮度               | 0 到 100                    |
| 估计剩余生命 | 从 10,000 小时开始倒计数 |

*遥测*

下表显示了灯泡以数据流形式报告给云的数据：

| 名称   | 值      |
| ------ | ----------- |
| 状态 | "on"、"off" |
| 温度 | 华氏度 |
| 联机 | true、false |

> [!NOTE]
> 对于所有模拟类型来说，**联机**遥测值是必需的。

*方法*

下表显示了新设备支持的操作：

| 名称        |
| ----------- |
| 打开   |
| 关闭  |

*初始状态*

下表显示了设备的初始状态：

| 名称                     | 值 |
| ------------------------ | -------|
| 初始颜色            | 白色  |
| 初始亮度       | 75     |
| 初始剩余生命   | 10,000 |
| 初始遥测状态 | "on"   |
| 初始遥测温度 | 200   |

在第二个方案中，我们将新的遥测类型添加到 Contoso 的现有**冷却器**设备。

本教程将介绍如何在远程监视预配置解决方案中使用设备模拟器：

本教程介绍如何执行下列操作：

>[!div class="checklist"]
> * 创建新设备类型
> * 模拟自定义设备行为
> * 将新设备类型添加到仪表板
> * 从现有的设备类型发送自定义遥测数据

以下视频演示了如何将模拟和真实设备连接到远程监视解决方案：

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Part-38-Customizing-Azure-IoT-Suite-solution-and-connect-a-real-device/Player]

## <a name="prerequisites"></a>先决条件

若要学习本教程，需要：

* 在 Azure 订阅中部署远程监视解决方案的实例。 如果尚未部署远程监视解决方案，应完成[部署远程监视预配置解决方案](iot-suite-remote-monitoring-deploy.md)教程。

* Visual Studio 2017。 如果尚未安装 Visual Studio 2017，可以下载免费的[Visual Studio Community Edition](https://www.visualstudio.com/free-developer-offers/)。

* [Cloud Explorer for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVS15Preview) Visual Studio 扩展。

* [Docker 中心](https://hub.docker.com/)帐户。 可以免费注册以开始学习本教程。

* 已在台式机上安装 [Git](https://git-scm.com/downloads)。

## <a name="prepare-your-development-environment"></a>准备开发环境

完成以下任务，将新的模拟设备添加到远程监视解决方案，以准备好开发环境：

### <a name="configure-ssh-access-to-the-solution-virtual-machine-in-azure"></a>在 Azure 中配置对解决方案虚拟机的 SSH 访问

在 [www.azureiotsuite.com](https://www.azureiotsuite.com) 中创建远程监视解决方案时，已选择了一个解决方案名称。 该解决方案名称将是包含解决方案所用的各个已部署资源的 Azure 资源组的名称。 以下命令使用名为 **Contoso-01** 的资源组，应将 **Contoso-01** 替换为自己的资源组名称。

以下命令使用 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest) 中的 `az` 命令。 可以在开发计算机上安装 Azure CLI 2.0，或者在 [Azure 门户](http://portal.azure.com)中使用 [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)。 Cloud Shell 中已预装 Azure CLI 2.0。

1. 若要验证包含远程监视资源的资源组的名称，请运行以下命令：

    ```sh
    az group list | grep "name"
    ```

    此命令列出订阅中的所有资源组。 该列表应包含一个与远程监视解决方案同名的资源组。

1. 若要将你的资源组设置为后续命令的默认组，请运行以下命令，并用该资源组的名称取代 **Contoso-01**：

    ```sh
    az configure --defaults group=Contoso-01
    ```

1. 若要列出资源组中的资源，请运行以下命令：

    ```sh
    az resource list -o table
    ```

    记下虚拟机和网络安全组的名称。 在后续步骤中将要用到这些值。

1. 若要启用对虚拟机的 SSH 访问，请使用上一步骤中记下的网络安全组名称运行以下命令：

    ```sh
    az network nsg rule create --name SSH --nsg-name YOUR-NETWORK-SECURITY-GROUP --priority 101 --destination-port-ranges 22 --access Allow --protocol TCP
    ```

    若要查看网络的入站规则列表，请运行以下命令：

    ```sh
    az network nsg rule list --nsg-name YOUR-NETWORK-SECURITY-GROUP -o table
    ```

1. 若要将虚拟机密码更改为所需的密码，请运行以下命令。 使用前面记下的虚拟机名称和所选的密码：

    ```sh
    az vm user update --name YOUR-VM-NAME --username azureuser --password YOUR-PASSWORD
    ```
1. 若要查找虚拟机的 IP 地址，请使用以下命令，并记下公共 IP 地址：

    ```sh
    az vm list-ip-addresses --name YOUR-VM-NAME
    ```

1. 现在，可以使用 SSH 连接到虚拟机。 Cloud Shell 中已预装 `ssh` 命令。 使用上一步骤中记下的公共 IP 地址，并在出现提示时，输入针对虚拟机配置的密码：

    ```sh
    ssh azureuser@public-ip-address
    ```

    现在，可以访问在远程监视解决方案中运行 Docker 容器的虚拟机中的 shell。 若要查看正在运行的容器，请使用以下命令：

    ```sh
    docker ps
    ```

### <a name="find-the-service-connection-strings"></a>查找服务连接字符串

本教程使用 Visual Studio 解决方案可连接到该解决方案的 Cosmos DB 和 IoT 中心服务。 以下步骤演示一种查找所需连接字符串值的方法：

1. 若要查找 Cosmos DB 连接字符串，请在已连接到虚拟机的 SSH 会话中运行以下命令：

    ```sh
    sudo grep STORAGEADAPTER_DOCUMENTDB /app/env-vars
    ```

    记下连接字符串。 本教程稍后会用到此值。

1. 若要查找 IoT 中心连接字符串，请在已连接到虚拟机的 SSH 会话中运行以下命令：

    ```sh
    sudo grep IOTHUB_CONNSTRING /app/env-vars
    ```

    记下连接字符串。 本教程稍后会用到此值。

> [!NOTE]
> 也可以在 Azure 门户中或使用 `az` 命令找到这些连接字符串。

### <a name="stop-the-device-simulation-service-in-the-virtual-machine"></a>在虚拟机中停止设备模拟服务

修改设备模拟服务时，可以在本地运行该服务以测试更改。 在本地运行设备模拟服务之前，必须按如下所示停止虚拟机中运行的实例：

1. 若要查找 **device-simulation** 服务的**容器 ID**，请在已连接到虚拟机的 SSH 会话中运行以下命令：

    ```sh
    docker ps
    ```

    记下 **device-simulation** 服务的容器 ID。

1. 若要停止 **device-simulation** 容器，请运行以下命令：

    ```sh
    docker stop container-id-from-previous-step
    ```

### <a name="clone-the-github-repositories"></a>克隆 GitHub 存储库

本教程使用 **device-simulation** 和 **storage-adapter** Visual Studio 项目。 可以从 GitHub 克隆源代码存储库。 请在装有 Visual Studio 的本地开发计算机上执行此步骤：

1. 打开命令提示符，并导航到要将 **device-simulation** 和 **storage-adapter** GitHub 存储库副本保存到的文件夹。

1. 若要克隆 .NET 版本的 **device-simulation** 存储库，请运行以下命令：

    ```cmd
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

    使用远程监视解决方案中的设备模拟服务可对内置的模拟设备类型进行更改，以及创建新的模拟设备类型。 连接物理设备之前，可以使用自定义设备类型测试远程监视解决方案的行为。

1. 若要克隆 .NET 版本的 **storage-adapter** 存储库，请运行以下命令：

    ```cmd
    git clone https://github.com/Azure/storage-adapter.git
    ```

    设备模拟服务使用存储适配器服务连接到 Azure 中的 Cosmos DB 服务。 远程监视解决方案将模拟设备配置数据存储在 Cosmos DB 数据库中。

### <a name="run-the-storage-adapter-service-locally"></a>在本地运行存储适配器服务

设备模拟服务使用存储适配器服务连接到解决方案的 Cosmos DB 数据库。 如果在本地运行设备模拟服务，则也必须在本地运行存储适配器服务。 以下步骤演示如何从 Visual Studio 运行存储适配器服务：

1. 在 Visual Studio 中，打开 **storage-adapter** 存储库的本地副本中的 **pcs-storage-adapter.sln** 解决方案文件。

1. 在解决方案资源管理器中，右键单击“WebService”项目，并依次选择“属性”、“调试”。

1. 在“环境变量”部分，将 **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** 变量的值编辑成前面记下的 Cosmos DB 连接字符串。 保存更改。

1. 在解决方案资源管理器中，右键单击“WebService”项目，并依次选择“调试”、“启动新实例”。

1. 该服务将在本地开始运行，并在默认浏览器中打开 `http://localhost:9022/v1/status`。 检查“状态”值是否为“正常: 活动且正常。”

1. 在完成本教程之前，请让存储适配器服务一直在本地保持运行。

现已做好所有准备，接下来可以开始将新的模拟设备类型添加到远程监视解决方案。

## <a name="create-a-simulated-device-type"></a>创建模拟设备类型

在设备模拟服务中创建新设备类型的最简单方法是复制并修改现有类型。 以下步骤说明如何复制内置的**冷却器**设备以创建新的**灯泡**设备：

1. 在 Visual Studio 中，打开 **device-simulation** 存储库的本地副本中的 **device-simulation.sln** 解决方案文件。

1. 在解决方案资源管理器中，右键单击“SimulationAgent”项目，并依次选择“属性”、“调试”。

1. 在“环境变量”部分，将 **PCS\_IOTHUB\_CONNSTRING** 变量的值编辑成前面记下的 IoT 中心连接字符串。 保存更改。

1. 在解决方案资源管理器中，右键单击“device-simulation”解决方案并选择“设置启动项目”。 依次选择“单启动项目”、“SimulationAgent”。 然后单击“确定”。

1. 每种设备类型都有一个 JSON 模型文件，并且在 **Services/data/devicemodels** 文件夹中具有关联的脚本。 在解决方案资源管理器中，如下表中所示，复制**冷却器**文件以创建**灯泡**文件：

    | Source                      | 目标                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | scripts/chiller-01-state.js | scripts/lightbulb-01-state.js |
    | scripts/reboot-method.js    | scripts/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>定义新设备类型的特征

**lightbulb-01.json** 文件定义类型的特征，例如，设备生成的遥测数据以及设备支持的方法。 以下步骤更新 **lightbulb-01.json** 文件以定义**灯泡**设备：

1. 在 **lightbulb-01.json** 文件中，如以下片段中所示更新设备元数据：

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. 在 **lightbulb-01.json** 文件中，如以下片段中所示更新模拟定义：

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "temperature": 200.0,
        "temperature_unit": "F",
        "status": "on"
      },
      "Script": {
        "Type": "javascript",
        "Path": "lightbulb-01-state.js",
        "Interval": "00:00:20"
      }
    },
    ```

1. 在 **lightbulb-01.json** 文件中，如以下片段中所示更新设备类型属性：

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. 在 **lightbulb-01.json** 文件中，如以下片段中所示更新设备类型遥测定义：

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "temperature": "double",
            "temperature_unit": "text",
            "status": "text"
          }
        }
      }
    ],
    ```

1. 在 **lightbulb-01.json** 文件中，如以下片段中所示更新设备类型方法：

    ```json
    "CloudToDeviceMethods": {
      "SwitchOn": {
        "Type": "javascript",
        "Path": "SwitchOn-method.js"
      },
      "SwitchOff": {
        "Type": "javascript",
        "Path": "SwitchOff-method.js"
      }
    }
    ```

1. 保存 **lightbulb-01.json** 文件。

### <a name="simulate-custom-device-behavior"></a>模拟自定义设备行为

**scripts/lightbulb-01-state.js** 文件定义 **Lightbulb** 类型的模拟行为。 以下步骤更新 **scripts/lightbulb-01-state.js** 文件，以定义**灯泡**设备的行为：

1. 在 **scripts/lightbulb-01-state.js** 文件中，如以下片段中所示编辑状态定义：

    ```js
    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };
    ```

1. 在 **vary** 函数的后面添加包含以下定义的 **flip** 函数：

    ```js
    /**
    * Simple formula that sometimes flips the status of the lightbulb
    */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }
    ```

1. 如以下片段中所示，编辑 **main** 函数以实现行为：

    ```js
    function main(context, previousState) {

      // Restore the global state before generating the new telemetry, so that
      // the telemetry can apply changes using the previous function state.
      restoreState(previousState);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. 保存 **scripts/lightbulb-01-state.js** 文件。

**scripts/SwitchOn-method.js** 文件在**灯泡**设备中实现 **SwitchOn** 方法。 以下步骤更新 **scripts/SwitchOn-method.js** 文件：

1. 在 **scripts/SwitchOn-method.js** 文件中，如以下片段中所示编辑状态定义：

    ```js
    var state = {
       status: "on"
    };
    ```

1. 若要打开灯泡，请按如下所示编辑 **main** 函数：

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. 保存 **scripts/SwitchOn-method.js** 文件。

1. 创建名为 **scripts/SwitchOff-method.js** 的 **scripts/SwitchOn-method.js** 文件副本。

1. 若要关闭灯泡，请在 **scripts/SwitchOff-method.js** 文件中按如下所示编辑 **main** 函数：

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. 保存 **scripts/SwitchOff method.js** 文件。

1. 在解决方案资源管理器中，轮流选择四个新文件中的每一个。 在每个文件的“属性”窗口中，检查“复制到输出目录”是否设置为“有更新时才复制”。

### <a name="configure-the-device-simulation-service"></a>配置设备模拟服务

若要限制测试过程中连接到解决方案的模拟设备数，请将服务配置为运行单个冷却器和单个灯泡设备。 配置数据存储在解决方案资源组中的 Cosmos DB 实例内。 若要编辑配置数据，请在 Visual Studio 中使用“Cloud Explorer”视图：

1. 若要在 Visual Studio 中打开“Cloud Explorer”视图，请依次选择“视图”、“Cloud Explorer”。

1. 若要查找模拟配置文档，请在“搜索资源”中输入 **simualtions.1**。

1. 双击“simulations.1”文档将其打开以进行编辑。

1. 在“数据”值中，找到如以下片段所示的 **DeviceModels** 数组：

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"chiller-02\",\"Count\":1},{\"Id\":\"elevator-01\",\"Count\":1},{\"Id\":\"elevator-02\",\"Count\":1},{\"Id\":\"engine-01\",\"Count\":1},{\"Id\":\"engine-02\",\"Count\":1},{\"Id\":\"prototype-01\",\"Count\":1},{\"Id\":\"prototype-02\",\"Count\":1},{\"Id\":\"truck-01\",\"Count\":1},{\"Id\":\"truck-02\",\"Count\":1}]
    ```

1. 若要定义单个冷却器和单个灯泡模拟设备，请将 **DeviceModels** 数组替换为以下代码：

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"lightbulb-01\",\"Count\":1}]
    ```

    保存对 **simulations.1** 文档所做的更改。

> [!NOTE]
> 也可以在 Azure 门户中使用 Cosmos DB 数据资源管理器来编辑 **simulations.1** 文档。

### <a name="test-the-lightbulb-device-type-locally"></a>在本地测试灯泡设备类型

现在，可以通过在本地运行设备模拟项目，来测试新的模拟灯泡类型。

1. 在解决方案资源管理器中，右键单击“SimulationAgent”，并依次选择“调试”、“启动新实例”。

1. 若要检查两个模拟设备是否已连接到 IoT 中心，请在浏览器中打开 Azure 门户。

1. 导航到包含远程监视解决方案的资源组中的 IoT 中心。

1. 在“监视”部分选择“指标”。 然后检查“连接的设备”数目是否为 2：

    ![连接的设备数](media/iot-suite-remote-monitoring-test/connecteddevices.png)

1. 在浏览器中，导航到远程监视解决方案的**仪表板**。 在**仪表板**上的遥测面板中，选择“温度”。 图表中将显示两个模拟设备的温度：

    ![温度遥测](media/iot-suite-remote-monitoring-test/telemetry.png)

现已在本地运行灯泡设备模拟。 下一步是将更新的模拟器代码部署到在 Azure 中运行远程监视微服务的虚拟机。

在继续操作之前，可以在 Visual Studio 中停止调试设备模拟和存储适配器项目。

### <a name="deploy-the-updated-simulator-to-the-cloud"></a>将更新的模拟器部署到云中

远程监视解决方案中的微服务在 docker 容器中运行。 容器托管在 Azure 中的解决方案虚拟机上。 本部分的操作：

* 创建新的设备模拟 docker 映像。
* 将该映像上传到 docker 中心存储库。
* 将该映像导入到解决方案的虚拟机中。

以下步骤假设 Docker 中心帐户包含一个名为 **lightbulb** 的存储库。

1. 在 Visual Studio 中的 **device-simulation** 项目内，打开文件 **solution\scripts\docker\build.cmd**。

1. 将用于设置 **DOCKER_IMAGE** 环境变量的行编辑成 Docker 中心存储库名称：

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    保存更改。

1. 在 Visual Studio 中的 **device-simulation** 项目内，打开文件 **solution\scripts\docker\publish.cmd**。

1. 将用于设置 **DOCKER_IMAGE** 环境变量的行编辑成 Docker 中心存储库名称：

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    保存更改。

1. 以管理员身份打开命令提示符。 然后导航到 **device-simulation** GitHub 存储库副本中的 **scripts\docker** 文件夹。

1. 若要生成 docker 映像，请运行以下命令：

    ```cmd
    build.cmd
    ```

1. 若要登录到 Docker 中心帐户，请运行以下命令：

    ```cmd
    docker login
    ```

1. 若要将新映像上传到 Docker 中心帐户，请运行以下命令：

    ```cmd
    publish.cmd
    ```

1. 若要验证上传，请导航到 [https://hub.docker.com/](https://hub.docker.com/)。 找到 **lightbulb** 存储库并选择“详细信息”。 然后选择“标记”：

    ![Docker 中心](media/iot-suite-remote-monitoring-test/dockerhub.png)

    脚本已将 **testing** 标记添加到映像。

1. 使用 SSH 连接到 Azure 中的解决方案虚拟机。 然后导航到 **App** 文件夹并编辑 **docker-compose.yaml** 文件：

    ```sh
    cd /app
    sudo nano docker-compose.yaml
    ```

1. 编辑设备模拟服务对应的条目，以使用该 docker 映像：

    ```yaml
    devicesimulation:
      image: {your docker ID}/lightbulb:testing
    ```

    保存所做更改。

1. 若要重启所有采用新设置的服务，请运行以下命令：

    ```sh
    sudo ./start.sh
    ```

1. 若要检查新设备模拟容器中的日志文件，请运行以下命令找到容器 ID：

    ```sh
    docker ps
    ```

    然后使用容器 ID 运行以下命令：

    ```sh
    docker logs {container ID}
    ```

现已完成将设备模拟服务更新版本部署到远程监视解决方案的步骤。

在浏览器中，导航到远程监视解决方案的**仪表板**。 在**仪表板**上的遥测面板中，选择“温度”。 图表中将显示两个模拟设备的温度：

![温度遥测](media/iot-suite-remote-monitoring-test/telemetry.png)

在“设备”页上，可以预配新类型的实例：

![查看可用模拟的列表](media/iot-suite-remote-monitoring-test/devicesmodellist.png)

可以查看来自模拟设备的遥测数据：

![查看灯泡遥测数据](media/iot-suite-remote-monitoring-test/devicestelemetry.png)

可在设备上调用 **SwitchOn** 和 **SwitchOff** 方法：

![调用灯泡方法](media/iot-suite-remote-monitoring-test/devicesmethods.png)

## <a name="add-a-new-telemetry-type"></a>添加新的遥测类型

本部分介绍如何修改现有模拟设备类型，以支持新的遥测类型。

### <a name="locate-the-chiller-device-type-files"></a>找到冷却器设备类型文件

以下步骤说明如何查找用于定义内置**冷却器**设备的文件：

1. 使用以下命令将**设备模拟** GitHub 存储库克隆到本地计算机（如果尚未这样做）：

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. 每种设备类型都有一个 JSON 模型文件，并且在 `data/devicemodels` 文件夹中具有关联的脚本。 定义模拟**冷却器**设备类型的文件为：

    * **data/devicemodels/chiller-01.json**
    * **data/devicemodels/scripts/chiller-01-state.js**

### <a name="specify-the-new-telemetry-type"></a>指定新的遥测类型

以下步骤说明如何将新的**内部温度**类型添加到**冷却器**设备类型：

1. 打开 **chiller-01.json** 文件。

1. 如下所示更新 **SchemaVersion** 值：

    ```json
    "SchemaVersion": "1.1.0",
    ```

1. 在 **InitialState** 节中添加以下两个定义：

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. 在 **Telemetry** 数组中添加以下定义：

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. 保存 **chiller-01.json** 文件。

1. 打开 **scripts/chiller-01-state.js** 文件。

1. 将以下字段添加到 **state** 变量：

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. 将以下行添加到 **main** 函数：

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. 保存 **scripts/chiller-01-state.js** 文件。

### <a name="test-the-chiller-device-type"></a>测试冷却器设备类型

若要测试已更新的**冷却器**设备类型，请先运行 **device-simulation** 服务的本地副本，以测试设备类型的行为是否符合预期。 在本地测试并调试已更新的设备类型后，可以重新生成容器，并将**设备模拟**服务重新部署到 Azure。

在本地运行**设备模拟**服务时，该服务会将遥测数据发送到远程监视解决方案。 在“设备”页上，可以预配已更新类型的实例。

若要在本地测试和调试更改，请参阅上一部分[在本地测试灯泡设备类型](#test-the-lightbulb-device-type-locally)。

若要将更新的设备模拟服务部署到 Azure 中的解决方案虚拟机，请参阅上一部分[将更新的模拟器部署到云中](#deploy-the-updated-simulator-to-the-cloud)。

在“设备”页上，可以预配已更新类型的实例：

![添加已更新的冷却器](media/iot-suite-remote-monitoring-test/devicesupdatedchiller.png)

可以查看来自模拟设备的新**内部温度**遥测数据。

## <a name="next-steps"></a>后续步骤

本教程已介绍以下操作：

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 创建新设备类型
> * 模拟自定义设备行为
> * 将新设备类型添加到仪表板
> * 从现有的设备类型发送自定义遥测数据

现在，我们已了解如何自定义设备模拟服务。 我们建议接下来了解如何[将物理设备连接到远程监视解决方案](iot-suite-connecting-devices-node.md)。

有关可供开发人员参考的远程监视解决方案详细信息，请参阅：

* [开发人员参考指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [开发人员故障排除指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->