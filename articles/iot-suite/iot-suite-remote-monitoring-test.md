---
title: "远程监视解决方案中的设备模拟 - Azure | Microsoft Docs"
description: "本教程介绍如何在远程监视预配置解决方案中使用设备模拟器。"
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 11/10/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 8b84b90e72f8cac1fc1f8a90391b7a5a4f6be1f4
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2017
---
# <a name="test-your-solution-with-simulated-devices"></a>使用模拟设备测试解决方案

本教程介绍如何在远程监视预配置解决方案中使用和自定义设备模拟器微服务。 为了演示设备模拟器的功能，本教程在 Contoso IoT 应用程序中使用了两个方案。

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

在第二个方案中，我们将新的遥测类型添加到 Contoso 的现有**冷却器**设备。

本教程将介绍如何在远程监视预配置解决方案中使用设备模拟器：

本教程介绍如何执行下列操作：

>[!div class="checklist"]
> * 创建新设备类型
> * 模拟自定义设备行为
> * 将新设备类型添加到仪表板
> * 从现有的设备类型发送自定义遥测数据

## <a name="prerequisites"></a>先决条件

若要遵循本教程，需在 Azure 订阅中部署远程监视解决方案的实例。

如果尚未部署远程监视解决方案，应完成[部署远程监视预配置解决方案](iot-suite-remote-monitoring-deploy.md)教程。

<!-- Dominic please this use as your reference https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models -->

## <a name="the-device-simulation-service"></a>设备模拟服务

使用预配置解决方案中的设备模拟服务可对内置的模拟设备类型进行更改，以及创建新的模拟设备类型。 将物理设备连接到解决方案之前，可以使用自定义设备类型测试远程监视解决方案的行为。

## <a name="create-a-simulated-device-type"></a>创建模拟设备类型

在模拟微服务中创建新设备类型的最简单方法是复制并修改现有类型。 以下步骤说明如何复制内置的**冷却器**设备以创建新的**灯泡**设备：

1. 使用以下命令将**设备模拟** GitHub 存储库克隆到本地计算机：

    ```cmd/sh
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

1. 每种设备类型都有一个 JSON 模型文件，并且在 `Services/data/devicemodels` 文件夹中具有关联的脚本。 如下表中所示，复制**冷却器**文件以创建**灯泡**文件：

    | 源                      | 目标                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | scripts/chiller-01-state.js | scripts/lightbulb-01-state.js |
    | scripts/reboot-method.js    | scripts/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>定义新设备类型的特征

`lightbulb-01.json` 文件定义类型的特征，例如，设备生成的遥测数据以及设备支持的方法。 以下步骤更新 `lightbulb-01.json` 文件以定义**灯泡**设备：

1. 在 `lightbulb-01.json` 文件中，如以下片段中所示更新设备元数据：

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. 在 `lightbulb-01.json` 文件中，如以下片段中所示更新模拟定义：

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "status": "on"
      },
      "Script": {
        "Type": "javascript",
        "Path": "lightbulb-01-state.js",
        "Interval": "00:00:20"
      }
    },
    ```

