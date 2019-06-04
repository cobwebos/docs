---
title: 开发适用于 Windows 设备的模块 - Azure IoT Edge | Microsoft Docs
description: 本教程详细介绍如何设置开发计算机和云资源来使用适用于 Windows 设备的 Windows 容器开发 IoT Edge 模块
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/20/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 891b64b8e31266360d718255dcd8e8a1f9fb597c
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306582"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>教程：开发适用于 Windows 设备的 IoT Edge 模块

使用 Visual Studio 开发代码并将其部署到运行 IoT Edge 的 Windows 设备。

在快速入门中，使用 Windows 虚拟机创建了 IoT Edge 设备，并部署了来自 Azure 市场的预构建模块。 本教程详细介绍如何才能开发自己的代码并将其部署到 IoT Edge 设备。 本教程是所有其他教程的有用系统必备组件，其将详细介绍特定编程语言或 Azure 服务。 

本教程使用**将 C 模块部署到 Windows 设备**的示例。 此示例因其简洁性而被选中，以便你可以了解开发工具，而无需担心是否安装了正确的库。 了解开发概念后，即可选择首选语言或 Azure 服务来深入了解详细信息。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置开发计算机。
> * 使用适用于 Visual Studio 的 IoT Edge Tools 创建新项目。
> * 将项目作为容器生成并将其存储在 Azure 容器注册表中。
> * 将代码部署到 IoT Edge 设备。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>关键概念

本教程详细介绍如何开发 IoT Edge 模块。 *IoT Edge 模块*有时简称*模块*，它是一个包含可执行代码的容器。 可以将一个或多个模块部署到 IoT Edge 设备。 模块可执行特定任务，例如从传感器引入数据、执行数据分析或数据清理操作，或将消息发送到 IoT 中心。 有关详细信息，请参阅[了解 Azure IoT Edge 模块](iot-edge-modules.md)。

开发 IoT Edge 模块时，了解开发计算机和最终将部署模块的目标 IoT Edge 设备之间的差异非常重要。 为保存模块代码而生成的容器必须与*目标设备*的操作系统 (OS) 匹配。 对于 Windows 容器开发，此概念更为简单，因为 Windows 容器仅在 Windows 操作系统上运行。 但是，可以使用 Windows 开发计算机生成适用于 Linux IoT Edge 设备的模块。 在该方案中，必须确保开发计算机运行 Linux 容器。 在学习本教程的过程中，请记住*开发计算机 OS* 和*容器 OS* 之间的差异。

本教程针对运行 IoT Edge 的 Windows 设备。 Windows IoT Edge 设备使用 Windows 容器。 我们建议使用 Visual Studio 进行 Windows 设备开发，这也是本教程将使用的工具。 还可以使用 Visual Studio Code，但两个工具提供的支持存在差异。

