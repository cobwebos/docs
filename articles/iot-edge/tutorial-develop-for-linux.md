---
title: 开发适用于 Linux 设备的模块 - Azure IoT Edge | Microsoft Docs
description: 本教程详细介绍如何设置开发计算机和云资源来使用适用于 Linux 设备的 Linux 容器开发 IoT Edge 模块
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/26/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 11fa72f5853350c76b2a8d0aa4fd7b96b598b670
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66303852"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>教程：开发适用于 Linux 设备的 IoT Edge 模块

使用 Visual Studio Code 开发代码并将其部署到运行 IoT Edge 的 Linux 设备。 

在快速入门文章中，使用 Linux 虚拟机创建了 IoT Edge 设备，并部署了来自 Azure 市场的预构建模块。 本教程详细介绍如何才能开发自己的代码并将其部署到 IoT Edge 设备。 本教程是所有其他教程的有用系统必备组件，其将详细介绍特定编程语言或 Azure 服务。 

本教程使用**将 C 模块部署到 Linux 设备**的示例。 此示例因需要安装的系统必备组件最少而被选中，以便你可以了解开发工具，而无需担心是否安装了正确的库。 了解开发概念后，即可选择首选语言或 Azure 服务来深入了解详细信息。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置开发计算机。
> * 使用适用于 Visual Studio Code 的 IoT Edge Tools 创建新项目。
> * 将项目作为容器生成并将其存储在 Azure 容器注册表中。
> * 将代码部署到 IoT Edge 设备。 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>关键概念

本教程详细介绍如何开发 IoT Edge 模块。 *IoT Edge 模块*有时简称*模块*，它是一个包含可执行代码的容器。 可以将一个或多个模块部署到 IoT Edge 设备。 模块可执行特定任务，例如从传感器引入数据、执行数据分析或数据清理操作，或将消息发送到 IoT 中心。 有关详细信息，请参阅[了解 Azure IoT Edge 模块](iot-edge-modules.md)。

开发 IoT Edge 模块时，了解开发计算机和最终将部署模块的目标 IoT Edge 设备之间的差异非常重要。 为保存模块代码而生成的容器必须与*目标设备*的操作系统 (OS) 匹配。 例如，最常见的方案是在 Windows 计算机上开发面向运行 IoT Edge 的 Linux 设备的模块。 在这种情况下，容器操作系统将为 Linux。 在学习本教程的过程中，请记住*开发计算机 OS* 和*容器 OS* 之间的差异。

本教程针对运行 IoT Edge 的 Linux 设备。 只要开发计算机可以运行 Linux 容器，就可以使用首选开发计算机操作系统。 我们建议使用 Visual Studio Code 进行 Linux 设备开发，这也是本教程将使用的工具。 还可以使用 Visual Studio，但这两个工具提供的支持存在差异。

