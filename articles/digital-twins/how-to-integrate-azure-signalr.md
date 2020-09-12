---
title: 与 Azure SignalR 服务集成
titleSuffix: Azure Digital Twins
description: 请参阅如何使用 Azure SignalR 将 Azure 数字孪生遥测流式传输到客户端
author: dejimarquis
ms.author: aymarqui
ms.date: 09/02/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0931a7e344d6546bd62ceb7513c4aa540f5b9638
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89447774"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>将 Azure 数字孪生与 Azure SignalR 服务集成

本文介绍如何将 Azure 数字孪生与 [Azure SignalR 服务](../azure-signalr/signalr-overview.md)集成。

本文中所述的解决方案使你能够将数字克隆遥测数据推送到连接的客户端，例如单个网页或移动应用程序。 因此，客户端将使用 IoT 设备的实时指标和状态进行更新，而无需轮询服务器或提交新的更新 HTTP 请求。

## <a name="prerequisites"></a>先决条件

下面是在继续操作之前应完成的先决条件：

* 在将你的解决方案与 Azure SignalR 服务集成之前，你应该完成 Azure 数字孪生 [_**教程：连接端到端解决方案**_](tutorial-end-to-end.md)，因为本操作方法基于此进行构建。 本教程介绍如何设置 Azure 数字孪生实例，该实例可与虚拟 IoT 设备一起触发数字非整数更新。 本操作说明将使用 Azure SignalR 服务将这些更新连接到示例 web 应用。
    - 你需要在本教程中创建的 **事件网格主题** 的名称。
