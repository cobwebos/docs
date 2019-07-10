---
title: 创建和部署自定义模块 - Azure IoT Edge 上的机器学习 | Microsoft Docs
description: 通过机器学习模型创建和部署基于叶设备处理数据的 IoT Edge 模块，然后将见解发送到 IoT 中心。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6c4636fe370a4046b1c5020aee249529f1498639
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155517"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>教程：创建并部署自定义 IoT Edge 模块

> [!NOTE]
> 有一个教程系列介绍如何在 IoT Edge 上使用 Azure 虚拟机，本文是其中的一篇。 如果你是直接转到本文的，建议你从该系列中的[第一篇文章](tutorial-machine-learning-edge-01-intro.md)开始，以获得最佳学习效果。

在本文中，我们要创建三个 IoT Edge 模块来从叶设备中接收消息，通过机器学习模型运行数据，然后将见解转发到 IoT 中心。

IoT Edge 中心促进模块间通信。 通过将 IoT Edge 中心用作消息中转站，可保证模块之间相互独立。 模块只需指定它们接受消息的输入和写入消息的输出。

我们希望 IoT Edge 设备代为完成四项操作：

* 从叶设备接收数据
* 预测发送此数据的设备的 RUL
* 在仅使用设备 RUL 的情况下将消息发送到 IoT 中心（可修改此函数，指示如果 RUL 低于某级别，则仅发送数据）
* 将叶设备数据保存到 IoT Edge 设备上的本地文件中。 此数据文件会通过文件上传定期上传到 IoT 中心，以优化机器学习模型的训练。 与定期消息流式传输相比，使用文件上传更具成本效益。

我们使用三个自定义模块来完成上述任务：

* RUL 分类器  ：我们在[训练和部署 Azure 机器学习模型](tutorial-machine-learning-edge-04-train-model.md)中创建的 turboFanRulClassifier 模块是一个标准机器学习模块，它公开一个名为“amlInput”的输入和一个名为“amlOutput”的输出。 “amlInput”需要其输入与我们发送到基于 ACI 的 Web 服务的输入完全相同。 同样地，“amlOutput”返回与 Web 服务相同的数据。

* Avro 编写器  ：此模块会接收有关“avroModuleInput”输入的消息，并按 Avro 格式将消息保留在磁盘中供稍后上传到 IoT 中心。

* 路由器模块  ：路由器模块会接收来自下游叶设备的消息，然后设置消息格式并将其发送到分类器。 然后，该模块会接收来自分类器的消息，并将消息转发给 Avro 编写器模块。 最后，该模块只将 RUL 预测结果发送到 IoT 中心。

  * 输入：
    * deviceInput：从叶设备接收消息 
    * rulInput：从“amlOutput”接收消息 

  * 输出：
    * 分类器：向“amlInput”发送消息 
    * writeAvro：发送“avroModuleInput”消息 
    * toIotHub：将消息发送到 $upstream，后者再将消息传递到连接的 IoT 中心 

下图显示了整个解决方案的模块、输入、输出和 IoT Edge 中心路由：