1. 在 `lightbulb-01.json` 文件中，如以下片段中所示更新设备类型属性：

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. 在 `lightbulb-01.json` 文件中，如以下片段中所示更新设备类型遥测定义：

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "status": "text"
          }
        }
      }
    ],
    ```

1. 在 `lightbulb-01.json` 文件中，如以下片段中所示更新设备类型方法：

    ```json
    "CloudToDeviceMethods": {
      "SwitchOn": {
        "Type": "javascript",
        "Path": "SwitchOn-method.js"
      },
      "SwitchOff": {
        "Type": "javascript",
        "Path": "SwitchOff-method.js"
      },
    }
    ```

1. 保存 `lightbulb-01.json` 文件。

### <a name="simulate-custom-device-behavior"></a>模拟自定义设备行为

`scripts/lightbulb-01-state.js` 文件定义 **Lightbulb** 类型的模拟行为。 以下步骤更新 `scripts/lightbulb-01-state.js` 文件，以定义**灯泡**设备的行为：

1. 在 `scripts/lightbulb-01-state.js` 文件中，如以下片段中所示编辑状态定义：

    ```js
    // Default state
    var state = {
      online: true,
      status: "on"
    };
    ```

1. 将 **vary** 函数替换为以下 **flip** 函数：

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

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. 保存 `scripts/lightbulb-01-state.js` 文件。

`scripts/SwitchOn-method.js` 文件在**灯泡**设备中实现 **SwitchOn** 方法。 以下步骤更新 `scripts/SwitchOn-method.js` 文件：

1. 在 `scripts/SwitchOn-method.js` 文件中，如以下片段中所示编辑状态定义：

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

1. 保存 `scripts/SwitchOn-method.js` 文件。

1. 创建名为 `scripts/SwitchOff-method.js` 的 `scripts/SwitchOn-method.js` 文件副本。

1. 若要关闭灯泡，请在 `scripts/SwitchOff-method.js` 文件中按如下所示编辑 **main** 函数：

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. 保存 `scripts/SwitchOff-method.js` 文件。

### <a name="test-the-lightbulb-device-type"></a>测试灯泡设备类型

若要测试**灯泡**设备类型，可以首先通过运行**设备模拟**服务的本地副本来测试设备类型的行为是否符合预期。 在本地测试并调试新设备类型后，可以重新生成容器，并将**设备模拟**服务重新部署到 Azure。

若要在本地测试和调试更改，请参阅[设备模拟概述](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md)。

配置项目，将新的“灯泡”设备文件复制到输出目录：

* 如果使用 Visual Studio，请确保将上一部分中创建的三个新的灯泡文件复制到解决方案中的“服务”项目。 然后使用**解决方案资源管理器**将其标记为要复制到输出目录。

* 如果使用 Visual Studio Code，请打开 **Services.csproj** 文件并添加在上一部分中创建的三个新的灯泡文件。 请参阅 **Services.csproj** 文件中的现有设备模型文件条目作为示例。

若要在已部署的解决方案中测试新设备，请参阅以下文档之一：

* [从自定义的 Docker 中心帐户部署容器](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account)
* [通过手动复制更新已部署的容器](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy)

在“设备”页上，可以预配新类型的实例：

![查看可用模拟的列表](media/iot-suite-remote-monitoring-test/devicesmodellist.png)

可以查看来自模拟设备的遥测数据：

![查看灯泡遥测数据](media/iot-suite-remote-monitoring-test/devicestelemetry.png)

可在设备上调用 **SwitchOn** 和 **SwitchOff** 方法：

![调用灯泡方法](media/iot-suite-remote-monitoring-test/devicesmethods.png)

若要使用新设备类型生成可部署到 Azure 的 Docker 映像，请参阅[生成自定义的 Docker 映像](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image)。

## <a name="add-a-new-telemetry-type"></a>添加新的遥测类型

本部分介绍如何修改现有模拟设备类型，以支持新的遥测类型。

### <a name="locate-the-chiller-device-type-files"></a>找到冷却器设备类型文件

以下步骤说明如何查找用于定义内置**冷却器**设备的文件：

1. 使用以下命令将**设备模拟** GitHub 存储库克隆到本地计算机（如果尚未这样做）：

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. 每种设备类型都有一个 JSON 模型文件，并且在 `data/devicemodels` 文件夹中具有关联的脚本。 定义模拟**冷却器**设备类型的文件为：
    * `data/devicemodels/chiller-01.json`
    * `data/devicemodels/scripts/chiller-01-state.js`

### <a name="specify-the-new-telemetry-type"></a>指定新的遥测类型

以下步骤说明如何将新的**内部温度**类型添加到**冷却器**设备类型：

1. 打开 `chiller-01.json` 文件。

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

1. 保存 `chiller-01.json` 文件。

1. 打开 `scripts/chiller-01-state.js` 文件。

1. 将以下字段添加到 **state** 变量：

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. 将以下行添加到 **main** 函数：

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. 保存 `scripts/chiller-01-state.js` 文件。

### <a name="test-the-chiller-device-type"></a>测试冷却器设备类型

若要测试已更新的**冷却器**设备类型，可以首先通过运行**设备模拟**服务的本地副本来测试设备类型的行为是否符合预期。 在本地测试并调试已更新的设备类型后，可以重新生成容器，并将**设备模拟**服务重新部署到 Azure。

在本地运行**设备模拟**服务时，该服务会将遥测数据发送到远程监视解决方案。 在“设备”页上，可以预配已更新类型的实例。

若要在本地测试和调试更改，请参阅[使用 Visual Studio 运行服务](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#running-the-service-with-visual-studio)或[从命令行生成和运行](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#build-and-run-from-the-command-line)。

若要在已部署的解决方案中测试新设备，请参阅以下文档之一：

* [从自定义的 Docker 中心帐户部署容器](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account)
* [通过手动复制更新已部署的容器](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy)

在“设备”页上，可以预配已更新类型的实例：

![添加已更新的冷却器](media/iot-suite-remote-monitoring-test/devicesupdatedchiller.png)

可以查看来自模拟设备的新**内部温度**遥测数据。

若要使用新设备类型生成可部署到 Azure 的 Docker 映像，请参阅[生成自定义的 Docker 映像](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image)。

## <a name="next-steps"></a>后续步骤

本教程已介绍以下操作：

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 创建新设备类型
> * 模拟自定义设备行为
> * 将新设备类型添加到仪表板
> * 从现有的设备类型发送自定义遥测数据

了解如何使用设备模拟服务后，我们建议接下来了解如何[将物理设备连接到远程监视解决方案](iot-suite-connecting-devices-node.md)。

有关可供开发人员参考的远程监视解决方案详细信息，请参阅：

* [开发人员参考指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [开发人员故障排除指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->