* 计算机上已安装 [**Node.js**](https://nodejs.org/) 。

你还可以继续，然后用你的 Azure 帐户登录到 [Azure 门户](https://portal.azure.com/) 。

## <a name="solution-architecture"></a>解决方案体系结构

你将通过以下路径将 Azure SignalR 服务连接到 Azure 数字孪生。 关系图中的 A、B 和 C 部分取自 [端到端教程必备组件](tutorial-end-to-end.md)的体系结构关系图。在本操作指南中，你将通过添加第 D 部分来构建。

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="在端到端方案中显示 Azure 服务。描述从设备流向 IoT 中心的数据，通过 Azure 函数 (箭头 B) 到 Azure 数字孪生实例 (第一) ，然后通过事件网格向外传递到另一个用于处理 (箭头 C) 的 Azure 函数。第 D 节显示从箭头 C 中的同一事件网格流向 "广播" 的 Azure 函数的数据流。"广播" 与标记为 "协商" 的另一个 Azure 函数通信，"广播" 和 "协商" 与计算机设备通信。" lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>下载示例应用程序

首先，下载所需的示例应用。 你将需要以下两项内容：
* [**Azure 数字孪生示例**](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/)：此示例包含一个 *AdtSampleApp* ，其中包含两个用于在 azure 数字孪生实例之间移动数据的 azure 函数 (你可以在 [*教程：连接端到端解决方案*](tutorial-end-to-end.md)) 中更详细地了解此方案。 它还包含一个 *devicesimulator.exe* 示例应用程序，该应用程序模拟 IoT 设备，每秒生成新的温度值。 
    - 导航到示例链接并按 " *下载 ZIP* " 按钮，将示例副本下载到计算机，如 _**Azure_Digital_Twins_samples.zip**_。 解压缩文件夹。
* [**SignalR integration web 应用示例**](https://docs.microsoft.com/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/)：这是一个示例响应 web 应用，它将使用 azure SignalR 服务中的 Azure 数字孪生遥测数据。
    -  导航到示例链接并按 " *下载 ZIP* " 按钮，将示例副本下载到计算机，如 _**Azure_Digital_Twins_SignalR_integration_web_app_sample.zip**_。 解压缩文件夹。

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

将浏览器窗口保持打开 Azure 门户，因为下一部分将再次使用该窗口。

## <a name="configure-and-run-the-azure-functions-app"></a>配置并运行 Azure Functions 应用

在本部分中，将设置两个 Azure 函数：
* **negotiate** -HTTP 触发器函数。 它使用 *SignalRConnectionInfo* 输入绑定生成并返回有效的连接信息。
* **广播** - [事件网格](../event-grid/overview.md) 触发器函数。 它将通过事件网格接收 Azure 数字孪生遥测数据，并使用在上一步中创建的 *SignalR* 实例的输出绑定将消息广播到所有连接的客户端应用程序。

首先，转到打开 Azure 门户的浏览器，然后完成以下步骤，获取已设置的 SignalR 实例的 **连接字符串** 。 你将需要它来配置函数。
1. 确认先前部署的 SignalR 服务实例已成功创建。 可以通过在门户顶部的搜索框中搜索其名称来实现此目的。 选择该实例以将其打开。

1. 从 "实例" 菜单中选择 " **密钥** "，以查看 SignalR 服务实例的连接字符串。

1. 选择图标以复制主连接字符串。

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="显示 SignalR 实例的密钥页的 Azure 门户屏幕截图。主连接字符串旁边的 "复制到剪贴板" 图标将突出显示。" lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

接下来，启动 Visual Studio (或你选择的另一个代码编辑器) ，然后在 *Azure_Digital_Twins_samples > ADTSampleApp* 文件夹中打开代码解决方案。 然后执行以下步骤来创建函数：

1. 在*SampleFunctionsApp*项目中创建一个名为**SignalRFunctions.cs**的新 c # 清晰类。

1. 将类文件的内容替换为以下代码：

    ```C#
    using System;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Azure.EventGrid.Models;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;
    using Microsoft.Azure.WebJobs.Extensions.EventGrid;
    using Microsoft.Azure.WebJobs.Extensions.SignalRService;
    using Microsoft.Extensions.Logging;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    using System.Collections.Generic;
    
    namespace SampleFunctionsApp
    {
        public static class SignalRFunctions
        {
            public static double temperature;
    
            [FunctionName("negotiate")]
            public static SignalRConnectionInfo GetSignalRInfo(
                [HttpTrigger(AuthorizationLevel.Anonymous, "post")] HttpRequest req,
                [SignalRConnectionInfo(HubName = "dttelemetry")] SignalRConnectionInfo connectionInfo)
            {
                return connectionInfo;
            }
    
            [FunctionName("broadcast")]
            public static Task SendMessage(
                [EventGridTrigger] EventGridEvent eventGridEvent,
                [SignalR(HubName = "dttelemetry")] IAsyncCollector<SignalRMessage> signalRMessages,
                ILogger log)
            {
                JObject eventGridData = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
    
                log.LogInformation($"Event grid message: {eventGridData}");
    
                var patch = (JObject)eventGridData["data"]["patch"][0];
                if (patch["path"].ToString().Contains("/Temperature"))
                {
                    temperature = Math.Round(patch["value"].ToObject<double>(), 2);
                }
    
                var message = new Dictionary<object, object>
                {
                    { "temperatureInFahrenheit", temperature},
                };
        
                return signalRMessages.AddAsync(
                    new SignalRMessage
                    {
                        Target = "newMessage",
                        Arguments = new[] { message }
                    });
            }
        }
    }
    ```

1. 在 Visual Studio 的 " *包管理器控制台* " 窗口或计算机上 *Azure_Digital_Twins_samples \adtsampleapp\samplefunctionsapp* "文件夹中的任何命令窗口，运行以下命令将 `SignalRService` NuGet 包安装到项目中：
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    这应该解决类中的所有依赖关系问题。

接下来，使用*连接端到端解决方案*教程中的[*发布应用程序*部分](tutorial-end-to-end.md#publish-the-app)中所述的步骤将函数发布到 Azure。 你可以将其发布到端到端教程 prereq 中使用的同一应用服务/函数应用，或创建一个新的应用服务/函数应用，但你可能想要使用同一个应用来最大程度地减少重复。 此外，通过以下步骤完成应用发布：
1. 收集 *negotiate* 函数的 **HTTP 终结点 URL**。 为此，请在 Azure 门户的 " [函数应用](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) " 页上，从列表中选择函数应用。 在 "应用程序" 菜单中，选择 " *函数* "，然后选择 *negotiate* 函数。

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="函数应用的 Azure 门户视图，菜单中突出显示了 "函数"。页面上显示了函数列表，同时还会突出显示 "negotiate" 函数。":::

    点击 " *获取函数 URL* " 并 **通过 _/api_ 复制值 (不包含最后一个 _/negotiate？_) **。 稍后会用到它。

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text=""Negotiate" 函数的 Azure 门户视图。"获取函数 URL" 按钮将突出显示，并从开头到 "/api" 的 URL 部分":::

1. 最后，使用以下 Azure CLI 命令将 Azure SignalR **连接字符串** 从前面添加到该函数的应用设置中。 如果[计算机上安装](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)了 Azure CLI，则可在[Azure Cloud Shell](https://shell.azure.com)中或在本地运行该命令：
 
    ```azurecli-interactive
    az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    此命令的输出将打印为 Azure 函数设置的所有应用设置。 查找 `AzureSignalRConnectionString` 列表底部的以验证是否已添加。

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="命令窗口中的输出摘录，显示名为 "AzureSignalRConnectionString" 的列表项":::

#### <a name="connect-the-function-to-event-grid"></a>将函数连接到事件网格

接下来，请将 *广播* Azure 函数订阅到在教程过程中创建的 **事件网格主题** [*：连接端到端解决方案*](tutorial-end-to-end.md) prereq。 这将允许遥测数据从 *thermostat67* 克隆流到该函数，该函数可将其广播到所有客户端。

为此，你将从事件网格主题创建 **事件网格订阅** ，并将 *广播* Azure 函数作为终结点。

在 [Azure 门户](https://portal.azure.com/)中，导航到事件网格主题，方法是在顶部搜索栏中搜索其名称。 选择“+ 事件订阅”。

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Azure 门户：事件网格事件订阅":::

在“创建事件订阅”页，按如下所示填写字段（默认填充的字段未提及）：
* “事件订阅详细信息” > “名称”：为事件订阅指定名称。
* “终结点详细信息” > “终结点类型”：从菜单选项中选择“Azure 函数”。
* “终结点详细信息” > “终结点”：点击“选择终结点”链接。 这会打开“选择 Azure 函数”窗口：
    - 填写 **订阅**、 **资源组**、 **函数应用** 和 **函数** (*广播*) 。 在选择订阅后，其中一些可能会自动填充。
    - 点击“确认所选内容”。

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="创建事件订阅 Azure 门户视图。上面的字段已填充，并且突出显示了 "确认选择" 和 "创建" 按钮。":::

返回“创建事件订阅”页，点击“创建”。

## <a name="configure-and-run-the-web-app"></a>配置和运行 web 应用

在此部分中，你将看到结果为 "操作"。 首先，启动 **模拟设备示例应用** ，通过 Azure 数字孪生实例发送遥测数据。 然后，将 **示例客户端 web 应用** 配置为连接到已设置的 Azure SignalR 流。 此后，你应该能够看到数据实时更新示例 web 应用。

### <a name="run-the-device-simulator"></a>运行设备模拟器

在端到端教程必备条件中，已将 [设备模拟器配置](tutorial-end-to-end.md#configure-and-run-the-simulation) 为通过 IoT 中心和 Azure 数字孪生实例发送数据。

现在，您只需启动模拟器项目，该项目位于 *Azure_Digital_Twins_samples > devicesimulator.exe > devicesimulator.exe*中。 如果你使用的是 Visual Studio，则可以打开该项目，然后使用工具栏中的此按钮运行它：

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="Visual Studio 开始按钮（DeviceSimulator 项目）":::

控制台窗口将打开并显示模拟的温度遥测消息。 这些都是通过 Azure 数字孪生实例发送的，它们随后由 Azure 函数和 SignalR 选取。

你无需此控制台中执行任何其他操作，但在完成后续步骤时，需要让其保持运行状态。

### <a name="configure-the-sample-client-web-app"></a>配置示例客户端 web 应用

接下来，请按照以下步骤设置 **SignalR integration web 应用示例** ：
1. 使用 Visual Studio 或所选的任何代码编辑器，打开你在 "[*先决条件*](#prerequisites)" 部分下载的解压缩_**Azure_Digital_Twins_SignalR_integration_web_app_sample**_ 文件夹。

1. 打开 *src/App.js* 文件，并将中的 URL 替换 `HubConnectionBuilder` 为之前保存的 **negotiate** 函数的 HTTP 终结点 URL：

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<URL>')
            .build();
    ```
1. 在 Visual Studio 的 " *开发人员命令提示* " 或计算机上的任何命令窗口中，导航到 *Azure_Digital_Twins_SignalR_integration_web_app_sample \src* "文件夹。 运行以下命令以安装依赖节点包：

    ```cmd
    npm install
    ```

接下来，在 Azure 门户的函数应用中设置权限：
1. 在 Azure 门户的 " [函数应用](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) " 页中，选择 Function app 实例。
1. 在 "实例" 菜单中向下滚动，然后选择 " *CORS*"。 在 "CORS" 页上， `http://localhost:3000` 通过将其输入到空框中来添加作为允许的源。 选中 " *启用访问控制-允许凭据* " 框，然后单击 " *保存*"。

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="Azure Function 中的 CORS 设置":::

### <a name="see-the-results"></a>查看结果

若要查看操作结果，请启动 **SignalR integration web 应用示例**。 可以通过运行以下命令，从 *Azure_Digital_Twins_SignalR_integration_web_app_sample \src* 位置的任何控制台窗口执行此操作：

```cmd
npm start
```

这会打开一个浏览器窗口，其中运行示例应用，其中显示了视觉温度仪表。 应用运行后，应开始查看设备模拟器中通过 Azure 数字孪生传播的温度遥测值，这些值是由 web 应用实时反射的。

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="示例客户端 web 应用中的摘录，其中显示了视觉温度仪表。温度反映为67.52":::

## <a name="clean-up-resources"></a>清理资源

如果不再需要本文中创建的资源，请按照以下步骤将其删除。 

使用 Azure Cloud Shell 或本地 Azure CLI，可以使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 命令删除资源组中的所有 Azure 资源。 删除资源组也会删除 .。。
* 从端到端教程 (Azure 数字孪生实例) 
* IoT 中心和中心设备注册 (从端到端教程) 
* 事件网格主题和关联的订阅
* Azure Functions 应用，包括所有三个函数和相关资源（如存储）
* Azure SignalR 实例

> [!IMPORTANT]
> 删除资源组的操作不可逆。 资源组以及包含在其中的所有资源将被永久删除。 请确保不会意外删除错误的资源组或资源。 

```azurecli-interactive
az group delete --name <your-resource-group>
```

如果要删除 Azure 数字孪生实例，还可以使用以下命令，在端到端教程中删除为其创建的 Azure AD 应用注册：

```azurecli
az ad app delete --id <your-application-ID>
```

最后，删除已下载到本地计算机的项目示例文件夹 (*Azure_Digital_Twins_samples.zip* 并 *Azure_Digital_Twins_SignalR_integration_web_app_sample.zip*) 。

## <a name="next-steps"></a>后续步骤

本文介绍如何使用 SignalR 设置 Azure 函数，将 Azure 数字孪生遥测事件广播到示例客户端应用程序。

接下来，了解有关 Azure SignalR 服务的详细信息：
* [*什么是 Azure SignalR 服务？*](../azure-signalr/signalr-overview.md)

或者阅读有关 Azure Functions 的 Azure SignalR 服务身份验证的详细信息：
* [*Azure SignalR 服务身份验证*](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)
