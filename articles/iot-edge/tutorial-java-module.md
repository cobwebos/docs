---
title: 教程：创建自定义 Java 模块 - Azure IoT Edge | Microsoft Docs
description: 本教程介绍如何使用 Java 代码创建 IoT Edge 模块并将其部署到边缘设备。
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 9a541f42670b3ccf83331e3e2e9069289bb9b4b3
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224067"
---
# <a name="tutorial-develop-a-java-iot-edge-module-and-deploy-to-your-simulated-device"></a>教程：开发 Java IoT Edge 模块并将其部署到模拟设备

可以使用 Azure IoT Edge 模块部署代码，直接将业务逻辑实现到 IoT Edge 设备。 本教程详细介绍如何创建并部署用于筛选传感器数据的 IoT Edge 模块。 将使用的模拟 IoT Edge 设备是在 [Windows](quickstart.md) 或 [Linux](quickstart-linux.md) 快速入门的“在模拟设备上部署 Azure IoT Edge”中创建的。 本教程介绍如何执行下列操作：    

> [!div class="checklist"]
> * 使用 Visual Studio Code，根据 Azure IoT Edge maven 模板包和 Azure IoT Java 设备 SDK 创建 IoT Edge Java 模块。
> * 使用 Visual Studio Code 和 Docker 创建 Docker 映像并将其发布到注册表。
> * 将模块部署到 IoT Edge 设备。
> * 查看生成的数据。


在本教程中创建的 IoT Edge 模块可以筛选由设备生成的温度数据。 它只在温度高于指定阈值的情况下，向上游发送消息。 在边缘进行的此类分析适用于减少传递到云中和存储在云中的数据量。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>先决条件

Azure IoT Edge 设备：

* 可以遵循适用于 [Linux](quickstart-linux.md) 或 [Windows](quickstart.md) 的快速入门中的步骤来设置 IoT Edge 设备。
* 对于 Windows 设备上的 IoT Edge，版本 1.0.5 不支持 Java 模块。 有关详细信息，请参阅 [1.0.5 发行说明](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5)。 有关如何安装特定版本的步骤，请参阅[更新 IoT Edge 安全守护程序和运行时](how-to-update-iot-edge.md)。

云资源：

* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。 

开发资源：