![IoT Edge 三模块架构图](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

本文的步骤通常由云开发人员执行。

## <a name="create-a-new-iot-edge-solution"></a>创建新的 IoT Edge 解决方案

在执行第二个 Azure Notebook（共 2 个）的过程中，我们创建并发布了一个包含 RUL 模型的容器映像。 Azure 机器学习在映像创建过程中构建到组件中，使该映像可部署为 Azure IoT Edge 模块。 在此步骤中，我们将使用“Azure 机器学习”模块创建一个 Azure IoT Edge 解决方案，并将该模块指向我们使用 Azure Notebook 发布的映像。

1. 打开到开发计算机的远程桌面会话。

2. 在 Visual Studio Code 中打开 C:\\source\\IoTEdgeAndMlSample 文件夹  。

3. 右键单击资源管理器面板（在空白区单击），然后选择“新建 IoT Edge 解决方案”  。

    ![创建新的 IoT Edge 解决方案](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

4. 接受默认解决方案名称 EdgeSolution  。

5. 选择“Azure 机器学习”作为模块模板  。

6. 将模块命名为 turbofanRulClassifier  。

7. 选择机器学习工作区。

8. 选择在运行 Azure Notebook 时创建的映像。

9. 查看解决方案并注意已创建的文件：

   * deployment.template.json：  此文件包含解决方案中每个模块的定义。 此文件中有三个部分需多加注意：

     * 注册表凭据：  定义你在解决方案中使用的一组自定义容器注册表。 当前，它应包含存储 Azure 机器学习映像的机器学习工作区中的注册表。 你可拥有任意数量的容器注册表，但为简单起见，这个注册表将通用于所有模块

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io”
         }
       }
       ```

     * 模块：  本部分包含一组此解决方案随附的用户定义的模块。 你将注意到此部分当前包含 tempSensor 和 turbofanRulClassifier 这两个模块。 Visual Studio Code 模板已安装 tempSensor，但此解决方案中无需使用它。 可从模块部分删除 tempSensor 模块定义。 请注意，turbofanRulClassifier 模块定义指向的是你的容器注册表中的映像。 在我们向解决方案添加更多模块时，它们将在此部分中显示。

       ```json
       "modules": {
         "tempSensor": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
             "createOptions": {}
           }
         },
         "turbofanRulClassifier": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "<your registry>.azurecr.io/edgemlsample:1",
             "createOptions": {}
           }
         }
       }
       ```

     * 路由：我们将在本教程中频繁使用路由  。 路由定义了模块彼此之间的通信方式。 模板定义的两个路由与我们需要的路由不符。 第一个路由将来自分类器所有输出的所有数据发送到 IoT 中心 ($upstream)。 而另一个路由用于我们刚才删除的 tempSensor。 删除两个默认路由。

       ```json
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
             "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/\* INTO $upstream",
             "sensorToturbofanRulClassifier": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\\"/modules/turbofanRulClassifier/inputs/input1\\")"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       }
       ```

   * deployment.debug.template.json：此文件是 deployment.template.json 的调试版本  。 我们应将来自 deployment.template.json 的所有更改镜像到此文件。

   * .env：你应在此文件中提供用户名和密码来访问注册表  。

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

10. 在 Visual Studio Code 资源浏览器中右键单击 deployment.template.json 文件，然后选择“生成 IoT Edge 解决方案”  。

11. 请注意，此命令会创建一个带有 deployment.amd64.json 文件的 config 文件夹。 此文件是解决方案的具体部署模板。

## <a name="add-router-module"></a>添加路由器模块

接下来，向解决方案添加路由器模块。 路由器模块复制解决方案的多项操作：

* 从叶设备接收消息：当消息从下游设备传输到 IoT Edge 设备时，路由器模块会接收消息并开始编排消息的路由  。
* 将消息发送到 RUL 分类器模块：当从下游设备接收到新的消息时，路由器模块会将消息转换为 RUL 分类器期望的格式  。 路由器将消息发送到 RUL 分类器进行 RUL 预测。 分类器做出预测后，会将消息发送回路由器模块。
* 将 RUL 消息发送到 IoT 中心：当路由器从分类器接收消息时，它会转换消息，使其仅包含基本信息、设备 ID 和 RUL，并将简短消息发送到 IoT 中心  。 仅当 RUL 预测结果低于阈值（例如，当 RUL 少于 100 个周期时），进一步优化才会将消息发送到 IoT 中心（我们未在此处进行此优化）。 此方式的筛选会减少消息量，同时降低 IoT 中心的成本。
* 将消息发送到 Avro 编写器模块：为保留下游设备发送的所有数据，路由器模块会将从分类器接收到的整条消息发送到 Avro 编写器模块，后者将保留数据并通过 IoT 中心文件上传来上传数据  。

> [!NOTE]
> 模块职责的说明可能使得处理显得连贯有序，但该流程实际上是基于消息/事件的。 这就是为什么我们需要一个类似路由器木块的业务流程模块。

### <a name="create-module-and-copy-files"></a>创建模块和复制文件

1. 在 Visual Studio Code 中右键单击模块文件夹，然后选择“添加 IoT Edge 模块”  。

2. 选择“C# 模块”  。

3. 将模块命名为 turbofanRouter  。

4. 当系统提示提供 Docker 映像存储库时，请使用来自机器学习工作区的注册表（可在 deployment.template.json 文件的 registryCredentials 节点中找到此注册表）  。 该值是指向注册表的完全限定的地址，例如 \<你的注册表\>.azurecr.io/turbofanrouter  。

    > [!NOTE]
    > 在本文中，我们使用由 Azure 机器学习服务工作区创建的 Azure 容器注册表，我们之前用它来训练和部署分类器。 使用它仅为方便起见。 我们本来可创建一个新的容器注册表并在此处发布模块。

5. 在 Visual Studio Code 中通过“视图” > “终端”打开新的终端窗口，然后复制模块目录中的文件   。

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

6. 当系统提示覆盖 program.cs 时，按 `y`，再点击 `Enter`。

### <a name="build-router-module"></a>构建路由器模块

1. 在 Visual Studio Code 中，选择“终端” > “配置默认生成任务”   。

2. 单击“基于模板创建 tasks.json 文件”  。

3. 单击“.NET Core”  。

4. 当 tasks.json 打开时，将内容替换为以下信息：

    ```json
    {
      // See https://go.microsoft.com/fwlink/?LinkId=733558
      // for the documentation about the tasks.json format
      "version": "2.0.0",
      "tasks": [
        {
          "label": "build",
          "command": "dotnet",
          "type": "shell",
          "group": {
            "kind": "build",
            "isDefault": true
          },
          "args": [
            "build",
            "${workspaceFolder}/modules/turbofanRouter"
          ],
          "presentation": {
            "reveal": "always"
          },
          "problemMatcher": "$msCompile"
        }
      ]
    }
    ```

5. 保存并关闭 tasks.json。

6. 通过 `Ctrl + Shift + B` 或“终端” > “运行生成任务”来运行生成   。

### <a name="set-up-module-routes"></a>设置模块路由

如上所述，IoT Edge 运行时使用 deployment.template.json 文件中配置的路由来管理松散耦合的模块之间的通信  。 在该部分，我们将深入探讨如何为 turbofanRouter 模块设置路由。 我们将先介绍输入路由，然后讲解输出。

#### <a name="inputs"></a>输入

1. 在 Program.cs 的 Init() 方法中，我们为模块注册两个回调：

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. 第一个回调侦听发送到 deviceInput 接收器的消息  。 从上图可见，我们想要将任何叶设备中的消息路由到此输入。 在 deployment.template.json 文件中，添加一个路由，以指示边缘中心将非 IoT Edge 模块发送的 IoT Edge 设备所接收的任何消息路由到 turbofanRouter 模块上名为“deviceInput”的输入中  ：

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. 接下来，将 rulClassifier 模块消息的路由添加到 turbofanRouter 模块：

   ```json
   "classifierToRouter": "FROM /messages/modules/classifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Outputs

