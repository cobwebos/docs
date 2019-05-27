---
title: 自定义 Java 模块教程 - Azure IoT Edge | Microsoft Docs
description: 本教程介绍如何使用 Java 代码创建 IoT Edge 模块并将其部署到边缘设备。
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 7f659240e7de729c6f64acf41d76530475fb810f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64569474"
---
# <a name="tutorial-develop-a-java-iot-edge-module-for-linux-devices"></a>教程：开发适用于 Linux 设备的 Java IoT Edge 模块

可以使用 Azure IoT Edge 模块部署代码，直接将业务逻辑实现到 IoT Edge 设备。 本教程详细介绍如何创建并部署用于筛选传感器数据的 IoT Edge 模块。 将使用的模拟 IoT Edge 设备是在 [Linux](quickstart-linux.md) 快速入门的“在模拟设备上部署 Azure IoT Edge”中创建的。 本教程介绍如何执行下列操作：    

> [!div class="checklist"]
> * 使用 Visual Studio Code，根据 Azure IoT Edge maven 模板包和 Azure IoT Java 设备 SDK 创建 IoT Edge Java 模块。
> * 使用 Visual Studio Code 和 Docker 创建 Docker 映像并将其发布到注册表。
> * 将模块部署到 IoT Edge 设备。
> * 查看生成的数据。


在本教程中创建的 IoT Edge 模块可以筛选由设备生成的温度数据。 它只在温度高于指定阈值的情况下，向上游发送消息。 在边缘进行的此类分析适用于减少传递到云中和存储在云中的数据量。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>解决方案范围

本教程演示如何使用 **Visual Studio Code** 以 **Java** 开发模块，以及如何将其部署到 **Linux 设备**。 IoT Edge 不支持 Windows 设备的 Java 模块。 

使用下表了解用于开发和部署 Java 模块的选项： 