下表列出 Visual Studio Code 和 Visual Studio 支持的 **Windows 容器**开发方案。

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Azure 服务** | Azure Functions <br> Azure 流分析 |   |
| **语言** | C#（不支持调试） | C <br> C# |
| **详细信息** | [适用于 Visual Studio Code 的 Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [适用于 Visual Studio 2017 的 Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)、[适用于 Visual Studio 2019 的 Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

本教程讲解适用于 Visual Studio 2019 的开发步骤。 如果想要使用 Visual Studio Code，请参阅[使用 Visual Studio Code 为 Azure IoT Edge 开发和调试模块](how-to-vs-code-develop-module.md)中的说明。 如果使用的是 Visual Studio 2017（15.7 或更高版本），请下载并安装 [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)。

## <a name="prerequisites"></a>先决条件

开发计算机：

* 具有 1809 更新或更高版本更新的 Windows 10。
* 可以使用自己的计算机或虚拟机，具体取决于开发首选项。
* 安装 [Git](https://git-scm.com/)。 
* 通过 vcpkg 安装适用于 Windows x64 的 Azure IoT C SDK：

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

<!--vcpkg only required for C development-->

Windows 上的 Azure IoT Edge 设备：

* 我们建议不要在开发计算机上运行 IoT Edge，而应使用单独的设备。 开发计算机和 IoT Edge 设备之间的这一区分可更准确地反映真实部署方案，并有助于区分不同的概念。
* 如果没有第二台可用的设备，请按照快速入门文章的说明在 Azure 中使用 [Windows 虚拟机](quickstart.md)创建 IoT Edge 设备。

云资源：

* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。 

## <a name="install-container-engine"></a>安装容器引擎

IoT Edge 模块被打包为容器，因此，需要在开发计算机上安装容器引擎来生成和管理容器。 我们建议使用 Docker Desktop 进行开发，因为它作为容器引擎拥有许多功能且广受欢迎。 借助 Windows 计算机上的 Docker Desktop，可在 Linux 容器和 Windows 容器之间切换，以便轻松地为不同类型的 IoT Edge 设备开发模块。 

按照 Docker 文档的说明在开发计算机上安装： 

* [安装适用于 Windows 的 Docker Desktop](https://docs.docker.com/docker-for-windows/install/)

  * 安装适用于 Windows 的 Docker Desktop 时，系统会询问是想要使用 Linux 容器还是 Windows 容器。 对于本教程，请使用 **Windows 容器**。 有关详细信息，请参阅[在 Windows 容器和 Linux 容器之间切换](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)。


## <a name="set-up-visual-studio-and-tools"></a>设置 Visual Studio 和工具

使用适用于 Visual Studio 2019 的 IoT 扩展开发 IoT Edge 模块。 这些扩展提供项目模板、自动化部署清单的创建，并允许监视和管理 IoT Edge 设备。 本部分将安装 Visual Studio 和 IoT Edge 扩展，然后设置 Azure 帐户用于在 Visual Studio 中管理 IoT 中心资源。 

1. 如果开发计算机上尚未安装 Visual Studio，请[安装 Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) 及以下工作负荷： 

   * Azure 开发
   * 使用 C++ 的桌面开发
   * .NET Core 跨平台开发

1. 如果开发计算机上已安装 Visual Studio 2019。 如果还没有所需的工作负荷，请按照[修改 Visual Studio](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) 中的步骤进行添加。

2. 下载并安装适用于 Visual Studio 2019 的 [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) 扩展。 

3. 安装完成后，打开 Visual Studio 2019 并选择“在没有代码的情况下继续”。 

4. 选择“查看” > “Cloud Explorer”   。 

5. 如果尚未登录，请在 Cloud Explorer 中选择配置文件图标并登录 Azure 帐户。 

6. 登录后，系统会列出你的 Azure 订阅。 选择想要通过 Cloud Explorer 访问的订阅，然后选择“应用”  。 

7. 依次展开你的订阅、“IoT 中心”和你的 IoT 中心  。 应看到 IoT 设备列表，可使用此资源管理器来管理它们。 

   ![在 Cloud Explorer 中访问 IoT 中心资源](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>创建新的模块项目

Azure IoT Edge Tools 扩展为 Visual Studio 中支持的所有 IoT Edge 模块语言提供项目模板。 这些模板包含将工作模块部署到测试 IoT Edge 所需的所有文件和代码，或为你提供使用自己的业务逻辑自定义模板的起点。 

1. 选择“文件” > “新建” > “项目...”   

2. 在“新建项目”窗口中，2. 在“新建项目”窗口中，搜索“IoT Edge”  项目，然后选择“Azure IoT Edge (Windows amd64)”  项目。 单击“下一步”。  

   ![创建新的 Azure IoT Edge 项目](./media/tutorial-develop-for-windows/new-project.png)

3. 在“配置新项目”窗口中，重命名项目和解决方案，使名称具有描述性，例如 **CTutorialApp**。 单击“创建”以创建项目。 

   ![配置新的 Azure IoT Edge 项目](./media/tutorial-develop-for-windows/configure-project.png)
 

4. 在 IoT Edge 应用程序和模块窗口中，使用以下值配置项目： 

   | 字段 | 值 |
   | ----- | ----- |

   | 选择模板 | 选择“C 模块”。  | | 模块项目名称 | 接受默认的 **IoTEdgeModule1**。 | | Docker 映像存储库 | 映像存储库包含容器注册表的名称和容器映像的名称。 系统已基于模块项目名称值预先填充容器映像。 将 **localhost:5000** 替换为 Azure 容器注册表中的登录服务器值。 可以在 Azure 门户的容器注册表的“概览”页中检索登录服务器。 <br><br> 最终的映像存储库看起来类似于 \<registry name\>.azurecr.io/iotedgemodule1。 |

   ![针对目标设备、模块类型和容器注册表配置项目](./media/tutorial-develop-for-windows/add-application-and-module.png)

5. 选择“确定”以应用更改  。 

在 Visual Studio 窗口中加载新项目后，请花时间熟悉其创建的文件： 

* 名为 **AzureIoTEdgeApp1.Windows.Amd64** 的 IoT Edge 项目。
    * 包含指向项目所含模块的指针的 **Modules** 文件夹。 在本例中，应该只有 IoTEdgeModule1。 
    * **deployment.template.json** 文件是可帮助创建部署清单的模板。 *部署清单*是一个文件，可准确定义想要在设备上部署的模块、应如何配置它们以及它们如何相互通信及与云通信。 
* 名为 **IoTEdgeModule1** 的 IoT Edge 模块项目。
    * **main.c** 文件包含项目模板随附的默认 C 模块代码。 默认模块从源获取输入并将其传递到 IoT 中心。 
    * **module.json** 文件保存有关模块的详细信息，其中包括完整的映像存储库、映像版本以及用于每个受支持平台的 Dockerfile。

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>向 IoT Edge 代理提供注册表凭据

IoT Edge 运行时需要注册表凭据才能将容器映像拉取到 IoT Edge 设备中。 将这些凭据添加到部署模板。 

1. 打开 **deployment.template.json** 文件。

2. 在 $edgeAgent 所需属性中找到 **registryCredentials** 属性。 

3. 使用凭据更新该属性，遵循以下格式： 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }

4. Save the deployment.template.json file. 

### Review the sample code

The solution template that you created includes sample code for an IoT Edge module. This sample module simply receives messages and then passes them on. The pipeline functionality demonstrates an important concept in IoT Edge, which is how modules communicate with each other.

Each module can have multiple *input* and *output* queues declared in their code. The IoT Edge hub running on the device routes messages from the output of one module into the input of one or more modules. The specific language for declaring inputs and outputs varies between languages, but the concept is the same across all modules. For more information about routing between modules, see [Declare routes](module-composition.md#declare-routes).

1. In the **main.c** file, find the **SetupCallbacksForModule** function.

2. This function sets up an input queue to receive incoming messages. It calls the C SDK module client function [SetInputMessageCallback](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-setinputmessagecallback). Review this function and see that it initializes an input queue called **input1**. 

   ![Find the input name in the SetInputMessageCallback constructor](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Next, find the **InputQueue1Callback** function.

4. This function processes received messages and sets up an output queue to pass them along. It calls the C SDK module client function [SendEventToOutputAsync](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-sendeventtooutputasync). Review this function and see that it initializes an output queue called **output1**. 

   ![Find the output name in the SendEventToOutputAsync constructor](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Open the **deployment.template.json** file.

6. Find the **modules** property of the $edgeAgent desired properties. 

   There should be two modules listed here. The first is **tempSensor**, which is included in all the templates by default to provide simulated temperature data that you can use to test your modules. The second is the **IotEdgeModule1** module that you created as part of this project.

   This modules property declares which modules should be included in the deployment to your device or devices. 

7. Find the **routes** property of the $edgeHub desired properties. 

   One of the functions if the IoT Edge hub module is to route messages between all the modules in a deployment. Review the values in the routes property. The first route, **IotEdgeModule1ToIoTHub**, uses a wildcard character (**\***) to include any message coming from any output queue in the IoTEdgeModule1 module. These messages go into *$upstream*, which is a reserved name that indicates IoT Hub. The second route, **sensorToIotEdgeModule1**, takes messages coming from the tempSensor module and routes them to the *input1* input queue of the IotEdgeModule1 module. 

   ![Review routes in deployment.template.json](./media/tutorial-develop-for-windows/deployment-routes.png)


## Build and push your solution

You've reviewed the module code and the deployment template to understand some key deployment concepts. Now, you're ready to build the IotEdgeModule1 container image and push it to your container registry. With the IoT tools extension for Visual Studio, this step also generates the deployment manifest based on the information in the template file and the module information from the solution files. 

### Sign in to Docker

Provide your container registry credentials to Docker on your development machine so that it can push your container image to be stored in the registry. 

1. Open PowerShell or a command prompt.

2. Sign in to Docker with the Azure container registry credentials that you saved after creating the registry. 

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   可能会收到一条安全警告，推荐使用 `--password-stdin`。 这条最佳做法是针对生产方案建议的，这超出了本教程的范畴。 有关详细信息，请参阅 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 参考。

### <a name="build-and-push"></a>生成并推送

开发计算机现在可以访问容器注册表，IoT Edge 设备也将拥有相应访问权限。 现在可将项目代码转换为容器映像。 

1. 右键单击 **AzureIotEdgeApp1.Windows.Amd64** 项目文件夹，然后选择“生成并推送 IoT Edge 模块”  。 

   ![生成并推送 IoT Edge 模块](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   “生成并推送”命令会启动三项操作。 首先，它在解决方案中创建名为 **config** 的新文件夹，用于保存基于部署模板和其他解决方案文件中的信息生成的完整部署清单。 其次，它会运行 `docker build`，以基于目标体系结构的相应 dockerfile 生成容器映像。 然后，它会运行 `docker push`，以将映像存储库推送到容器注册表。 

   首次执行此过程可能需要几分钟时间，但下次运行命令时速度会变快。 

2. 打开新建的 config 文件夹中的 **deployment.windows-amd64.json** 文件。 （config 文件夹可能不会在 Visual Studio 的解决方案资源管理器中显示。 如果遇到这种情况，请选择解决方案资源管理器任务栏中的**显示所有文件**图标。）

3. 找到 IotEdgeModule1 部分的 **image** 参数。 请注意，该映像包含完整的映像存储库，以及 module.json 文件的名称、版本和体系结构标记。

4. 打开 IotEdgeModule1 文件夹中的 **module.json** 文件。 

5. 更改模块映像的版本号。 （版本，而不是 $schema-version。）例如，将补丁版本号递增为 **0.0.2**，就像我们对模块代码进行了小修补一样。 

   >[!TIP]
   >模块版本支持版本控制，允许在将更新部署到生产环境之前在少量设备上测试更改。 如果在生成并推送之前未递增模块版本，则会替代容器注册表中的存储库。 

6. 将更改保存到 module.json 文件。

7. 再次右键单击 **AzureIotEdgeApp1.Windows.Amd64** 项目文件夹，然后再次选择“生成并推送 IoT Edge 模块”  。 

8. 再次打开 **deployment.windows-amd64.json** 文件。 请注意，再次运行“生成并推送”命令时未创建新文件， 而是更新了同一文件以反映更改。 IotEdgeModule1 映像现指向容器的 0.0.2 版。 部署清单中的此更改告知 IoT Edge 设备有要拉取的新模块版本。 

9. 若要进一步验证“生成并推送”命令执行的操作，请转到 [Azure 门户](https://portal.azure.com)并导航到容器注册表。 

10. 在容器注册表中，依次选择“存储库”和“iotedgemodule1”   。 验证映像的两个版本是否都已推送到注册表。

    ![在容器注册表中查看两个映像版本](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>故障排除

如果在生成并推送模块映像时遇到错误，这通常与开发计算机上的 Docker 配置有关。 使用以下检查来检查配置： 

* 是否使用从容器注册表复制的凭据运行 `docker login` 命令？ 这些凭据与用于登录 Azure 的凭据不同。 
* 你的容器存储库是否正确？ 它是否拥有正确的容器注册表名称和正确的模块名称？ 打开 IotEdgeModule1 文件夹中的 **module.json** 文件进行检查。 存储库值应类似于 **\<registry name\>.azurecr.io/iotedgemodule1**。 
* 如果为模块使用的名称不是 **IotEdgeModule1**，那么使用的名称在整个解决方案中是否保持一致？
* 计算机运行的容器类型与要生成的容器类型是否相同？ 本教程适用于 Windows IoT Edge 设备，因此，Visual Studio 文件应具有 **windows-amd64** 扩展名，且 Docker Desktop 应运行 Windows 容器。 

## <a name="deploy-modules-to-device"></a>将模块部署到设备

因为已确认生成的容器映像存储在容器注册表中，所以现在可将它们部署到设备中。 确保 IoT Edge 设备已启动并运行。 

1. 在 Visual Studio 中打开 Cloud Explorer 并展开 IoT 中心的详细信息。 

2. 选择想要在其中部署的设备的名称。 在“操作”列表中，选择“创建部署”   。

   ![为单个设备创建部署](./media/tutorial-develop-for-windows/create-deployment.png)


3. 在文件资源管理器中，导航到项目的 config 文件夹，然后选择 **deployment.windows-amd64.json** 文件。 此文件通常位于 `C:\Users\<username>\source\repos\AzureIotEdgeApp1\AzureIotEdgeApp1.Windows.Amd64\config\deployment.windows-amd64.json`

   不要使用 deployment.template.json 文件，该文件不包含完整的模块映像值。 

4. 在 Cloud Explorer 中展开 IoT Edge 设备的详细信息，以查看设备上的模块。

5. 使用“刷新”按钮更新设备状态，以查看 tempSensor 和 IotEdgeModule1 模块是否已部署到设备  。 


   ![查看在 IoT Edge 设备上运行的模块](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>查看设备消息

IotEdgeModule1 代码通过其输入队列接收消息，并通过其输出队列传递消息。 部署清单声明了将消息从 tempSensor 传递到 IotEdgeModule1，再将消息从 IotEdgeModule1 转发到 IoT 中心的路由。 适用于 Visual Studio 的 Azure IoT Edge Tools 允许查看从各个设备到达 IoT 中心的消息。 

1. 在 Visual Studio Cloud Explorer 中，选择部署到其中的 IoT Edge 设备的名称。 

2. 在“操作”菜单中，选择“开始监视内置事件终结点”。  

3. 观察 Visual Studio 中的“输出”部分，以查看到达 IoT 中心的消息  。 

   启动两个模块可能需要数分钟时间。 IoT Edge 运行时需要接收其新的部署清单，从容器运行时下拉模块映像，然后启动每个新模块。 如果你 

   ![查看传入的设备到云消息](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>查看设备上的更改

如果想要查看设备本身发生的情况，请使用本部分中的命令检查设备上运行的 IoT Edge 运行时和模块。 

本部分中的命令适用于 IoT Edge 设备，而不适用于开发计算机。 如果为 IoT Edge 设备使用虚拟机，请立即连接到虚拟机。 在 Azure 中，转到虚拟机的“概述”页，然后选择“连接”以访问远程桌面连接  。 在设备上，打开命令或 PowerShell 窗口以运行 `iotedge` 命令。

* 查看部署到设备的所有模块，并检查其状态：

   ```cmd
   iotedge list
   ```

   应该看到四个模块：两个 IoT Edge 运行时模块、tempSensor 和 IotEdgeModule1。 全部四个模块都应列为“正在运行”。

* 检查特定模块的日志：

   ```cmd
   iotedge logs <module name>
   ```

   IoT Edge 模块区分大小写。 

   tempSensor 和 IotEdgeModule1 日志应显示其正在处理的消息。 edgeAgent 模块负责启动其他模块，因此其日志将包含有关实施部署清单的信息。 如果有任何模块未列出或未运行，则 edgeAgent 日志可能包含相关错误信息。 edgeHub 模块负责模块和 IoT 中心之间的通信。 如果模块已启动并运行，但消息未到达 IoT 中心，则 edgeHub 日志可能包含相关错误信息。 

## <a name="next-steps"></a>后续步骤

在本教程中，在开发计算机上设置了 Visual Studio 2019 并从中部署了第一个 IoT Edge 模块。 你已经了解基本概念，请尝试向模块添加功能，以便它可以分析通过它传递的数据。 选择首选语言： 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)