* [Visual Studio Code](https://code.visualstudio.com/)。 
* 适用于 Visual Studio Code 的 [Java 扩展包](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)。
* 适用于 Visual Studio Code 的 [Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
* [Java SE 开发工具包 10](https://aka.ms/azure-jdks)。请[将 `JAVA_HOME` 环境变量](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/)设置为指向 JDK 安装项目。
* [Maven](https://maven.apache.org/)
* [Docker CE](https://docs.docker.com/install/)
   * 如果是在 Windows 设备上进行开发，请确保将 Docker [配置为使用 Linux 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)。 


## <a name="create-a-container-registry"></a>创建容器注册表

本教程将使用适用于 Visual Studio Code 的 Azure IoT 工具来生成模块并从文件创建**容器映像**。 然后将该映像推送到用于存储和管理映像的**注册表**。 最后，从注册表部署在 IoT Edge 设备上运行的映像。  

可以使用任意兼容 Docker 的注册表来保存容器映像。 两个常见 Docker 注册表服务分别是 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)和 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)。 本教程使用 Azure 容器注册表。 

如果还没有容器注册表，请执行以下步骤，以便在 Azure 中创建一个新的：

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源” > “容器” > “容器注册表”。

2. 提供以下值，以便创建容器注册表：

   | 字段 | 值 | 
   | ----- | ----- |
   | 注册表名称 | 提供唯一名称。 |
   | 订阅 | 从下拉列表中选择“订阅”。 |
   | 资源组 | 为了简化管理，请对在 IoT Edge 快速入门和教程中创建的所有测试资源使用同一资源组。 例如，**IoTEdgeResources**。 |
   | 位置 | 选择靠近你的位置。 |
   | 管理员用户 | 设置为“启用”。 |
   | SKU | 选择“基本”。 | 

5. 选择“创建”。

6. 创建容器注册表后，请浏览到其中，然后选择“访问密钥”。 

7. 复制“登录服务器”、“用户名”和“密码”的值。 本教程后面会用到这些值来访问容器注册表。 

## <a name="create-an-iot-edge-module-project"></a>创建 IoT Edge 模块项目
以下步骤创建基于 Azure IoT Edge maven 模板包和 Azure IoT Java 设备 SDK 的 IoT Edge 项目。 使用 Visual Studio Code 和 Azure IoT 工具创建该项目。

### <a name="create-a-new-solution"></a>创建新的解决方案

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

### <a name="update-the-module-with-custom-code"></a>使用自定义代码更新模块

1. 在 VS Code 资源管理器中，打开 **modules** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java**。

5. 在该文件顶部添加以下代码，以便导入新的引用的类。

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

5. 将以下定义添加到 **App** 类中。 此变量设置温度阈值。 在测量的机器温度超过此值之前，不会向 IoT 中心报告此温度。 

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

7. 将 **MessageCallbackMqtt** 执行方法替换为以下代码。 每当模块从 IoT Edge 中心接收 MQTT 消息，就会调用此方法。 此方法筛选掉那些所报告温度低于温度阈值（通过孪生模块进行设置）的消息。

    ```java
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
    ```

8. 将下面的两个静态内部类添加到 **App** 类中。 当模块孪生的所需属性发生更改时，这些类将更新 tempThreshold 变量。 所有模块都有自己的孪生模块，因此可以直接从云配置在模块中运行的代码。

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

9. 将以下行添加到 **main** 方法的 **client.open()** 后面，以便订阅模块孪生更新。

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

11. 保存 App.java 文件。

12. 在 VS Code 资源管理器的 IoT Edge 解决方案工作区中打开 **deployment.template.json** 文件。 此文件告知 IoT Edge 代理部署哪些模块（在本例中为 **tempSensor** 和 **JavaModule**），并告知 IoT Edge 中心如何在它们之间路由消息。 Visual Studio Code 扩展会自动填充部署模板中所需的大部分信息，但确保解决方案的所有内容都是准确的： 

   1. 在 VS Code 状态栏中将 IoT Edge 的默认平台设置为 **amd64**，这意味着将 **JavaModule** 设置为映像的 Linux amd64 版本。 在状态栏中将默认平台从 **amd64** 更改为 **arm32v7** 或 **windows-amd64**（如果这就是 IoT Edge 设备的体系结构）。 

      ![更新模块映像平台](./media/tutorial-java-module/image-platform.png)

   2. 验证该模板具有正确的模块名称，而不是具有在创建 IoT Edge 解决方案时你更改的默认 **SampleModule** 名称。

   3. **registryCredentials** 节会存储 Docker 注册表凭据，以便 IoT Edge 代理可以拉取模块映像。 实际的用户名和密码对存储在 git 忽略的 .env 文件中。 将凭据添加到 .env 文件中（如果尚未这样做）。  

   4. 如果想要了解有关部署清单的更多信息，请参阅[了解如何在 IoT Edge 中部署模块和建立路由](module-composition.md)。

13. 将 **JavaModule** 模块孪生添加到部署清单。 在 **moduleContent** 节底部的 **$edgeHub** 模块孪生后面插入以下 JSON 内容： 

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![将模块孪生添加到部署模板](./media/tutorial-java-module/module-twin.png)

14. 保存 deployment.template.json 文件。

## <a name="build-your-iot-edge-solution"></a>生成 IoT Edge 解决方案

在上一部分，你已经创建了一个 IoT Edge 解决方案并将代码添加到了 **JavaModule**，该函数会筛选出其中报告的计算机温度低于可接受限制的消息。 现在，请将解决方案生成为容器映像并将其推送到容器注册表。 

1. 在 Visual Studio Code 终端中输入以下命令，以登录到 Docker。 然后可将模块映像推送到 Azure 容器注册表。
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   使用用户名、密码以及在第一部分从 Azure 容器注册表复制的登录服务器。 也可以在 Azure 门户中从注册表的“访问密钥”部分检索这些值。

2. 在 VS Code 资源管理器中右键单击“deployment.template.json”文件，然后选择“生成并推送 IoT Edge 解决方案”。 

告知 Visual Studio Code 生成解决方案时，它首先获取部署模板中的信息，然后在名为 **config** 的新文件夹中生成 deployment.json 文件。然后，它在集成终端运行两个命令，即 `docker build` 和 `docker push`。 这两个命令会生成代码，将 Java 应用容器化，然后将代码推送到在初始化解决方案时指定的容器注册表。 

可在 VS Code 集成终端中查看具有标记的完整容器映像地址。 映像地址根据 module.json 文件中的信息生成，其格式为 \<存储库\>:\<版本\>-\<平台\>。 在本教程中，它应该类似于 registryname.azurecr.io/javamodule:0.0.1-amd64。

>[!TIP]
>如果你在尝试生成并推送模块时收到错误，请进行以下检查：
>* 你在 Visual Studio Code 中登录到 Docker 时是否使用了来自容器注册表的凭据？ 这些凭据不同于用来登录到 Azure 门户的凭据。
>* 你的容器存储库是否正确？ 打开“模块” > “cmodule” > “module.json”并查找 **repository** 字段。 映像存储库应当类似于 **\<registryname\>.azurecr.io/javamodule**。 
>* 你在生成的容器是否为开发计算机运行的同一类型的容器？ Visual Studio Code 默认生成 Linux amd64 容器。 如果开发计算机运行的是 Windows 容器或 Linux arm32v7 容器，请在 VS Code 窗口底部的蓝色状态栏上更新平台，以匹配你的容器平台。

## <a name="deploy-and-run-the-solution"></a>部署并运行解决方案

在用于设置 IoT Edge 设备的快速入门文章中，已使用 Azure 门户部署了一个模块。 还可以使用用于 Visual Studio Code 的 Azure IoT 中心工具包扩展（以前称为 Azure IoT 工具包扩展）来部署模块。 你已经为方案准备了部署清单，即 **deployment.json** 文件。 现在需要做的就是选择一个设备来接收部署。

1. 在 VS Code 命令面板中，运行命令“Azure: Sign in”并按照说明登录 Azure 帐户。 如果已登录，则可跳过此步骤。

2. 在 VS Code 命令面板中，运行“Azure IoT 中心: **Select IoT Hub** 命令。 

3. 选择包含要配置的 IoT Edge 设备的订阅和 IoT 中心。 

4. 在 VS Code 资源管理器中，展开“Azure IoT 中心设备”部分。 

5. 右键单击 IoT Edge 设备的名称，然后选择“为单个设备创建部署”。 

   ![为单个设备创建部署](./media/tutorial-java-module/create-deployment.png)

6. 选择 **config** 文件夹中的 **deployment.json** 文件，然后单击“选择 Edge 部署清单”。 不要使用 deployment.template.json 文件。 

7. 单击“刷新”按钮。 此时会看到新的 **JavaModule** 在运行，此外还有 **TempSensor** 模块以及 **$edgeAgent** 和 **$edgeHub** 在运行。  

## <a name="view-generated-data"></a>查看生成的数据

将部署清单应用到 IoT Edge 设备以后，设备上的 IoT Edge 运行时就会收集新的部署信息并开始在其上执行操作。 在设备上运行的未包括在部署清单中的任何模块都会停止。 设备中缺失的任何模块都会启动。 

可以通过 Visual Studio Code 资源管理器的“Azure IoT 中心设备”部分查看 IoT Edge 设备的状态。 展开设备的详细信息，可以看到已部署的正在运行的模块的列表。 

在 IoT Edge 设备上，可以使用 `iotedge list` 命令查看部署模块的状态。 应该看到四个模块：两个 IoT Edge 运行时模块、tempSensor 以及在本教程中创建的自定义模块。 启动所有模块可能需要数分钟，因此如果一开始没有看到全部模块，请重新运行命令。 

若要查看由任何模块生成的消息，请使用 `iotedge logs <module name>` 命令。 

可以使用 Visual Studio Code 在消息到达 IoT 中心时查看它们。 

1. 若要监视抵达 IoT 中心的数据，请选择省略号 (**...**)，然后选择“开始监视 D2C 消息”。
2. 若要监视特定设备的 D2C 消息，请右键单击列表中的设备，然后选择“开始监视 D2C 消息”。
3. 若要停止监视数据，请在命令面板中运行“Azure IoT Hub: Stop monitoring D2C message”命令。 
4. 若要查看或更新模块孪生，请右键单击列表中的模块，然后选择“编辑模块孪生”。 若要更新模块孪生，请保存孪生 JSON 文件，然后右键单击编辑器区域并选择“更新模块孪生”。
5. 若要查看 Docker 日志，请安装适用于 VS Code 的 [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)。 可在 Docker 资源管理器中本地查找正在运行的模块。 在上下文菜单中选择“显示日志”，在集成终端中进行查看。
 
## <a name="clean-up-resources"></a>清理资源 

如果打算继续学习下一篇建议的文章，可以保留已创建的资源和配置，以便重复使用。 还可以继续使用相同的 IoT Edge 设备作为测试设备。 

否则，可以删除本文中创建的本地配置和 Azure 资源，以避免收费。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>后续步骤

在本教程中，你已创建一个 IoT Edge 模块，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 可以继续阅读后续教程，了解如何使用 Azure IoT Edge 通过其他方式将数据转化为边缘业务见解。

> [!div class="nextstepaction"]
> [使用 SQL Server 数据库在边缘存储数据](tutorial-store-data-sql-server.md)