| Java | Visual Studio Code | Visual Studio 2017 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![使用 VS Code 开发 Linux AMD64 上的 Java 模块](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![使用 VS Code 开发 Linux ARM32 上的 Java 模块](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前，应已完成上一篇教程，了解如何设置用于开发 Linux 容器的开发环境：[开发适用于 Linux 设备的 IoT Edge 模块](tutorial-develop-for-linux.md)。 完成其中的一篇教程后，应已准备好以下必备组件： 

* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* 一个[运行 Azure IoT Edge 的 Linux 设备](quickstart-linux.md)
* 一个容器注册表，例如 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)。
* 配置了 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 的 [Visual Studio Code](https://code.visualstudio.com/)。
* 配置为运行 Linux 容器的 [Docker CE](https://docs.docker.com/install/)。

若要开发以 Java 编写的 IoT Edge 模块，请在开发计算机上安装下述额外的必备组件： 

* 适用于 Visual Studio Code 的 [Java 扩展包](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)。
* [Java SE 开发工具包 10](https://aka.ms/azure-jdks)。请[将 `JAVA_HOME` 环境变量](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/)设置为指向 JDK 安装项目。
* [Maven](https://maven.apache.org/)

## <a name="create-a-module-project"></a>创建模块项目
以下步骤创建基于 Azure IoT Edge maven 模板包和 Azure IoT Java 设备 SDK 的 IoT Edge 项目。 使用 Visual Studio Code 和 Azure IoT 工具创建该项目。

### <a name="create-a-new-project"></a>创建新项目

创建可以使用你自己的代码进行自定义的 Java 解决方案模板。 

1. 在 Visual Studio Code 中，选择“查看” > “命令面板”，以打开 VS Code 命令面板。 

2. 在命令面板中，输入并运行“Azure IoT Edge: **New IoT Edge solution** 命令。 按命令面板中的提示创建解决方案。

   | 字段 | 值 |
   | ----- | ----- |
   | 选择文件夹 | 在适用于 VS Code 的开发计算机上选择用于创建解决方案文件的位置。 |
   | 提供解决方案名称 | 输入解决方案的描述性名称，或者接受默认的 **EdgeSolution**。 |
   | 选择模块模板 | 选择“Java 模块”。 |
   | 为 groupId 提供值 | 输入组 ID 值或接受默认的 **com.edgemodule**。 |
   | 提供模块名称 | 将模块命名为 **JavaModule**。 |
   | 为模块提供 Docker 映像存储库 | 映像存储库包含容器注册表的名称和容器映像的名称。 容器映像是基于你在上一步中提供的名称预先填充的。 将 **localhost:5000** 替换为 Azure 容器注册表中的登录服务器值。 可以在 Azure 门户的容器注册表的“概览”页中检索登录服务器。 <br><br>最终的映像存储库看起来类似于 \<registry name\>.azurecr.io/javamodule。 |
 
   ![提供 Docker 映像存储库](./media/tutorial-java-module/repository.png)
   
如果是第一次创建 Java 模块，则下载 maven 包可能需要数分钟。 然后，VS Code 窗口会加载 IoT Edge 解决方案工作区。 解决方案工作区包含五个顶级组件。 **modules** 文件夹包含模块的 Java 代码以及用于将模块构建为容器映像的 Dockerfile。 **\.env** 文件存储容器注册表凭据。 **deployment.template.json** 文件包含 IoT Edge 运行时用于在设备上部署模块的信息。 **deployment.debug.template.json** 文件包含模块的调试版本。 你不会在本教程中编辑 **\.vscode** 文件夹或 **\.gitignore** 文件。 

如果在创建解决方案时未指定容器注册表，但接受了默认的 localhost:5000 值，则不会有 \.env 文件。 

### <a name="add-your-registry-credentials"></a>添加注册表凭据

环境文件存储容器注册表的凭据，并将其与 IoT Edge 运行时共享。 此运行时需要这些凭据才能将专用映像拉取到 IoT Edge 设备中。 

1. 在 VS Code 资源管理器中，打开 .env 文件。 
2. 使用从 Azure 容器注册表复制的 **username** 和 **password** 值更新相关字段。 
3. 保存此文件。 

### <a name="select-your-target-architecture"></a>选择目标体系结构

目前，Visual Studio Code 可以开发适用于 Linux AMD64 和 Linux ARM32v7 设备的 Java 模块。 需要选择面向每个解决方案的体系结构，因为每种体系结构类型的容器的生成和运行方式均不相同。 默认设置为 Linux AMD64。 

1. 打开命令面板并搜索 **Azure IoT Edge:Set Default Target Platform for Edge Solution**，或者选择窗口底部边栏中的快捷方式图标。 

2. 在命令面板中，从选项列表中选择目标体系结构。 本教程将使用 Ubuntu 虚拟机作为 IoT Edge 设备，因此将保留默认设置 **amd64**。 

### <a name="update-the-module-with-custom-code"></a>使用自定义代码更新模块

1. 在 VS Code 资源管理器中，打开 **modules** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java**。

2. 在该文件顶部添加以下代码，以便导入新的引用的类。

    ```java
    import java.io.StringReader;
    import java.util.concurrent.atomic.AtomicLong;
    import java.util.HashMap;
    import java.util.Map;
    
    import javax.json.Json;
    import javax.json.JsonObject;
    import javax.json.JsonReader;
    
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Pair;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Property;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.TwinPropertyCallBack;
    ```

3. 将以下定义添加到 **App** 类中。 此变量设置温度阈值。 在测量的机器温度超过此值之前，不会向 IoT 中心报告此温度。 

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

4. 将 **MessageCallbackMqtt** 执行方法替换为以下代码。 每当模块从 IoT Edge 中心接收 MQTT 消息，就会调用此方法。 此方法筛选掉那些所报告温度低于温度阈值（通过孪生模块进行设置）的消息。

    ```java
    protected static class MessageCallbackMqtt implements MessageCallback {
        private int counter = 0;
        @Override
        public IotHubMessageResult execute(Message msg, Object context) {
            this.counter += 1;
 
            String msgString = new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET);
            System.out.println(
                   String.format("Received message %d: %s",
                            this.counter, msgString));
            if (context instanceof ModuleClient) {
                try (JsonReader jsonReader = Json.createReader(new StringReader(msgString))) {
                    final JsonObject msgObject = jsonReader.readObject();
                    double temperature = msgObject.getJsonObject("machine").getJsonNumber("temperature").doubleValue();
                    long threshold = App.tempThreshold.get();
                    if (temperature >= threshold) {
                        ModuleClient client = (ModuleClient) context;
                        System.out.println(
                            String.format("Temperature above threshold %d. Sending message: %s",
                            threshold, msgString));
                        client.sendEventAsync(msg, eventCallback, msg, App.OUTPUT_NAME);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return IotHubMessageResult.COMPLETE;
        }
    }
    ```

5. 将下面的两个静态内部类添加到 **App** 类中。 当模块孪生的所需属性发生更改时，这些类将更新 tempThreshold 变量。 所有模块都有自己的孪生模块，因此可以直接从云配置在模块中运行的代码。

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
 
    protected static class OnProperty implements TwinPropertyCallBack {
        @Override
        public void TwinPropertyCallBack(Property property, Object context) {
            if (!property.getIsReported()) {
                if (property.getKey().equals(App.TEMP_THRESHOLD)) {
                    try {
                        long threshold = Math.round((double) property.getValue());
                        App.tempThreshold.set(threshold);
                    } catch (Exception e) {
                        System.out.println("Faile to set TemperatureThread with exception");
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

6. 将以下行添加到 **main** 方法的 **client.open()** 后面，以便订阅模块孪生更新。

    ```java
    client.startTwin(new DeviceTwinStatusCallBack(), null, new OnProperty(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> onDesiredPropertyChange = new HashMap<Property, Pair<TwinPropertyCallBack, Object>>() {
        {
            put(new Property(App.TEMP_THRESHOLD, null), new Pair<TwinPropertyCallBack, Object>(new OnProperty(), null));
        }
    };
    client.subscribeToTwinDesiredProperties(onDesiredPropertyChange);
    client.getTwin();
    ```

7. 保存 App.java 文件。

8. 在 VS Code 资源管理器的 IoT Edge 解决方案工作区中打开 **deployment.template.json** 文件。

9. 将 **JavaModule** 模块孪生添加到部署清单。 在 **moduleContent** 节底部的 **$edgeHub** 模块孪生后面插入以下 JSON 内容： 

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![将模块孪生添加到部署模板](./media/tutorial-java-module/module-twin.png)

10. 保存 deployment.template.json 文件。

## <a name="build-and-push-your-module"></a>生成并推送模块

在上一部分，你已经创建了一个 IoT Edge 解决方案并将代码添加到了 **JavaModule**，该函数会筛选出其中报告的计算机温度低于可接受限制的消息。 现在，请将解决方案生成为容器映像并将其推送到容器注册表。 

1. 打开 VS Code 集成终端，方法是选择“视图” > “终端”。

1. 在终端中输入以下命令，以登录到 Docker。 使用 Azure 容器注册表中的用户名、密码和登录服务器登录。 可以在 Azure 门户中从注册表的“访问密钥”部分检索这些值。
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   可能会出现一条安全警告，其中建议使用 `--password-stdin`。 这条最佳做法是针对生产场景建议的，这超出了本教程的范畴。 有关详细信息，请参阅 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 参考。

2. 在 VS Code 资源管理器中右键单击“deployment.template.json”文件，然后选择“生成并推送 IoT Edge 解决方案”。

   “生成并推送”命令会启动三项操作。 首先，它在解决方案中创建名为 **config** 的新文件夹，用于保存基于部署模板和其他解决方案文件中的信息生成的完整部署清单。 其次，它会运行 `docker build`，以基于目标体系结构的相应 dockerfile 生成容器映像。 然后，它会运行 `docker push`，以将映像存储库推送到容器注册表。

## <a name="deploy-modules-to-device"></a>将模块部署到设备

使用 Visual Studio Code 资源管理器和 Azure IoT Tools 扩展将模块项目部署到 IoT Edge 设备。 你已经为方案准备了部署清单，即 config 文件夹中的 **deployment.json** 文件。 现在需要做的就是选择一个设备来接收部署。

请确保 IoT Edge 设备已启动并正在运行。

1. 在 Visual Studio Code 资源管理器中展开“Azure IoT 中心设备”部分，查看 IoT 设备的列表。

2. 右键单击 IoT Edge 设备的名称，然后选择“为单个设备创建部署”。

3. 选择 **config** 文件夹中的 **deployment.json** 文件，然后单击“选择 Edge 部署清单”。 不要使用 deployment.template.json 文件。

4. 单击“刷新”按钮。 此时会看到新的 **JavaModule** 在运行，此外还有 **TempSensor** 模块以及 **$edgeAgent** 和 **$edgeHub** 在运行。  

## <a name="view-generated-data"></a>查看生成的数据

将部署清单应用到 IoT Edge 设备以后，设备上的 IoT Edge 运行时就会收集新的部署信息并开始在其上执行操作。 在设备上运行的未包括在部署清单中的任何模块都会停止。 设备中缺失的任何模块都会启动。

可以通过 Visual Studio Code 资源管理器的“Azure IoT 中心设备”部分查看 IoT Edge 设备的状态。 展开设备的详细信息，可以看到已部署的正在运行的模块的列表。

1. 在 Visual Studio Code 资源管理器中右键单击 IoT Edge 设备的名称，选择“开始监视 D2C 消息”。

2. 查看抵达 IoT 中心的消息。 消息可能需要在一段时间后才会抵达，因为 IoT Edge 设备必须接收其新部署并启动所有模块。 然后，我们对 JavaModule 代码所做的更改将等到计算机温度达到 25 度时才发送消息。 IoT 中心还会将消息类型“警报”添加到达到该温度阈值的任何消息。 

## <a name="edit-the-module-twin"></a>编辑模块孪生

我们已使用部署清单中的 JavaModule 模块孪生将温度阈值设置为 25 度。 可以使用模块孪生来更改功能，而无需更新模块代码。

1. 在 Visual Studio Code 中，展开 IoT Edge 设备下的详细信息以查看正在运行的模块。 

2. 右键单击“JavaModule”并选择“编辑模块孪生”。 

3. 在所需属性中找到 **TemperatureThreshold**。 将其值更改为比上次报告的温度高出 5 到 10 度的新温度。 

4. 保存模块孪生文件。

5. 右键单击模块孪生编辑窗格中的任意位置，然后选择“更新模块孪生”。 

5. 监视传入的设备到云消息。 应会看到，在达到新的温度阈值之前，消息会停止发送。 
 
## <a name="clean-up-resources"></a>清理资源 

如果打算继续学习下一篇建议的文章，可以保留已创建的资源和配置，以便重复使用。 还可以继续使用相同的 IoT Edge 设备作为测试设备。 

否则，可以删除本文中创建的本地配置和 Azure 资源，以避免收费。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，创建了 IoT Edge 模块，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 准备好生成自己的模块时，可以详细了解如何[开发自己的 IoT Edge 模块](module-development.md)或如何[使用 Visual Studio Code 开发模块](how-to-vs-code-develop-module.md)。 可以继续学习后续教程，了解 Azure IoT Edge 如何帮助你部署 Azure 云服务，以在边缘位置处理和分析数据。

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [流分析](tutorial-deploy-stream-analytics.md)
> [机器学习](tutorial-deploy-machine-learning.md)
> [自定义视觉服务](tutorial-deploy-custom-vision.md)
