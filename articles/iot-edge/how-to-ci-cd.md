---
title: Azure IoT Edge 持续集成和持续部署 | Microsoft Docs
description: Azure IoT Edge 持续集成和持续部署的概述
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 62d8d770f6b4c3a62a2395eb8c1505dbc3835c28
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37047449"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>向 Azure IoT Edge 进行持续集成和持续部署

本文演示如何使用 Visual Studio Team Services (VSTS) 和 Microsoft Team Foundation Server (TFS) 的持续集成和持续部署功能，快速高效地向 Azure IoT Edge 生成、测试并部署应用程序。 

本文介绍如何执行以下操作：
* 创建并签入包含单元测试的示例 IoT Edge 解决方案。
* 安装适用于 VSTS 的 Azure IoT Edge 扩展。
* 配置持续集成 (CI) 以生成解决方案并运行单元测试。
* 配置持续部署 (CD) 以部署解决方案并查看响应。

完成本文中的步骤需要 30 分钟。

![CI 和 CD](./media/how-to-ci-cd/cd.png)

## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>使用 Visual Studio Code 创建示例 Azure IoT Edge 解决方案

在本部分中，创建一个示例 IoT Edge 解决方案，其中包含可在生成过程中执行的单元测试。 在按照本部分中的指导进行操作之前，请先完成[使用 Visual Studio Code 中的多个模块开发 IoT Edge 解决方案](tutorial-multiple-modules-in-vscode.md)中的步骤。

1. 在 VS Code 命令面板中，键入并运行命令 **Edge: New IoT Edge solution**。 然后，选择你的工作区文件夹，提供解决方案名称（默认名称为 EdgeSolution），并创建一个 C# 模块 (FilterModule) 作为此解决方案中的第一个用户模块。 还需要为你的第一个模块指定 Docker 映像存储库。 默认映像存储库基于本地 Docker 注册表 (`localhost:5000/filtermodule`)。 为了进一步的持续集成，需要将其更改为 Azure 容器注册表 (`<your container registry address>/filtermodule`) 或 Docker 中心。

    ![安装程序 ACR](./media/how-to-ci-cd/acr.png)

2. VS Code 窗口将加载你的 IoT Edge 解决方案空间。 可以选择键入并运行“Edge：添加 IoT Edge 模块”以添加更多模块。 根文件夹中有一个 `modules` 文件夹、一个 `.vscode` 文件夹和一个部署清单模板文件。 所有用户模块代码都将成为文件夹 `modules` 下的子文件夹。 `deployment.template.json` 是部署清单模板。 此文件中的某些参数将通过 `module.json` 进行分析，它存在于每个模块文件夹中。