再向 $edgeHub 路由参数添加 4 个路由，以处理来自路由器模块的输出。

1. Program.cs 定义了 SendMessageToClassifier() 方法，后者使用模块客户端通过路由将消息发送到 RUL 分类器：

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/classifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub() 使用模块客户端通过路由仅将设备的 RUL 数据发送到 IoT 中心：

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter() 使用模块客户端来发送消息，其中 RUL 数据添加到 avroFileWriter 模块。

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage() 将失败的消息发送到 IoT 中心的上游，它们稍后可在此处进行路由。

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

将所有路由合在一起，“$edgeHub”节点应如下述 JSON 所示：

```json
"$edgeHub": {
  "properties.desired": {
    "schemaVersion": "1.0",
    "routes": {
      "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")",
      "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amlOutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")",
      "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")",
      "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream",
      "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")",
      "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
    },
    "storeAndForwardConfiguration": {
      "timeToLiveSecs": 7200
    }
  }
}
```

> [!NOTE]
> 通过添加 turbofanRouter 模块，额外创建了下述路由：`turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`。 删除此路由，仅在 deployment.template.json 文件中保留上述所列的路由。

#### <a name="copy-routes-to-deploymentdebugtemplatejson"></a>将路由复制到 deployment.debug.template.json

