---
title: include 文件
description: include 文件
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 09/28/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 5eb3c08792b760bf66e443f79762d91210706c92
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435106"
---
在第一个方案中，我们将新的遥测类型添加到 Contoso 的现有“冷却器”设备类型。

在第二个方案中，Contoso 想要测试新的智能灯泡设备。 为了运行测试，我们创建了具有以下特征的新模拟设备：

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

方法

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

完成本操作方法指南中的步骤需要有效的 Azure 订阅。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites"></a>先决条件

按照本操作方法指南操作需要：

* Visual Studio Code。 可以[下载适用于 Mac、Linux 和 Windows 的 Visual Studio Code](https://code.visualstudio.com/download)。
* .NET Core。 可以下载[适用于 Mac、Linux 和 Windows 的 .NET Core](https://www.microsoft.com/net/download)。
* [适用于 Visual Studio Code 的 C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* Postman。 可以下载[适用于 Mac、Windows 或 Linux 的 Postman](https://www.getpostman.com/apps)。
* [部署到 Azure 订阅的 IoT 中心](../articles/iot-hub/iot-hub-create-through-portal.md)。 需要 IoT 中心的连接字符串才能完成本指南中的步骤。 从 Azure 门户可获取连接字符串。
* 使用 SQL API 且配置为[强一致性](../articles/cosmos-db/manage-account.md)的 Cosmos DB 数据库。 需要 Cosmos DB 数据库的连接字符串才能完成本指南中的步骤。 从 Azure 门户可获取连接字符串。

## <a name="prepare-your-development-environment"></a>准备开发环境

完成以下任务以准备开发环境：

* 下载设备模拟微服务的源。
* 下载存储适配器微服务的源。
* 在本地运行存储适配器微服务。

本文中的说明假定使用 Windows 操作系统。 如果使用另一操作系统，则可能需要调整某些文件路径和命令以适应环境。

### <a name="download-the-microservices"></a>下载微服务

从 GitHub 下载[远程监视微服务](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip)并将其解压缩到本地计算机上的适当位置。 本文假设此文件夹的名称为 **remote-monitoring-services-dotnet-master**。

从 GitHub 下载[设备模拟微服务](https://github.com/Azure/device-simulation-dotnet/archive/master.zip)并将其解压缩到本地计算机上的适当位置。 本文假设此文件夹的名称为 **device-simulation-dotnet-master**。

### <a name="run-the-storage-adapter-microservice"></a>运行存储适配器微服务

在 Visual Studio Code 中打开 remote-monitoring-services-dotnet-master\storage-adapter 文件夹。 单击任意“还原”按钮，修复任何未解决的依赖项。

打开 .vscode/launch.json 文件，并将 Cosmos DB 连接字符串分配给 PCS_STORAGEADAPTER_DOCUMENTDB_CONNSTRING 环境变量。

<!-- Open the **WebService/appsettings.ini** file and assign your Cosmos DB connection string to the **documentdb_connstring** configuration setting.-->

要在本地运行微服务，请单击“调试”>“启动调试”。

Visual Studio Code 中的“终端”窗口显示正在运行的微服务的输出，包括 Web 服务运行状况检查的 URL：[http://127.0.0.1:9022/v1/status](http://127.0.0.1:9022/v1/status)。 导航到此地址时，状态应显示为“正常: 活动且正常”。

在完成后续步骤时，让存储适配器微服务继续在 Visual Studio Code 的此实例中运行。

## <a name="modify-the-chiller"></a>修改冷却器

在本部分中，将新的“内部温度”遥测类型添加到现有“冷却器”设备类型：

1. 在本地计算机上创建一个新文件夹 C:\temp\devicemodelsc 中。

1. 将以下文件从设备模拟微服务的已下载副本复制到新文件夹：

    | Source | 目标 |
    | ------ | ----------- |
    | Services\data\devicemodels\chiller-01.json | C:\temp\devicemodels\chiller-01.json |
    | Services\data\devicemodels\scripts\chiller-01-state.js | C:\temp\devicemodels\scripts\chiller-01-state.js |
    | Services\data\devicemodels\scripts\Reboot-method.js | C:\temp\devicemodels\scripts\Reboot-method.js |
    | Services\data\devicemodels\scripts\FirmwareUpdate-method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-method.js |
    | Services\data\devicemodels\scripts\EmergencyValveRelease-method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-method.js |
    | Services\data\devicemodels\scripts\IncreasePressure-method.js | C:\temp\devicemodels\scripts\IncreasePressure-method.js |

1. 打开 C:\temp\devicemodels\chiller-01.json 文件。

1. 在 InitialState 部分中，添加以下两个定义：

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

1. 保存 C:\temp\devicemodels\chiller-01.json 文件。

1. 打开 C:\temp\devicemodels\scripts\chiller-01-state.js 文件。

1. 将以下字段添加到 **state** 变量：

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. 按如下所示更新 main 函数：

    ```js
    function main(context, previousState, previousProperties) {

        // Restore the global state before generating the new telemetry, so that
        // the telemetry can apply changes using the previous function state.
        restoreSimulation(previousState, previousProperties);

        // 75F +/- 5%,  Min 25F, Max 100F
        state.temperature = vary(75, 5, 25, 100);

        // 70% +/- 5%,  Min 2%, Max 99%
        state.humidity = vary(70, 5, 2, 99);

        // 65F +/- 2%,  Min 15F, Max 125F
        state.internal_temperature = vary(65, 2, 15, 125);

        log("Simulation state: " + state.simulation_state);
        if (state.simulation_state === "high_pressure") {
            // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
            state.pressure = vary(250, 25, 50, 300);
        } else {
            // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
            state.pressure = vary(150, 10, 50, 300);
        }

        updateState(state);
        return state;
    }
    ```

1. 保存 C:\temp\devicemodels\scripts\chiller-01-state.js 文件。

## <a name="create-the-lightbulb"></a>创建灯泡

在本部分中，定义新的“灯泡”设备类型：

1. 创建文件 C:\temp\devicemodels\lightbulb-01.json 并添加以下内容：

    ```json
    {
      "SchemaVersion": "1.0.0",
      "Id": "lightbulb-01",
      "Version": "0.0.1",
      "Name": "Lightbulb",
      "Description": "Smart lightbulb device.",
      "Protocol": "MQTT",
      "Simulation": {
        "InitialState": {
          "online": true,
          "temperature": 200.0,
          "temperature_unit": "F",
          "status": "on"
        },
        "Interval": "00:00:20",
        "Scripts": [
          {
            "Type": "javascript",
            "Path": "lightbulb-01-state.js"
          }
        ]
      },
      "Properties": {
        "Type": "Lightbulb",
        "Color": "White",
        "Brightness": 75,
        "EstimatedRemainingLife": 10000
      },
      "Tags": {
        "Location": "Building 2",
        "Floor": "2",
        "Campus": "Redmond"
      },
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
    }
    ```

    保存对 C:\temp\devicemodels\lightbulb-01.json 的更改。

1. 创建文件 C:\temp\devicemodels\scripts\lightbulb-01-state.js 并添加以下内容：

    ```javascript
    "use strict";

    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };

    // Default device properties
    var properties = {};

    /**
     * Restore the global state using data from the previous iteration.
     *
     * @param previousState device state from the previous iteration
     * @param previousProperties device properties from the previous iteration
     */
    function restoreSimulation(previousState, previousProperties) {
      // If the previous state is null, force a default state
      if (previousState) {
        state = previousState;
      } else {
        log("Using default state");
      }

      if (previousProperties) {
        properties = previousProperties;
      } else {
        log("Using default properties");
      }
    }

    /**
     * Simple formula generating a random value around the average
     * in between min and max
     *
     * @returns random value with given parameters
     */
    function vary(avg, percentage, min, max) {
      var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
      value = Math.max(value, min);
      value = Math.min(value, max);
      return value;
    }

    /**
     * Simple formula that sometimes flips the status of the lightbulb
     */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }

    /**
     * Entry point function called by the simulation engine.
     * Returns updated simulation state.
     * Device property updates must call updateProperties() to persist.
     *
     * @param context             The context contains current time, device model and id
     * @param previousState       The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState, previousProperties) {

      // Restore the global device properties and the global state before
      // generating the new telemetry, so that the telemetry can apply changes
      // using the previous function state.
      restoreSimulation(previousState, previousProperties);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      updateState(state);

      return state;
    }
    ```

    保存对 C:\temp\devicemodels\scripts\lightbulb-01-state.js 的更改。

1. 创建文件 C:\temp\devicemodels\scripts\SwitchOn-method.js 并添加以下内容：

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch On method.");
      state.status = "on";
      updateState(state);
    }
    ```

    保存对 C:\temp\devicemodels\scripts\SwitchOn-method.js 的更改。

1. 创建文件 C:\temp\devicemodels\scripts\SwitchOff-method.js 并添加以下内容：

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch Off method.");
      state.status = "off";
      updateState(state);
    }
    ```

    保存对 C:\temp\devicemodels\scripts\SwitchOff-method.js 的更改。

现在已创建自定义版本的“冷却器”设备类型和新的“灯泡”设备类型。

## <a name="test-the-devices"></a>测试设备

在本部分中，测试你在之前部分在本地创建的设备类型。

### <a name="run-the-device-simulation-microservice"></a>运行设备模拟微服务

在 Visual Studio Code 的新实例中打开从 GitHub 下载的 device-simulation-dotnet-master 文件夹。 单击任意“还原”按钮，修复任何未解决的依赖项。

打开 .vscode/launch.json 文件，将 IoT 中心连接字符串分配给 PCS_IOTHUB_CONNSTRING 环境变量。 在同一文件中，添加 **PCS_STORAGEADAPTER_DOCUMENTDB_CONNSTRING** 环境变量，并为 Cosmos DB 数据库分配连接字符串。

打开 WebService/Properties/launchSettings.json 文件，将 IoT 中心连接字符串分配给 PCS_IOTHUB_CONNSTRING 环境变量。

按如下所示打开 WebService/appsettings.ini 文件并修改设置：

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

要在本地运行微服务，请单击“调试”>“启动调试”。

Visual Studio Code 中的“终端”窗口显示正在运行的微服务的输出。

在完成后续步骤时，让设备模拟微服务继续在 Visual Studio Code 的此实例中运行。

### <a name="set-up-a-monitor-for-device-events"></a>设置设备事件的监视器

在本部分中，使用 Azure CLI 设置事件监视器，以查看从连接到 IoT 中心的设备发送的遥测。

以下脚本假定 IoT 中心的名称为 device-simulation-test。

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

测试模拟设备时，让事件监视器继续运行。

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>使用更新的冷却器设备类型创建模拟

在本部分中，使用 Postman 工具请求设备模拟微服务利用更新的冷却器设备类型运行模拟。 Postman 是一种可实现将 REST 请求发送到 Web 服务的工具。 所需的 Postman 配置文件位于 device-simulation-dotnet 存储库的本地副本中。

要设置 Postman，请执行以下操作：

1. 在本地计算机上打开 Postman。

1. 单击“文件”>“导入”。 然后单击“选择文件”。

1. 导航到 device-simulation-dotnet-master/docs/postman 文件夹。 选择“Azure IoT 设备模拟解决方案 accelerator.postman_collection”和“Azure IoT 设备模拟解决方案 accelerator.postman_environment”，然后单击“打开”。

1. 将“Azure IoT 设备模拟解决方案加速器”展开到可以发送的请求。

1. 单击“无环境”，然后选择“Azure IoT 设备模拟解决方案加速器”。

现在，已在 Postman 工作区中加载可用于与设备模拟微服务进行交互的集合和环境。

要配置和运行模拟，请执行以下操作：

1. 在 Postman 集合中，选择“创建修改的冷却器模拟”并单击“发送”。 此请求将创建模拟的冷却器设备类型的四个实例。

1. Azure CLI 窗口中的事件监视器输出显示来自模拟设备的遥测，包括新的 internal_temperature 值。

要停止模拟，请选择 Postman 中的“停止模拟”请求并单击“发送”。

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>使用灯泡设备类型创建模拟

在本部分中，使用 Postman 工具请求设备模拟微服务利用灯泡设备类型运行模拟。 Postman 是一种可实现将 REST 请求发送到 Web 服务的工具。

要配置和运行模拟，请执行以下操作：

1. 在 Postman 集合中，选择“创建灯泡模拟”并单击“发送”。 此请求将创建模拟的灯泡设备类型的两个实例。

1. Azure CLI 窗口中的事件监视器输出显示来自模拟灯泡的遥测。

要停止模拟，请选择 Postman 中的“停止模拟”请求并单击“发送”。

## <a name="clean-up-resources"></a>清理资源

在本地运行的微服务的 Visual Studio Code 实例中可停止这两个本地运行的微服务（“调试”>“停止调试”）。

如果不再需要 IoT 中心和 Cosmos DB 实例，请从 Azure 订阅中将其删除，以避免产生任何不必要的费用。