3. 现在，示例 IoT Edge 解决方案已准备就绪。 默认 C# 模块充当管道消息模块。 在 `deployment.template.json` 中，你将看到此解决方案包含两个模块。 消息将从 `tempSensor` 模块生成，并且将通过 `FilterModule` 直接输送，然后发送到 IoT 中心。 用以下内容替换整个 Program.cs 文件。 有关此代码片段的详细信息，可以参阅[创建 IoT Edge C# 模块项目](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module#create-an-iot-edge-module-project)。

    ```csharp
    namespace FilterModule
    {
        using System;
        using System.IO;
        using System.Runtime.InteropServices;
        using System.Runtime.Loader;
        using System.Security.Cryptography.X509Certificates;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using System.Collections.Generic;     // for KeyValuePair<>
        using Microsoft.Azure.Devices.Shared; // for TwinCollection
        using Newtonsoft.Json;                // for JsonConvert

        public class MessageBody
        {
            public Machine machine { get; set; }
            public Ambient ambient { get; set; }
            public string timeCreated { get; set; }
        }
        public class Machine
        {
            public double temperature { get; set; }
            public double pressure { get; set; }
        }
        public class Ambient
        {
            public double temperature { get; set; }
            public int humidity { get; set; }
        }

        public class Program
        {
            static int counter;
            static int temperatureThreshold { get; set; } = 25;

            static void Main(string[] args)
            {
                Init().Wait();

                // Wait until the app unloads or is cancelled
                var cts = new CancellationTokenSource();
                AssemblyLoadContext.Default.Unloading += (ctx) => cts.Cancel();
                Console.CancelKeyPress += (sender, cpe) => cts.Cancel();
                WhenCancelled(cts.Token).Wait();
            }

            /// <summary>
            /// Handles cleanup operations when app is cancelled or unloads
            /// </summary>
            public static Task WhenCancelled(CancellationToken cancellationToken)
            {
                var tcs = new TaskCompletionSource<bool>();
                cancellationToken.Register(s => ((TaskCompletionSource<bool>)s).SetResult(true), tcs);
                return tcs.Task;
            }

            /// <summary>
            /// Initializes the ModuleClient and sets up the callback to receive
            /// messages containing temperature information
            /// </summary>
            static async Task Init()
            {
                MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
                ITransportSettings[] settings = { mqttSetting };

                // Open a connection to the Edge runtime
                ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
                await ioTHubModuleClient.OpenAsync();
                Console.WriteLine("IoT Hub module client initialized.");

                // Register callback to be called when a message is received by the module
                await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessage, ioTHubModuleClient);
            }

            /// <summary>
            /// This method is called whenever the module is sent a message from the EdgeHub. 
            /// It just pipe the messages without any change.
            /// It prints all the incoming messages.
            /// </summary>
            static async Task<MessageResponse> FilterMessage(Message message, object userContext)
            {
                int counterValue = Interlocked.Increment(ref counter);

                var moduleClient = userContext as ModuleClient;
                if (moduleClient == null)
                {
                    throw new InvalidOperationException("UserContext doesn't contain " + "expected values");
                }

                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                var filteredMessage = filter(message);

                if (filteredMessage != null && !string.IsNullOrEmpty(messageString))
                {
                    var pipeMessage = new Message(messageBytes);
                    foreach (var prop in message.Properties)
                    {
                        pipeMessage.Properties.Add(prop.Key, prop.Value);
                    }
                    await moduleClient.SendEventAsync("output1", pipeMessage);
                    Console.WriteLine("Received message sent");
                }
                return MessageResponse.Completed;
            }

            public static Message filter(Message message)
            {
                var counterValue = Interlocked.Increment(ref counter);

                var messageBytes = message.GetBytes();
                var messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message {counterValue}: [{messageString}]");

                // Get message body
                var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                {
                    Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                        $"exceeds threshold {temperatureThreshold}");
                    var filteredMessage = new Message(messageBytes);
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    return filteredMessage;
                }
                return null;
            }
        }
    }
    ```