在最后一步中，为保证文件同步，请将所作更改镜像到 deployment.debug.template.json 中的 deployment.template.json。

## <a name="add-avro-writer-module"></a>添加 Avro 编写器模块

Avro 编写器模块在解决方案中负责两个操作：存储消息和上传文件。

* 存储消息：当 Avro 编写器模块收到消息时，它会按 Avro 格式将消息写入到本地文件系统  。 我们使用了绑定装载，它将目录（在本例中是 /data/avrofiles）装载到模块容器的路径中。 通过此装载，模块可写入到本地路径 (/avrofiles)，还使这些文件可直接通过 IoT Edge 设备进行访问。

* 上传文件：Avro 编写器模块使用 Azure IoT 中心文件上传功能将文件上传到 Azure 存储帐户  。 成功上传文件后，模块从磁盘中删除文件

### <a name="create-module-and-copy-files"></a>创建模块和复制文件

1. 在命令面板中，搜索并选择“Python:  选择解释器”。

1. 选择在 C:\\Python37 中找到的解释器。

1. 再次打开命令面板，搜索并选择“终端:  选择默认 Shell”。

1. 出现提示时，选择“命令提示符”  。

1. 打开新的终端 shell，选择“终端” > “新建终端”   。

1. 在 Visual Studio Code 中右键单击模块文件夹，然后选择“添加 IoT Edge 模块”  。

1. 选择“Python 模块”。 

1. 将模块命名为“avroFileWriter”。

1. 当系统提示提供 Docker 映像存储库时，请使用在添加路由器模块时所用的同一注册表。

1. 将示例模块中的文件复制到解决方案中。

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. 如果系统提示覆盖 main.py，请键入 `y`，然后点击 `Enter`。

1. 请注意，filemanager.py 和 schema.py 已添加到解决方案中，且 main.py 已更新。

> [!NOTE]
> 打开 Python 文件时，系统可能提示你安装 pylint。 无需安装 Linter 即可完成此教程。

### <a name="bind-mount-for-data-files"></a>针对数据文件的绑定装载

如简介中所述，编写器模块依靠绑定装载的存在来将 Avro 文件写入设备的文件系统。

#### <a name="add-directory-to-device"></a>将目录添加到设备

1. 使用 SSH 连接到 IoT Edge 设备 VM。

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. 创建将保留所保存的叶设备消息的目录。

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

3. 更新目录权限，使其可由容器写入。

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