下表列出了 Visual Studio Code 和 Visual Studio 支持的 **Linux 容器**开发方案。

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux 设备体系结构** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Azure 服务** | Azure Functions <br> Azure 流分析 <br> Azure 机器学习 |   |
| **语言** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **详细信息** | [适用于 Visual Studio Code 的 Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [适用于 Visual Studio 2017 的 Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)、[适用于 Visual Studio 2019 的 Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

本教程讲解适用于 Visual Studio Code 的开发步骤。 如果想要使用 Visual Studio，请参阅[使用 Visual Studio 2019 为 Azure IoT Edge 开发和调试模块](how-to-visual-studio-develop-module.md)中的说明。

## <a name="prerequisites"></a>先决条件

开发计算机：

* 可以使用自己的计算机或虚拟机，具体取决于开发首选项。
* 大多数可以运行容器引擎的操作系统都可用于为 Linux 设备开发 IoT Edge 模块。 本教程使用 Windows 计算机，但指出 MacOS 或 Linux 上的已知差异。 
* 安装 [Git](https://git-scm.com/)，以便在本教程稍后部分拉取模块模板包。  

Linux 上的 Azure IoT Edge 设备：

* 我们建议不要在开发计算机上运行 IoT Edge，而应使用单独的设备。 开发计算机和 IoT Edge 设备之间的这一区分可更准确地反映真实部署方案，并有助于区分不同的概念。
* 如果没有第二台可用的设备，请按照快速入门文章的说明在 Azure 中使用 [Linux 虚拟机](quickstart-linux.md)创建 IoT Edge 设备。

云资源：

* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。 

## <a name="install-container-engine"></a>安装容器引擎

IoT Edge 模块被打包为容器，因此，需要在开发计算机上安装容器引擎来生成和管理容器。 我们建议使用 Docker Desktop 进行开发，因为它作为容器引擎拥有许多功能且广受欢迎。 借助 Windows 设备上的 Docker Desktop，可在 Linux 容器和 Windows 容器之间切换，以便轻松地为不同类型的 IoT Edge 设备开发模块。 

按照 Docker 文档的说明在开发计算机上安装： 

* [安装适用于 Windows 的 Docker Desktop](https://docs.docker.com/docker-for-windows/install/)

  * 安装适用于 Windows 的 Docker Desktop 时，系统会询问是想要使用 Linux 容器还是 Windows 容器。 可随时使用便捷开关更改此决定。 本教程使用 Linux 容器，因为模块面向 Linux 设备。 有关详细信息，请参阅[在 Windows 容器和 Linux 容器之间切换](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)。

* [安装适用于 Mac 的 Docker Desktop](https://docs.docker.com/docker-for-mac/install/)

* 阅读[关于 Docker CE](https://docs.docker.com/install/)，获取有关多个 Linux 平台的安装信息。

## <a name="set-up-vs-code-and-tools"></a>设置 VS Code 和工具

使用适用于 Visual Studio Code 的 IoT 扩展开发 IoT Edge 模块。 这些扩展提供项目模板、自动化部署清单的创建，并允许监视和管理 IoT Edge 设备。 本部分将安装 Visual Studio Code 和 IoT 扩展，然后设置 Azure 帐户用于在 Visual Studio Code 中管理 IoT 中心资源。 

1. 在开发计算机上安装 [Visual Studio Code](https://code.visualstudio.com/)。 

2. 安装完成后，选择“查看” > “扩展”   。 

3. 搜索 **Azure IoT Tools**，它实际上是一个扩展集合，可帮助与 IoT 中心和 IoT 设备进行交互，以及开发 IoT Edge 模块。 

4. 选择“安装”  。 每个随附的扩展均单独安装。 

5. 完成扩展安装后，通过选择“查看” > “命令面板”来打开命令面板   。 

6. 在命令面板中，搜索并选择 **Azure:Sign in** 命令。 根据提示登录到 Azure 帐户。 

7. 再次在命令面板中搜索并选择 **Azure IoT Hub:Select IoT Hub** 命令。 按照提示选择 Azure 订阅和 IoT 中心。 

7. 通过选择左侧活动栏中的图标，或选择“查看” > “资源管理器”，打开 Visual Studio Code 的“资源管理器”部分   。 

8. 在“资源管理器”部分的底部，展开折叠的“Azure IoT 中心设备”菜单  。 应该看到与通过命令面板选择的 IoT 中心关联的设备和 IoT Edge 设备。 

   ![查看 IoT 中心中的设备](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>创建新的模块项目

Azure IoT Edge Tools 扩展为 Visual Studio Code 中支持的所有 IoT Edge 模块语言提供项目模板。 这些模板包含将工作模块部署到测试 IoT Edge 所需的所有文件和代码，或为你提供使用自己的业务逻辑自定义模板的起点。 

本教程使用 C 模块模板，因为它需要安装的系统必备组件最少。 

### <a name="create-a-project-template"></a>创建项目模板

在 Visual Studio Code 命令面板中，搜索并选择 **Azure IoT Edge:** New IoT Edge Solution 命令。 按照提示操作并使用以下值创建解决方案： 

   | 字段 | 值 |
   | ----- | ----- |
   | 选择文件夹 | 在适用于 VS Code 的开发计算机上选择用于创建解决方案文件的位置。 |
   | 提供解决方案名称 | 输入解决方案的描述性名称，或者接受默认的 **EdgeSolution**。 |
   | 选择模块模板 | 选择“C 模块”。  |
   | 提供模块名称 | 接受默认的 **SampleModule**。 |
   | 为模块提供 Docker 映像存储库 | 映像存储库包含容器注册表的名称和容器映像的名称。 容器映像是基于你在上一步中提供的名称预先填充的。 将 **localhost:5000** 替换为 Azure 容器注册表中的登录服务器值。 可以在 Azure 门户的容器注册表的“概览”页中检索登录服务器。 <br><br> 最终的映像存储库看起来类似于 \<registry name\>.azurecr.io/samplemodule。 |
 
   ![提供 Docker 映像存储库](./media/tutorial-develop-for-linux/image-repository.png)

在 Visual Studio Code 窗口中加载新解决方案后，请花时间熟悉其创建的文件： 

* **.vscode** 文件夹包含名为 **launch.json** 的文件，该文件用于调试模块。
* **modules** 文件夹包含用于解决方案中每个模块的文件夹。 现在应该只有 **SampleModule**，或为模块提供的任何名称。 SampleModule 文件夹包含主程序代码、模块元数据和多个 Docker 文件。 
* **.env** 文件保存容器注册表凭据。 这些凭据与 IoT Edge 设备共享，以便它可以访问并拉取容器映像。 
* **deployment.debug.template.json** 文件和 **deployment.template.json** 文件是帮助创建部署清单的模板。 *部署清单*是一个文件，可准确定义想要在设备上部署的模块、应如何配置它们以及它们如何相互通信及与云通信。 模板文件使用指针来表示某些值。 将模板转换为真正的部署清单时，指针将替换为从其他解决方案文件中获取的值。 找到部署模板中的两个常用占位符： 

  * 在“注册表凭据”部分中，将根据创建解决方案时提供的信息自动填充地址。 但是，用户名和密码引用 .env 文件中存储的变量。 这是出于安全考量，因为 .env 文件被 Git 忽略，但部署模板未被忽略。 
  * 在“SampleModule”部分中，即使在创建解决方案时提供了映像存储库，也未填充容器映像。 此占位符指向 SampleModule 文件夹中的 **module.json** 文件。 如果转到该文件，将看到“映像”字段确实包含存储库，但也包含由容器的版本和平台组成的标记值。 可以在开发周期中手动迭代版本，并使用我们在本部分稍后介绍的开关选择容器平台。 

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>向 IoT Edge 代理提供注册表凭据

环境文件存储容器注册表的凭据，并将其与 IoT Edge 运行时共享。 运行时需要这些凭据才能将容器映像拉取到 IoT Edge 设备中。 

IoT Edge 扩展尝试从 Azure 中拉取容器注册表凭据并将其填充到环境文件中。 检查凭据是否已包含在内。 如果没有，请立即添加：

1. 打开模块解决方案中的 **.env** 文件。 
2. 添加从 Azure 容器注册表中复制的 **username** 和 **password** 值。
3. 将更改保存到 .env 文件。 

### <a name="select-your-target-architecture"></a>选择目标体系结构

目前，Visual Studio Code 可以为 Linux AMD64 和 Linux ARM32v7 设备开发 C 模块。 需要选择每种解决方案的目标体系结构，因为这会影响容器的生成和运行方式。 默认值为 Linux AMD64。 

1. 打开命令面板并搜索 **Azure IoT Edge:Set Default Target Platform for Edge Solution**，或选择窗口底部侧栏中的快捷方式图标。 

   ![选择侧栏中的体系结构图标](./media/tutorial-develop-for-linux/select-architecture.png)

2. 在命令面板中，从选项列表中选择目标体系结构。 在本教程中，我们使用 Ubuntu 虚拟机作为 IoT Edge 设备，因此将保留默认的“amd64”  。 

### <a name="review-the-sample-code"></a>查看示例代码

创建的解决方案模板包含 IoT Edge 模块的示例代码。 此示例模块仅接收消息，然后传递消息。 管道功能演示 IoT Edge 中的一个重要概念，即模块之间相互通信的方式。

每个模块可以在其代码中声明多个*输入*和*输出*队列。 在设备上运行的 IoT Edge 中心将来自一个模块的输出的消息路由到一个或多个模块的输入。 用于声明输入和输出的特定语言因语言而异，但所有模块的概念都相同。 有关在模块之间路由的详细信息，请参阅[声明路由](module-composition.md#declare-routes)。

1. 打开 **main.c** 文件，该文件位于 **modules/SampleModules/** 文件夹中。 

2. IoT 中心 C SDK 使用 [SetInputMessageCallback](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-setinputmessagecallback) 函数来初始化模块输入队列。 在 main.c 文件中搜索该函数。

3. 查看 SetInputMessageCallback 函数构造函数，将看到名为 **input1** 的输入队列在代码中初始化。 

   ![在 SetInputMessageCallback 构造函数中找到输入名称](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. 模块输出队列以类似的方式初始化。 在 main.c 文件中搜索 [SendEventToOutputAsync](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-sendeventtooutputasync) 函数。 

5. 查看 SendEventToOutputAsync 函数构造函数，将看到名为 **output1** 的输出队列在代码中初始化。 

   ![在 SendEventToOutputAsync 中找到输出名称](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. 打开 **deployment.template.json** 文件。

7. 找到 $edgeAgent 所需属性的 **modules** 属性。 

   此处应该列出两个模块。 第一个模块是 **tempSensor**，该模块默认包含在所有模板中，提供可用于测试模块的模拟温度数据。 第二个模块是作为此解决方案一部分创建的 **SampleModule** 模块。

7. 在文件底部，找到 **$edgeHub** 模块的所需属性。 

   IoT Edge 中心模块的其中一个函数可在部署中的所有模块之间路由消息。 查看 **routes** 属性中的值。 第一个路由 **SampleModuleToIoTHub** 使用通配符 ( **\*** ) 指示来自 SampleModule 模块中任何输出队列的任何消息。 这些消息进入 *$upstream*，后者是指示 IoT 中心的保留名称。 第二个路由 sensorToSampleModule 接收来自 tempSensor 模块的消息，并将它们路由到在 SampleModule 代码中初始化的 *input1* 输入队列。 

   ![查看 deployment.template.json 中的路由](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>生成并推送解决方案

你已查看模块代码和部署模板来了解一些关键部署概念。 现在，已准备好生成 SampleModule 容器映像并将其推送到容器注册表。 借助适用于 Visual Studio Code 的 IoT Tools 扩展，此步骤还会根据模板文件中的信息和解决方案文件中的模块信息生成部署清单。 

### <a name="sign-in-to-docker"></a>登录 Docker

向 Docker 提供容器注册表凭据，以便它可以推送要存储在注册表中的容器映像。 

1. 选择“查看” > “终端”，打开 Visual Studio Code 集成终端   。

2. 使用创建注册表后保存的 Azure 容器注册表凭据登录 Docker。 

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   可能会收到一条安全警告，推荐使用 `--password-stdin`。 这条最佳做法是针对生产方案建议的，这超出了本教程的范畴。 有关详细信息，请参阅 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 参考。

### <a name="build-and-push"></a>生成并推送 

Visual Studio Code 现在有权访问容器注册表，因此可以将解决方案代码转换为容器映像。 

1. 在 Visual Studio Code 资源管理器中右键单击 **deployment.template.json** 文件，然后选择“生成并推送 IoT Edge 解决方案”  。 

   ![生成并推送 IoT Edge 模块](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   “生成并推送”命令会启动三项操作。 首先，它在解决方案中创建名为 **config** 的新文件夹，用于保存基于部署模板和其他解决方案文件中的信息生成的完整部署清单。 其次，它会运行 `docker build`，以基于目标体系结构的相应 dockerfile 生成容器映像。 然后，它会运行 `docker push`，以将映像存储库推送到容器注册表。 

   首次执行此过程可能需要几分钟时间，但下次运行命令时速度会变快。 

2. 打开新建的 config 文件夹中的 **deployment.amd64.json** 文件。 文件名反映目标体系结构，因此，如果选择其他体系结构，则会有所不同。

3. 请注意，具有占位符的两个参数现在都填充了适当的值。 **registryCredentials** 部分具有从 .env 文件中拉取的注册表用户名和密码。 **SampleModule** 具有完整的映像存储库，以及 module.json 文件的名称、版本和体系结构标记。 

4. 打开 SampleModule 文件夹中的 **module.json** 文件。 

5. 更改模块映像的版本号。 （版本，而不是 $schema-version。）例如，将补丁版本号递增为 **0.0.2**，就像我们对模块代码进行了小修补一样。 

   >[!TIP]
   >模块版本支持版本控制，允许在将更新部署到生产环境之前在少量设备上测试更改。 如果在生成并推送之前未递增模块版本，则会替代容器注册表中的存储库。 

6. 将更改保存到 module.json 文件。

7. 再次右键单击 **deployment.template.json** 文件，然后再次选择“生成并推送 IoT Edge 解决方案”  。

8. 再次打开 **deployment.amd64.json** 文件。 请注意，再次运行“生成并推送”命令时未创建新文件， 而是更新了同一文件以反映更改。 SampleModule 映像现在指向容器的 0.0.2 版。 

9. 若要进一步验证“生成并推送”命令执行的操作，请转到 [Azure 门户](https://portal.azure.com)并导航到容器注册表。 

10. 在容器注册表中，依次选择“存储库”和“samplemodule”   。 验证映像的两个版本是否都已推送到注册表。

   ![在容器注册表中查看两个映像版本](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>故障排除

如果在生成并推送模块映像时遇到错误，这通常与开发计算机上的 Docker 配置有关。 使用以下检查来检查配置： 

* 是否使用从容器注册表复制的凭据运行 `docker login` 命令？ 这些凭据与用于登录 Azure 的凭据不同。 
* 你的容器存储库是否正确？ 它是否拥有正确的容器注册表名称和正确的模块名称？ 打开 SampleModule 文件夹中的 **module.json** 文件进行检查。 存储库值应类似于 **\<registry name\>.azurecr.io/samplemodule**。 
* 如果为模块使用的名称不是 **SampleModule**，那么使用的名称在整个解决方案中是否保持一致？
* 计算机运行的容器类型与要生成的容器类型是否相同？ 本教程适用于 Linux IoT Edge 设备，因此，Visual Studio Code 应在侧栏指明 **amd64** 或 **arm32v7**，且 Docker Desktop 应运行 Linux 容器。 Visual Studio Code 中的 C 模块不支持 Windows 容器。 

## <a name="deploy-modules-to-device"></a>将模块部署到设备

因为已确认生成的容器映像存储在容器注册表中，所以现在可将它们部署到设备中。 确保 IoT Edge 设备已启动并运行。 

1. 在 Visual Studio Code 资源管理器中，展开“Azure IoT 中心设备”部分。 

2. 右键单击想要在其中部署的 IoT Edge 设备，然后选择“为单个设备创建部署”  。 

   ![为单个设备创建部署](./media/tutorial-develop-for-linux/create-deployment.png)

3. 在文件资源管理器中，导航到 **config** 文件夹，然后选择 **deployment.amd64.json** 文件。 

   不要使用 deployment.template.json 文件，该文件不包含容器注册表凭据或模块映像值。 如果面向 Linux ARM32 设备，则部署清单将命名为 deployment.arm32v7.json。 

4. 展开 IoT Edge 设备的详细信息，然后展开设备的“模块”列表  。 

5. 使用“刷新”按钮更新设备视图，直至看到设备上运行 tempSensor 和 SampleModule 模块。 

   启动两个模块可能需要数分钟时间。 IoT Edge 运行时需要接收其新的部署清单，从容器运行时下拉模块映像，然后启动每个新模块。 

   ![查看在 IoT Edge 设备上运行的模块](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>查看设备消息

SampleModule 代码通过其输入队列接收消息，并通过其输出队列传递消息。 部署清单声明了从 tempSensor 将消息传递到 SampleModule，再将消息从 SampleModule 转发到 IoT 中心的路由。 适用于 Visual Studio Code 的 Azure IoT Tools 允许查看从各个设备到达 IoT 中心的消息。 

1. 在 Visual Studio Code 资源管理器中，右键单击想要监视的 IoT Edge 设备，然后选择“开始监视内置事件终结点”  。 

2. 观察 Visual Studio Code 的输出窗口，查看到达 IoT 中心的消息。 

   ![查看传入的设备到云消息](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>查看设备上的更改

如果想要查看设备本身发生的情况，请使用本部分中的命令检查设备上运行的 IoT Edge 运行时和模块。 

本部分中的命令适用于 IoT Edge 设备，而不适用于开发计算机。 如果为 IoT Edge 设备使用虚拟机，请立即连接到虚拟机。 在 Azure 中，转到虚拟机的“概述”页，然后选择“连接”以访问安全 shell 连接  。 

* 查看部署到设备的所有模块，并检查其状态：

   ```bash
   iotedge list
   ```

   应该看到四个模块：两个 IoT Edge 运行时模块、tempSensor 和 SampleModule。 全部四个模块都应列为“正在运行”。

* 检查特定模块的日志：

   ```bash
   iotedge logs <module name>
   ```

   IoT Edge 模块区分大小写。 

   tempSensor 和 SamplModule 日志应显示其正在处理的消息。 edgeAgent 模块负责启动其他模块，因此其日志将包含有关实施部署清单的信息。 如果有任何模块未列出或未运行，则 edgeAgent 日志可能包含相关错误信息。 edgeHub 模块负责模块和 IoT 中心之间的通信。 如果模块已启动并运行，但消息未到达 IoT 中心，则 edgeHub 日志可能包含相关错误信息。 

## <a name="next-steps"></a>后续步骤

在本教程中，在开发计算机上设置了 Visual Studio Code 并从中部署了第一个 IoT Edge 模块。 你已经了解基本概念，请尝试向模块添加功能，以便它可以分析通过它传递的数据。 选择首选语言： 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java](tutorial-java-module.md)
> [Node.js](tutorial-node-module.md)
> [Python](tutorial-python-module.md)