4. 创建 .Net Core 单元测试项目。 在 VS Code 文件资源管理器中，在工作区新建一个文件夹 tests\FilterModuleTest。 然后在 VS Code 集成终端 (Ctrl + `) 中运行以下命令，创建 xUnit 测试项目并将引用添加至 FilterModule 项目。

    ```cmd
    cd tests\FilterModuleTest
    dotnet new xunit
    dotnet add reference ../../modules/FilterModule/FilterModule.csproj
    ```

    ![文件夹结构](./media/how-to-ci-cd/add-test-project.png)

5. 在 FilterModuleTest 文件夹中，将文件名从 UnitTest1.cs 更新为 FilterModuleTest.cs。 选择并打开 FilterModuleTest.cs，使用以下代码片段替换整个代码，该代码片段包含针对 FilterModule 项目的单元测试。

    ```csharp
    using Xunit;
    using FilterModule;
    using Newtonsoft.Json;
    using System;
    using System.IO;
    using System.Runtime.InteropServices;
    using System.Runtime.Loader;
    using System.Security.Cryptography.X509Certificates;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Client.Transport.Mqtt;

    namespace FilterModuleTest
    {
        public class FilterModuleTest
        {
            [Fact]
            public void filterLessThanThresholdTest()
            {
                var source = createMessage(25 - 1);
                var result = Program.filter(source);
                Assert.True(result == null);
            }

            [Fact]
            public void filterMoreThanThresholdAlertPropertyTest()
            {
                var source = createMessage(25 + 1);
                var result = Program.filter(source);
                Assert.True(result.Properties["MessageType"] == "Alert");
            }

            [Fact]
            public void filterMoreThanThresholdCopyPropertyTest()
            {
                var source = createMessage(25 + 1);
                source.Properties.Add("customTestKey", "customTestValue");
                var result = Program.filter(source);
                Assert.True(result.Properties["customTestKey"] == "customTestValue");
            }

            private Message createMessage(int temperature)
            {
                var messageBody = createMessageBody(temperature);
                var messageString = JsonConvert.SerializeObject(messageBody);
                var messageBytes = Encoding.UTF8.GetBytes(messageString);
                return new Message(messageBytes);
            }

            private MessageBody createMessageBody(int temperature)
            {
                var messageBody = new MessageBody
                {
                    machine = new Machine
                    {
                        temperature = temperature,
                        pressure = 0
                    },
                    ambient = new Ambient
                    {
                        temperature = 0,
                        humidity = 0
                    },
                    timeCreated = string.Format("{0:O}", DateTime.Now)
                };

                return messageBody;
            }
        }
    }
    ```

6. 可以在集成终端输入以下命令来本地运行单元测试。 
    ```cmd
    dotnet test
    ```

    ![单元测试](./media/how-to-ci-cd/unit-test.png)

7. 保存这些项目，然后将其签入至 VSTS 或 TFS 存储库。
    

> [!NOTE]
> 有关使用 VSTS 代码存储库的详细信息，请参阅 [Share your code with Visual Studio and VSTS Git](https://docs.microsoft.com/vsts/git/share-your-code-in-git-vs?view=vsts)（与 Visual Studio 和 VSTS Git 共享代码）。


## <a name="configure-continuous-integration"></a>配置持续集成
在本部分中创建一个生成定义，此定义配置为在将任何更改签入示例 IoT Edge 解决方案时自动运行，且它会自动执行所包含的单元测试。

1. 登录 VSTS 帐户 (https://_your-account_.visualstudio.com) 并打开签入了示例应用的项目。

    ![签入代码](./media/how-to-ci-cd/init-project.png)

1. 访问 VSTS Marketplace 上的 [Azure IoT Edge For VSTS](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy)（适用于 VSTS 的 Azure IoT Edge）。 单击“免费获取”并按照向导操作，将此扩展安装至 VSTS 帐户或下载到 TFS。

    ![安装扩展](./media/how-to-ci-cd/install-extension.png)

1. 在 VSTS 中打开“生成和发布”中心，并在“生成”选项卡中选择“+ 新建定义”**&amp;**。 或者，如果已有生成定义，则选择“+ 新建”按钮。 

    ![新建生成](./media/how-to-ci-cd/add-new-build.png)

1. 若出现提示，选择“VSTS Git”资源类型，然后选择代码所在的项目、存储库和分支。 选择“继续”。

    ![选择 VSTS git](./media/how-to-ci-cd/select-vsts-git.png)

1. 在“选择模板”窗口中选择“从空进程开始”。

    ![从空进程开始](./media/how-to-ci-cd/start-with-empty.png)

1. 单击“阶段 1”右侧的 +，向该阶段添加任务。 然后搜索并选择“.Net Core”并单击“添加”，将此任务添加至该阶段。

    ![Dotnet 测试](./media/how-to-ci-cd/add-dot-net-core.png)

1. 将“显示名称”更新为“dotnet 测试”，并在“命令”下拉列表中选择“测试”。 将下面的路径添加到“项目路径”。

    ```
    tests/FilterModuleTest/*.csproj
    ```

    ![配置 dotnet 测试](./media/how-to-ci-cd/dotnet-test.png)

1. 单击“阶段 1”右侧的 +，向该阶段添加任务。 然后搜索并选择“Azure IoT Edge”并单击“添加”按钮两次，将这些任务添加到该阶段。

    ![IoT Edge](./media/how-to-ci-cd/add-azure-iot-edge.png)

1. 在第一个 Azure IoT Edge 任务中，将“显示名称”更新为“模块生成和推送”，并在“操作”下拉列表中选择“生成并推送”。 在“Module.json 文件”文本框中添加下面的路径。 然后选择“容器注册表类型”，确保在代码中配置并选择相同的注册表。 此任务将生成并推送解决方案中的所有模块，并发布至指定的容器注册表。 如果要将模块推送至不同的注册表，则会有多个“模块生成和推送”任务。

    ```
    **/module.json
    ```

    ![模块生成和推送](./media/how-to-ci-cd/module-build-push.png)

1. 在第二个 Azure IoT Edge 任务中，将“显示名称”更新为“部署至 IoT Edge 设备”并在“操作”下拉列表中选择“部署至 IoT Edge 设备”。 选择 Azure 订阅并输入你的 IoT 中心名称。 可以指定 IoT Edge 部署 ID 和部署优先级。 还可以选择部署至单个设备或多个设备。 如果要部署至多个设备，需要指定设备目标条件。 例如，若想将设备标记用作条件，则需要在部署前更新对应的设备标记。 

    ![部署到 Edge](./media/how-to-ci-cd/deploy-to-edge.png)

1. 单击“进程”并确保“代理队列”为“托管 Linux 预览版”。

    ![配置](./media/how-to-ci-cd/configure-env.png)

1. 打开“触发器”选项卡并开启“持续集成”触发器。 确保包含代码的分支已包括在内。

    ![触发器](./media/how-to-ci-cd/configure-trigger.png)

1. 保存新的生成定义并将新的生成排队。 单击“保存和排队”按钮。

1. 在显示的消息栏中选择指向该生成的链接。 或者转至生成定义，查看进入队列的最新生成作业。

    ![构建](./media/how-to-ci-cd/build-def.png)

1. 完成生成后，可以在实时日志文件中看到每个任务的摘要和结果。 
    
    ![完成](./media/how-to-ci-cd/complete.png)

1. 可以返回 VS Code 并检查 IoT 中心设备资源管理器。 具备该模块的 Edge 设备应开始运行（确保已将注册表凭据添加到 Edge 运行时）。

    ![Edge 运行](./media/how-to-ci-cd/edge-running.png)

## <a name="continuous-deployment-to-iot-edge-devices"></a>向 IoT Edge 设备进行持续部署

若要启用连续部署，基本上需要使用合适的 IoT Edge 设备设置 CI 作业，为项目中的分支启用“触发器”。 在经典 DevOps 实践中，一个项目包含两个主要分支。 主分支应该为稳定版本的代码，而开发分支包含最新的代码更改。 团队中的每个开发人员都应在开始更新代码时在开发分支中创建他/她自己的功能分支，这意味着所有提交都发生在开发分支下的功能分支上。 并且推送的每个提交都应通过 CI 系统进行测试。 对代码进行全面的本地测试后，应通过拉取请求将功能分支合并至开发分支。 通过 CI 系统对开发分支上的代码进行测试后，就能通过拉取请求将其合并至主分支。

因此，在向 IoT Edge 设备进行部署时存在三种主要环境。
- 在功能分支上，可以使用开发计算机上的模拟 IoT Edge 设备或部署至物理 IoT Edge 设备。
- 在开发分支上应部署至物理 IoT Edge 设备。
- 在主分支上，目标 IoT Edge 设备应该为生成设备。

## <a name="next-steps"></a>后续步骤

* 在[了解单设备 IoT Edge 部署或大规模 IoT Edge 部署](module-deployment-monitoring.md)中了解 IoT Edge 部署
* 阅读[大规模地部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)，详细了解创建、更新或删除部署的步骤。