4. 验证目录显示是否具有面向用户、组和所有者的写入 (w) 权限。

   ```bash
   ls -la /data
   ```

   ![avrofiles 的目录权限](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>将目录添加到模块

要将目录添加到模块的容器，我们将修改与 avroFileWriter 模块关联的 Dockerfile。 下面是与模块关联的三个 Dockerfile：Dockerfile.amd64、Dockerfile.amd64.debug 和 Dockerfile.arm32v7。 应保证这些文件同步，以防我们希望调试或部署到 arm32 设备。 在本文中，只需关注 Dockerfile.amd64。

1. 在开发计算机上，打开 Dockerfile.amd64 文件  。

2. 修改此文件，使其如下例所示：

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

   `mkdir` 和 `chown` 命令会指导 Docker 生成过程在映像中创建一个名为 /avrofiles 的顶级目录，然后将模块用户设置为该目录的所有者。 有必要确保这些命令插入到用户通过 `useradd` 命令添加到映像的模块后面且模块用户的上下文开关（USER 模块用户）的前面。

3. 对 Dockerfile.amd64.debug 和 Dockerfile.arm32v7 进行相应更改。

#### <a name="update-the-module-configuration"></a>更新模块配置

创建绑定的最后一步是，使用绑定信息更新 deployment.template.json（以及 deployment.debug.template.json）文件。

1. 打开 deployment.template.json。

2. 通过添加 `Binds` 参数将容器目录 /avrofiles 指向边缘设备上的本地目录，修改 avroFileWriter 的模块定义。 模块定义应与下例一致：

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles"
           ]
         }
       }
     }
   }
   ```

3. 对 deployment.debug.template.json 进行相应的更改。

### <a name="bind-mount-for-access-to-configyaml"></a>针对 config.yaml 访问的绑定装载

需要为编写器模块再添加一个绑定。 通过此绑定，模块可从 IoT Edge 设备上的 /etc/iotedge/config.yaml 文件读取连接字符串。 我们需要连接字符串来创建 IoTHubClient，因此可调用 \_blob\_async 方法，将文件上传到 IoT 中心。 添加此绑定的步骤与上一部分中的步骤类似。

#### <a name="update-directory-permission"></a>更新目录权限

1. 使用 SSH 连接到 IoT Edge 设备。

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. 添加到 config.yaml 文件的读取权限。

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

3. 验证权限是否设置正确。

   ```bash
   ls -la /etc/iotedge/
   ```

4. 确保 config.yaml 的权限为 -r--r--r--  。

#### <a name="add-directory-to-module"></a>将目录添加到模块

1. 在开发计算机上，打开 Dockerfile.amd64 文件  。

2. 再向文件添加一组 `mkdir` 和 `chown` 命令，使其如下所示：

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig

   USER moduleuser

   CMD "python3", "-u", "./main.py"]
   ```

3. 对 Dockerfile.amd64.debug 和 Dockerfile.arm32v7 进行相应更改。

#### <a name="update-the-module-configuration"></a>更新模块配置

1. 打开 **deployment.template.json** 文件。

2. 通过将 `Binds` 参数添加第二行将容器目录 (/app/iotconfig) 指向设备上的本地目录 (/etc/iotedge)，修改 avroFileWriter 的定义。

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles",
             "/etc/iotedge:/app/iotconfig"
           ]
         }
       }
     }
   }
   ```

3. 对 deployment.debug.template.json 进行相应的更改。

## <a name="install-dependencies"></a>安装依赖项

编写器模块在两个 Python 库（fastavro 和 PyYAML）上获得依赖项。 我们需要在开发计算机上安装依赖项，并指示 Docker 生成过程在模块映像中安装它们。

### <a name="pyyaml"></a>PyYAML

1. 在开发计算机上，打开 requirements.txt 文件并添加 pyyaml  。

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

2. 打开 Dockerfile.amd64 文件，再添加 `pip install` 命令来升级 setuptools  。

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && \
       apt-get install -y --no-install-recommends libcurl4-openssl-dev python3-pip libboost-python1.58-dev libpython3-dev && \
       rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   RUN pip install -U pip setuptools
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

3. 对 Dockerfile.amd64.debug 进行相应更改。 <!--may not be necessary. Add 'if needed'?-->

4. 在 Visual Studio Code 中打开终端并键入内容，本地安装 pyyaml

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro

1. 在 requirements.txt 中，在 pyyaml 的后面添加 fastavro。

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

2. 使用 Visual Studio Code 终端将 fastavro 安装到开发计算机上。

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>重新配置 IoT 中心

通过将 IoT Edge 设备和模块引入到系统中，我们对于要将哪些数据发送到中心以及出于何种目的发送所抱有的期望出现了变化。 我们需要在中心内重新配置路由来满足我们新的需求。

> [!NOTE]
> 我们在部署模块前重新配置了中心，原因是需要正确设置一些中心设置（尤其是文件上传），才能使 avroFileWriter 模块正确运行

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>在 IoT 中心内设置 RUL 消息的路由

部署好路由器和分类器后，我们希望定期接收仅包含设备 ID 和设备 RUL 预测的消息。 我们想要将 RUL 数据路由到其自身的存储位置，我们可在此位置监视设备的状态、生成报告并根据需要发出警报。 同时，我们希望仍由尚未附加到 IoT Edge 设备的叶设备直接发送的任何设备数据继续路由到当前的存储位置。

#### <a name="create-a-rul-message-route"></a>创建 RUL 消息路由

1. 在 Azure 门户中，导航到 IoT 中心。

2. 从右侧导航栏中，选择“消息路由”  。

3. 选择 **添加** 。

4. 将路由命名为 RulMessageRoute  。

5. 在“终结点”选择器旁边选择“添加”，然后选择“Blob 存储”    。

6. 在“添加存储终结点”窗体中，将终结点命令为 ruldata   。

7. 选择“选取容器”  。

8. 选择在这整个教程中使用的存储帐户，该帐户采用 iotedgeandml\<唯一前缀\> 之类的名称  。

9. 选择 ruldata 容器，然后单击“选择”   。

10. 单击“创建”以创建存储终结点  。

11. 对于“路由查询”，请输入以下查询  ：

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

12. 展开“测试”部分，然后展开“消息正文”部分   。 将消息替换为以下预期消息示例：

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

13. 选择“测试路由”  。 如果测试成功，将看到“消息与查询相匹配”。

14. 单击“ **保存**”。

#### <a name="update-turbofandevicetostorage-route"></a>更新 turbofanDeviceToStorage 路由

我们不希望将新的预测数据路由到旧的存储位置，因此请更新路由来避免此问题。

1. 在 IoT 中心的“消息路由”页面，选择“路由”选项卡   。

2. 选择 turbofanDeviceDataToStorage，或选择向初始设备数据路由赋予的任何名称  。

3. 将路由查询更新为

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

4. 展开“测试”部分，然后展开“消息正文”部分   。 将消息替换为以下预期消息示例：

   ```json
   {
     "Sensor13": 2387.96,
     "OperationalSetting1": -0.0008,
     "Sensor6": 21.61,
     "Sensor11": 47.2,
     "Sensor9": 9061.45,
     "Sensor4": 1397.86,
     "Sensor14": 8140.39,
     "Sensor18": 2388.0,
     "Sensor12": 522.87,
     "Sensor2": 642.42,
     "Sensor17": 391.0,
     "OperationalSetting3": 100.0,
     "Sensor1": 518.67,
     "OperationalSetting2": 0.0002,
     "Sensor20": 39.03,
     "DeviceId": 19.0,
     "Sensor5": 14.62,
     "PredictedRul": 212.00132402791962,
     "Sensor8": 2388.01,
     "Sensor16": 0.03,
     "CycleTime": 42.0,
     "Sensor21": 23.3188,
     "Sensor15": 8.3773,
     "Sensor3": 1580.09,
     "Sensor10": 1.3,
     "Sensor7": 554.57,
     "Sensor19": 100.0
   }
   ```

5. 选择“测试路由”  。 如果测试成功，将看到“消息与查询相匹配”。

6. 选择“保存”。 

### <a name="configure-file-upload"></a>配置文件上传

配置 IoT 中心文件上传功能，使文件编写器模块能够将文件上传到存储。

1. 从 IoT 中心内的左侧导航栏，选择“文件上传”  。

2. 选择“Azure 存储容器”  。

3. 从列表中选择存储帐户。

4. 选择 uploadturbofanfiles 容器，然后单击“选择”   。

5. 选择“保存”。  保存完毕时，门户将向你发送通知。

> [!Note]
> 在本教程中，我们不打开上传通知，但你可参阅[接收文件上传通知](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification)，详细了解如何处理文件上传通知。

## <a name="build-publish-and-deploy-modules"></a>生成、发布和部署模块

我们已进行配置更改，接下来准备生成映像并将其发布到 Azure 容器注册表。 生成过程会使用 deployment.template.json 文件来决定需要生成哪些模块。 可在模块文件夹的 module.json 文件中找到每个模块的设置（包括版本）。 生成过程首先针对与 module.json 文件中找到的当前配置相匹配的 Dockerfile 运行一个 Docker 生成来创建映像。 然后，它通过与 module.json 文件中的标记相匹配的版本标记将映像从 module.json 文件发布到注册表。 最后，它生成一个配置特定的部署清单（例如 deployment.amd64.json），我们要将它部署到 IoT Edge 设备。 IoT Edge 设备从部署清单中读取消息，然后根据说明下载模块、配置路由并设置所有必需属性。 此部署方法有两个需注册的“副作用”：

* 部署滞后：由于 IoT Edge 运行时必须识别对其所需属性的更改，然后才能开始重新配置，因此在你部署模块后直到运行时获取这些模块并开始更新 IoT Edge 设备需要一段时间  。

* 模块版本问题：如果使用与上一模块相同的版本标记将模块容器的新版本发布到容器注册表，运行时不会下载此模块的新版本  。 它会将本地映像的版本标记与部署清单中的必需映像进行比较。 如果两者相匹配，则运行时不执行任何操作。 因此，有必要在每次希望部署新的更改时将模块版本号进行递增。 版本号递增方式是，针对要更改的模块在 module.json 文件的 tag 属性下更改 version 属性   。 生成并发布模块。

    ```json
    {
      "$schema-version": "0.0.1",
      "description": "",
      "image": {
        "repository": "<your registry>.azurecr.io/avrofilewriter",
        "tag": {
          "version": "0.0.1",
          "platforms": {
            "amd64": "./Dockerfile.amd64",
            "amd64.debug": "./Dockerfile.amd64.debug",
            "arm32v7": "./Dockerfile.arm32v7"
          }
        },
        "buildOptions": []
      },
      "language": "python"
    }
    ```

### <a name="build-and-publish"></a>生成并发布

1. 在开发 VM 的 Visual Studio Code 中，打开 Visual Studio Code 终端窗口并登录容器注册表。

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. 在 Visual Studio Code 中，右键单击 deployment.template.json，然后选择“生成并推动 IoT Edge 解决方案”  。

### <a name="view-modules-in-the-registry"></a>在注册表中查看模块

成功完成生成后，我们将能够使用 Azure 门户查看所发布的模块。

1. 在 Azure 门户中，导航到 Azure 机器学习工作区，然后单击注册表的超链接  。

    ![从机器学习服务工作区导航到注册表](media/tutorial-machine-learning-edge-06-custom-modules/follow-registry-link.png)

2. 从注册表端导航器中，选择“存储库”  。

3. 请注意，所创建的两个模块（avrofilewriter 和 turbofanrouter）都显示为存储库   。

4. 选择 turbofanrouter，并记下你已发布一个标记为 0.0.1-amd64 的映像  。

   ![查看第一个标记的 turbofanrouter 版本](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>将模块部署到 IoT Edge 设备

我们已生成模块并将其部署到解决方案中，接下来要将模块部署到 IoT Edge 设备。

1. 在 Visual Studio Code 中，右键单击 config 文件夹中的 deployment.amd64.json 文件  。

2. 选择“为单个设备创建部署”  。

3. 选择 IoT Edge 设备 aaTurboFanEdgeDevice  。

4. 在 Visual Studio Code 资源管理器中刷新 Azure IoT 中心设备面板。 你应会看到已部署三个新模块，但它们尚未运行。

5. 几分钟后再次刷新，你将看到模块正在运行。

   ![在 Visual Studio Code 中查看正在运行的模块](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> 模块可能需要几分钟才会启动并进入稳定运行状态。 在此期间，你可能看到模块启动但在尝试建立与 IoT Edge 中心模块的连接时停止。

## <a name="diagnosing-failures"></a>诊断故障

在此部分，我们分享了查看一个或多个模块的哪些方面出现故障的一些技巧。 通常，首先可通过 Visual Studio Code 中的状态发现故障。

### <a name="identify-failed-modules"></a>确定出现故障的模块

* Visual Studio Code：  查看 Azure IoT 中心设备面板。 如果大多数模块处于运行状态，但其中一个模块已停止，则需要进一步调查停止运行的模块。 如果所有模块长期处于停止状态，这也可能表示出现故障。

* **Azure 门户：** 通过导航到门户中的 IoT 中心，再找到设备详细信息页面（在 IoT Edge 下向下钻取设备），你可能发现某模块已报告错误或从未向 IoT 中心报告任何问题。

### <a name="diagnosing-from-the-device"></a>通过设备诊断

通过登录 IoT Edge 设备，可获取大量有关模块状态的信息。 我们使用的主要机制是 Docker 命令，它可用于检查设备上的容器和映像。

1. 列出所有正在运行的容器。 我们希望看到每个模块的容器都有与该模块相对应的名称。 此外，此命令列出了与你的期望完全匹配的容器映像（包括版本）。 你还可通过在命令中将“映像”替换为“容器”来列出映像。

   ```bash
   sudo docker container ls
   ```

2. 获取容器的日志。 此命令会输出容器中写入到 StdErr 和 StdOut 的所有内容。 此命令适用于已启动但由于某种原因而停机的容器。 它还帮助理解 edgeAgent 或 edgeHub 容器发生了什么情况。

   ```bash
   sudo docker container logs <container name>
   ```

3. 检查容器。 此命令提供了大量有关映像的信息。 可根据要查找的内容筛选数据。 例如，如果你要查看 avroFileWriter 上的绑定是否正确，可使用以下命令：

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

4. 连接到正在运行的容器。 如果想要在容器运行时检查该容器，此命令非常有用：

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>后续步骤

本文中，我们在 Visual Studio Code 中创建了一个 IoT Edge 解决方案，它具有 3 个模块、1 个分类器、1 个路由器和 1 个文件编写器/上载程序。 我们设置了路由来允许模块在边缘设备上彼此通信，修改了边缘设备的配置，还上传了 Dockerfile 来安装依赖项并将绑定装载添加到模块的容器中。 接下来，我们上传了 IoT 中心的配置来根据类型路由消息并处理文件上传操作。 一切就绪后，我们将模块部署到 IoT Edge设备，同时确保了模块正确运行。

可在以下页面上找到详细信息：

* [了解如何在 IoT Edge 中部署模块和建立路由](module-composition.md)
* [IoT 中心消息路由查询语法](../iot-hub/iot-hub-devguide-routing-query-syntax.md)
* [IoT 中心消息路由：现可基于消息正文进行路由](https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/)
* [使用 IoT 中心上传文件](../iot-hub/iot-hub-devguide-file-upload.md)
* [通过 IoT 中心将设备中的文件上传到云](../iot-hub/iot-hub-python-python-file-upload.md)

请转到下一篇文章，开始发送数据和查看解决方案的实际运用。

> [!div class="nextstepaction"]
> [通过透明网关发送数据](tutorial-machine-learning-edge-07-send-data-to-hub.md)
