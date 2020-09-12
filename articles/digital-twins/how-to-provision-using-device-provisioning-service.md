---
title: 使用 DPS 自动管理设备
titleSuffix: Azure Digital Twins
description: 请参阅如何使用 (DPS) 上的设备预配服务设置自动化过程，以便在 Azure 数字孪生中设置和停用 IoT 设备。
author: baanders
ms.author: baanders
ms.date: 9/1/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1a7ab90cccd78c3b005487938432a0f955d50738
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89380614"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>使用设备预配服务 (DPS) 自动管理 Azure 数字孪生中的设备

本文介绍如何将 Azure 数字孪生与 [设备预配服务集成 (DPS) ](../iot-dps/about-iot-dps.md)。

本文中所述的解决方案使你可以使用设备预配服务自动执行在 Azure 数字孪生中 **_预配_** 和 **_停_** 用 IoT 中心设备的过程。 

有关 _预配_ 和 _停_ 用阶段的详细信息，以及为了更好地了解所有企业 IoT 项目通用的常规设备管理阶段集，请参阅 IoT 中心设备管理文档的 " [*设备生命周期* " 一节](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) 。

## <a name="prerequisites"></a>先决条件

在设置预配之前，需要有一个包含模型和孪生的 **Azure 数字孪生实例** 。 还应设置此实例，使其能够基于数据更新数字输出信息。 

如果尚未设置此设置，则可以按照 Azure 数字孪生教程创建它 [*：连接端到端解决方案*](tutorial-end-to-end.md)。 本教程将指导你使用模型和孪生、连接的 Azure [IoT 中心](../iot-hub/about-iot-hub.md)和几个用于传播数据流的 [Azure 函数](../azure-functions/functions-overview.md) 设置 azure 数字孪生实例。

你将在本文后面的设置实例时需要以下值。 如果需要再次收集这些值，请使用下面的链接来了解相关说明。
* Azure 数字孪生实例主机名（[在门户中查找](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)）
* Azure 事件中心连接字符串 **_连接字符串_** ([在门户中查找](../event-hubs/event-hubs-get-connection-string.md#get-connection-string-from-the-portal)) 

此示例还使用设备 **模拟器** ，其中包括使用设备预配服务的预配。 设备模拟器位于此处： [Azure 数字孪生和 IoT 中心集成示例](https://docs.microsoft.com/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)。 导航到示例链接，并选择标题下面的 " *下载 ZIP* " 按钮，获取计算机上的示例项目。 解压缩已下载文件夹。

设备模拟器基于 **Node.js**版本 10.0. x 或更高版本。 [*准备开发环境*](https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md) 介绍如何在 Windows 或 Linux 上安装本教程 Node.js。

## <a name="solution-architecture"></a>解决方案体系结构

下图演示了此解决方案的体系结构，该解决方案使用 Azure 数字孪生与设备预配服务。 其中显示了设备预配和停用流。

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="在端到端方案中查看设备和多个 Azure 服务。数据在恒温器设备和 DPS 之间来回流动。数据还会从 DPS 流出到 IoT 中心，并通过名为 "分配" 的 Azure 功能通过 Azure 数字孪生。手动 "删除设备" 操作中的数据通过 IoT 中心流向 > 事件中心 > Azure Functions > Azure 数字孪生。":::

本文划分为两个部分：
* [*使用设备预配服务自动设置设备*](#auto-provision-device-using-device-provisioning-service)
* [*使用 IoT 中心生命周期事件自动停用设备*](#auto-retire-device-using-iot-hub-lifecycle-events)

有关体系结构中每个步骤的更深入说明，请参阅本文后面的各个部分。

## <a name="auto-provision-device-using-device-provisioning-service"></a>使用设备预配服务自动设置设备

在本部分中，会将设备预配服务附加到 Azure 数字孪生，通过以下路径自动设置设备。 这是 [之前](#solution-architecture)所示的完整体系结构的摘录。

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="预配流-解决方案体系结构关系图摘录，其中包含流的数字标签部分。数据在恒温器设备与 DPS (1 之间来回流动，> 设备 > DPS，5用于 DPS 设备) 。数据还会从 DPS 流出到 IoT 中心 (4) ，以及通过标记为 "分配" (2) 的 Azure 功能向 Azure 数字孪生 (3) 。":::

下面是流程的说明：
1. 设备联系 DPS 终结点，传递标识信息来证明其身份。
2. DPS 通过对照注册列表验证注册 ID 和密钥，并调用 [Azure 函数](../azure-functions/functions-overview.md) 进行分配，来验证设备标识。
3. Azure 函数在 Azure 数字孪生 [中为](concepts-twins-graph.md) 设备创建新的克隆。
4. DPS 向 IoT 中心注册设备，并填充设备所需的克隆状态。
5. IoT 中心将设备 ID 信息和 IoT 中心连接信息返回到设备。 设备现在可以连接到 IoT 中心。

以下各节逐步讲解设置此自动设置设备流的步骤。

### <a name="create-a-device-provisioning-service"></a>创建设备预配服务

使用设备预配服务预配新设备时，可以在 Azure 数字孪生中创建该设备的新的克隆。

创建将用于预配 IoT 设备的设备预配服务实例。 你可以使用以下 Azure CLI 说明，或使用 Azure 门户： [*快速入门：使用 Azure 门户设置 IoT 中心设备预配服务*](../iot-dps/quick-setup-auto-provision.md)。

以下 Azure CLI 命令将创建设备预配服务。 你将需要指定名称、资源组和区域。 如果[计算机上安装](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)了 Azure CLI，则可在[Cloud Shell](https://shell.azure.com)中或在本地运行该命令。

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region; for example, eastus>
```

### <a name="create-an-azure-function"></a>创建 Azure 函数

接下来，你将在 function app 中创建一个 HTTP 请求触发的函数。 可以使用端到端教程中创建的函数应用 ([*教程：连接端到端解决方案*](tutorial-end-to-end.md)) 或你自己的解决方案。

设备预配服务将在预配新设备的 [自定义分配策略](../iot-dps/how-to-use-custom-allocation-policies.md) 中使用此功能。 有关对 Azure 函数使用 HTTP 请求的详细信息，请参阅 [*Azure Functions 的 Azure http 请求触发器*](../azure-functions/functions-bindings-http-webhook-trigger.md)。

在 function app 项目中，添加一个新函数。 同时，将新的 NuGet 包添加到项目： `Microsoft.Azure.Devices.Provisioning.Service` 。

在新创建的函数代码文件中，粘贴以下代码。

```C#
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Devices.Shared;
using Microsoft.Azure.Devices.Provisioning.Service;
using System.Net.Http;
using Azure.Identity;
using Azure.DigitalTwins.Core;
using Azure.Core.Pipeline;
using Azure;
using System.Collections.Generic;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Samples.AdtIothub
{
    public static class DpsAdtAllocationFunc
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("DpsAdtAllocationFunc")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger log)
        {
            // Get request body
            string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogDebug($"Request.Body: {requestBody}");
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Get registration ID of the device
            string regId = data?.deviceRuntimeContext?.registrationId;

            bool fail = false;
            string message = "Uncaught error";
            ResponseObj obj = new ResponseObj();

            // Must have unique registration ID on DPS request 
            if (regId == null)
            {
                message = "Registration ID not provided for the device.";
                log.LogInformation("Registration ID: NULL");
                fail = true;
            }
            else
            {
                string[] hubs = data?.linkedHubs.ToObject<string[]>();

                // Must have hubs selected on the enrollment
                if (hubs == null)
                {
                    message = "No hub group defined for the enrollment.";
                    log.LogInformation("linkedHubs: NULL");
                    fail = true;
                }
                else
                {
                    // Find or create twin based on the provided registration ID and model ID
                    dynamic payloadContext = data?.deviceRuntimeContext?.payload;
                    string dtmi = payloadContext.modelId;
                    log.LogDebug($"payload.modelId: {dtmi}");
                    string dtId = await FindOrCreateTwin(dtmi, regId, log);

                    // Get first linked hub (TODO: select one of the linked hubs based on policy)
                    obj.iotHubHostName = hubs[0];

                    // Specify the initial tags for the device.
                    TwinCollection tags = new TwinCollection();
                    tags["dtmi"] = dtmi;
                    tags["dtId"] = dtId;

                    // Specify the initial desired properties for the device.
                    TwinCollection properties = new TwinCollection();

                    // Add the initial twin state to the response.
                    TwinState twinState = new TwinState(tags, properties);
                    obj.initialTwin = twinState;
                }
            }

            log.LogDebug("Response: " + ((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message));

            return (fail)
                ? new BadRequestObjectResult(message)
                : (ActionResult)new OkObjectResult(obj);
        }

        public static async Task<string> FindOrCreateTwin(string dtmi, string regId, ILogger log)
        {
            // Create Digital Twins client
            var cred = new ManagedIdentityCredential(adtAppId);
            var client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });

            // Find existing twin with registration ID
            string dtId;
            string query = $"SELECT * FROM DigitalTwins T WHERE $dtId = '{regId}' AND IS_OF_MODEL('{dtmi}')";
            AsyncPageable<string> twins = client.QueryAsync(query);
            
            await foreach (string twinJson in twins)
            {
                // Get DT ID from the Twin
                JObject twin = (JObject)JsonConvert.DeserializeObject(twinJson);
                dtId = (string)twin["$dtId"];
                log.LogInformation($"Twin '{dtId}' with Registration ID '{regId}' found in DT");
                return dtId;
            }

            // Not found, so create new twin
            log.LogInformation($"Twin ID not found, setting DT ID to regID");
            dtId = regId; // use the Registration ID as the DT ID

            // Define the model type for the twin to be created
            Dictionary<string, object> meta = new Dictionary<string, object>()
            {
                { "$model", dtmi }
            };
            // Initialize the twin properties
            Dictionary<string, object> twinProps = new Dictionary<string, object>()
            {
                { "$metadata", meta }
            };
            twinProps.Add("Temperature", 0.0);
            await client.CreateDigitalTwinAsync(dtId, System.Text.Json.JsonSerializer.Serialize<Dictionary<string, object>>(twinProps));
            log.LogInformation($"Twin '{dtId}' created in DT");

            return dtId;
        }
    }

    public class ResponseObj
    {
        public string iotHubHostName { get; set; }
        public TwinState initialTwin { get; set; }
    }
}
```

保存该文件，然后重新发布函数应用。 有关发布 function app 的说明，请参阅端到端教程的 [*发布应用*](tutorial-end-to-end.md#publish-the-app) 部分。

### <a name="configure-your-function"></a>配置函数

接下来，需要在函数应用中设置环境变量，并在其中包含对已创建的 Azure 数字孪生实例的引用。 如果你使用的是端到端教程 ([*教程：连接端到端解决方案*](tutorial-end-to-end.md)) ，则将配置此设置。

通过此 Azure CLI 命令添加设置：

```azurecli-interactive
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

确保为 function app 正确配置了权限和托管标识角色分配，如端对端教程中的向 [*函数应用分配权限*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) 部分中所述。

<!-- 
* Azure AD app registration **_Application (client) ID_** ([find in portal](../articles/digital-twins/how-to-set-up-instance-portal.md#collect-important-values))

```azurecli-interactive
az functionapp config appsettings set --settings "AdtAppId=<Application (client)" ID> -g <resource group> -n <your App Service (function app) name> 
``` -->

### <a name="create-device-provisioning-enrollment"></a>创建设备预配注册

接下来，需要使用 **自定义分配函数**在设备预配服务中创建注册。 按照有关自定义分配策略的设备预配服务一文的 [*创建注册*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) 和 [*派生唯一设备密钥*](../iot-dps/how-to-use-custom-allocation-policies.md#derive-unique-device-keys) 部分中的说明进行操作。

遍历该流时，你将通过在步骤中 **选择你想要将设备分配给集线器的方式**来将注册链接到刚刚创建的函数。 创建注册后，稍后将使用注册名称和主密钥或辅助 SAS 密钥来配置此项目的设备模拟器。

### <a name="set-up-the-device-simulator"></a>设置设备模拟器

此示例使用设备模拟器，其中包括使用设备预配服务的预配。 设备模拟器位于此处： [Azure 数字孪生和 IoT 中心集成示例](https://docs.microsoft.com/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)。 如果尚未下载该示例，请导航到示例链接，并选择标题下面的 " *下载 ZIP* " 按钮，立即获取该示例。 解压缩已下载文件夹。

打开命令窗口并导航到已下载的文件夹，然后导航到 *设备模拟器* 目录。 使用以下命令安装项目的依赖项：

```cmd
npm install
```

接下来，将该 *. 模板* 文件复制到名为 *env*的新文件，并填写以下设置：

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary or secondary SAS key>"
```

保存并关闭该文件。

### <a name="start-running-the-device-simulator"></a>开始运行设备模拟器

仍在 *设备模拟器* 目录的命令窗口中，使用以下命令启动设备模拟器：

```cmd
node .\adt_custom_register.js
```

应该会看到注册并连接到 IoT 中心的设备，然后开始发送消息。
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="显示设备注册和发送消息的命令窗口":::

### <a name="validate"></a>验证

由于在本文中设置的流的结果，设备将在 Azure 数字孪生中自动注册。 使用以下 [Azure 数字孪生 CLI](how-to-use-cli.md) 命令在创建的 Azure 数字孪生实例中查找设备的克隆。

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

应会看到在 Azure 数字孪生实例中找到的设备的克隆。
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="显示新创建的克隆的命令窗口":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>使用 IoT 中心生命周期事件自动停用设备

在本部分中，会将 IoT 中心生命周期事件附加到 Azure 数字孪生，通过以下路径自动停用设备。 这是 [之前](#solution-architecture)所示的完整体系结构的摘录。

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="停用设备流-解决方案体系结构图的摘录，其中包含流的数字标签部分。显示的恒温器设备没有连接到关系图中的 Azure 服务。手动 "删除设备" 操作中的数据通过 IoT 中心流过 (1) > 事件中心 (2) > Azure Functions > Azure 数字孪生 (3) 。":::

下面是流程的说明：
1. 外部或手动过程会触发在 IoT 中心内删除设备的操作。
2. IoT 中心删除设备并生成将路由到[事件中心](../event-hubs/event-hubs-about.md)的[设备生命周期](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle)事件。
3. Azure 函数删除 Azure 数字孪生中的设备的克隆。

以下各节逐步讲解设置此自动停用设备流的步骤。

### <a name="create-an-event-hub"></a>创建事件中心

你现在需要创建 Azure [事件中心](../event-hubs/event-hubs-about.md)，该中心将用于接收 IoT 中心生命周期事件。 

使用以下信息完成 [*创建事件中心*](../event-hubs/event-hubs-create.md) 快速入门中所述的步骤：
* 如果使用的是端到端教程 ([*教程：连接端到端解决方案*](tutorial-end-to-end.md)) ，可以重复使用为端到端教程创建的资源组。
* 将你的事件中心命名为 *lifecycleevents*，或选择其他内容，并记住你创建的命名空间。 在接下来的部分中设置生命周期函数和 IoT 中心路由时，将使用这些设置。

### <a name="create-an-azure-function"></a>创建 Azure 函数

接下来，你将在 function app 中创建一个事件中心触发的函数。 可以使用端到端教程中创建的函数应用 ([*教程：连接端到端解决方案*](tutorial-end-to-end.md)) 或你自己的解决方案。 

命名你的事件中心触发器 *lifecycleevents*，并将事件中心触发器连接到你在上一步中创建的事件中心。 如果使用了不同的事件中心名称，请将其更改为在下面的触发器名称中匹配。

此函数将使用 IoT 中心设备生命周期事件停用现有的设备。 有关生命周期事件的详细信息，请参阅 [*IoT 中心非遥测事件*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。 有关将事件中心与 Azure 函数结合使用的详细信息，请参阅 [*适用于 Azure Functions 的 Azure 事件中心触发器*](../azure-functions/functions-bindings-event-hubs-trigger.md)。

在已发布的函数应用中，添加 " *事件中心触发器*" 类型的新函数类，并粘贴以下代码。

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Azure;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Samples.AdtIothub
{
    public static class DeleteDeviceInTwinFunc
    {
        private static string adtAppId = "https://digitaltwins.azure.net";
        private static readonly string adtInstanceUrl = System.Environment.GetEnvironmentVariable("ADT_SERVICE_URL", EnvironmentVariableTarget.Process);
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("DeleteDeviceInTwinFunc")]
        public static async Task Run(
            [EventHubTrigger("lifecycleevents", Connection = "EVENTHUB_CONNECTIONSTRING")] EventData[] events, ILogger log)
        {
            var exceptions = new List<Exception>();

            foreach (EventData eventData in events)
            {
                try
                {
                    //log.LogDebug($"EventData: {System.Text.Json.JsonSerializer.Serialize(eventData)}");

                    string opType = eventData.Properties["opType"] as string;
                    if (opType == "deleteDeviceIdentity")
                    {
                        string deviceId = eventData.Properties["deviceId"] as string;
                        
                        // Create Digital Twin client
                        var cred = new ManagedIdentityCredential(adtAppId);
                        var client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });

                        // Find twin based on the original Registration ID
                        string regID = deviceId; // simple mapping
                        string dtId = await GetTwinId(client, regID, log);
                        if (dtId != null)
                        {
                            await DeleteRelationships(client, dtId, log);

                            // Delete twin
                            await client.DeleteDigitalTwinAsync(dtId);
                            log.LogInformation($"Twin '{dtId}' deleted in DT");
                        }
                    }
                }
                catch (Exception e)
                {
                    // We need to keep processing the rest of the batch - capture this exception and continue.
                    exceptions.Add(e);
                }
            }

            if (exceptions.Count > 1)
                throw new AggregateException(exceptions);

            if (exceptions.Count == 1)
                throw exceptions.Single();
        }


        public static async Task<string> GetTwinId(DigitalTwinsClient client, string regId, ILogger log)
        {
            string query = $"SELECT * FROM DigitalTwins T WHERE T.$dtId = '{regId}'";
            AsyncPageable<string> twins = client.QueryAsync(query);
            await foreach (string twinJson in twins)
            {
                JObject twin = (JObject)JsonConvert.DeserializeObject(twinJson);
                string dtId = (string)twin["$dtId"];
                log.LogInformation($"Twin '{dtId}' found in DT");
                return dtId;
            }

            return null;
        }

        public static async Task DeleteRelationships(DigitalTwinsClient client, string dtId, ILogger log)
        {
            var relationshipIds = new List<string>();

            AsyncPageable<string> relationships = client.GetRelationshipsAsync(dtId);
            await foreach (var relationshipJson in relationships)
            {
                BasicRelationship relationship = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relationshipJson);
                relationshipIds.Add(relationship.Id);
            }

            foreach (var relationshipId in relationshipIds)
            {
                client.DeleteRelationship(dtId, relationshipId);
                log.LogInformation($"Twin '{dtId}' relationship '{relationshipId}' deleted in DT");
            }
        }
    }
}
```

保存项目，然后再次发布函数应用。 有关发布 function app 的说明，请参阅端到端教程的 [*发布应用*](tutorial-end-to-end.md#publish-the-app) 部分。

### <a name="configure-your-function"></a>配置函数

接下来，需要在函数应用中设置环境变量，并在其中包含对已创建的 Azure 数字孪生实例的引用以及事件中心。 如果你使用的是端到端教程 ([*教程：连接端到端解决方案*](./tutorial-end-to-end.md)) ，将已配置第一个设置。

将此设置添加 Azure CLI 命令。 如果[计算机上安装](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)了 Azure CLI，则可在[Cloud Shell](https://shell.azure.com)中或在本地运行该命令。

```azurecli-interactive
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

接下来，你将需要配置函数环境变量以连接到新创建的事件中心。

```azurecli-interactive
az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
```

确保为 function app 正确配置了权限和托管标识角色分配，如端对端教程中的向 [*函数应用分配权限*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) 部分中所述。

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>为生命周期事件创建 IoT 中心路由

现在需要设置 IoT 中心路由，以路由设备生命周期事件。 在这种情况下，你将专门侦听由标识的设备删除事件 `if (opType == "deleteDeviceIdentity")` 。 这会触发数字克隆项的删除，并最终确定设备及其数字克隆的停用。

本文介绍了有关创建 IoT 中心路由的说明： [*使用 IoT 中心消息路由将设备到云的消息发送到不同的终结点*](../iot-hub/iot-hub-devguide-messages-d2c.md)。 *非遥测事件*部分说明，你可以使用**设备生命周期事件**作为路由的数据源。

此设置需要完成的步骤如下：
1. 创建自定义 IoT 中心事件中心终结点。 此终结点应以 [*创建事件中心*](#create-an-event-hub) 部分中创建的事件中心为目标。
2. 添加 *设备生命周期事件* 路由。 使用在上一步中创建的终结点。 您可以通过添加路由查询限制设备生命周期事件只发送删除事件 `opType='deleteDeviceIdentity'` 。
    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="添加路由":::

完成此流程后，所有内容都将设置为端到端停用设备。

### <a name="validate"></a>验证

若要触发停用过程，需要从 IoT 中心手动删除设备。

在本文的前半 [部分](#auto-provision-device-using-device-provisioning-service)，已在 IoT 中心创建了一个设备，并在相应的数字克隆中创建了一个。 

现在，请前往 IoT 中心，删除该设备 (你可以使用 [Azure CLI 命令](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-delete) 或在 [Azure 门户](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Devices%2FIotHubs)) 中执行此操作。 

设备将自动从 Azure 数字孪生中删除。 

使用以下 [Azure 数字孪生 CLI](how-to-use-cli.md) 命令验证是否已删除 Azure 数字孪生实例中的设备的克隆。

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

应该会看到，在 Azure 数字孪生实例中找不到设备的克隆。
:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="找不到显示的命令窗口":::

## <a name="clean-up-resources"></a>清理资源

如果不再需要本文中创建的资源，请按照以下步骤将其删除。

使用 Azure Cloud Shell 或本地 Azure CLI，可以使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 命令删除资源组中的所有 Azure 资源。 这将删除资源组;Azure 数字孪生实例;IoT 中心和中心设备注册;事件网格主题和关联的订阅;事件中心命名空间以及这两个 Azure Functions 应用，包括存储等关联资源。

> [!IMPORTANT]
> 删除资源组的操作不可逆。 资源组以及包含在其中的所有资源将被永久删除。 请确保不会意外删除错误的资源组或资源。 

```azurecli-interactive
az group delete --name <your-resource-group>
```
<!-- 
Next, delete the Azure AD app registration you created for your client app with this command:

```azurecli
az ad app delete --id <your-application-ID>
``` -->

然后，删除从本地计算机下载的项目示例文件夹。

## <a name="next-steps"></a>后续步骤

为设备创建的数字孪生在 Azure 数字孪生中存储为平面层次结构，但可通过模型信息和组织的多级别层次结构进行丰富。 若要了解有关此概念的详细信息，请阅读：

* [*概念：数字孪生和双子图形*](concepts-twins-graph.md)

你可以编写自定义逻辑，使用 Azure 数字孪生中存储的模型和图形数据自动提供此信息。 若要详细了解如何管理、升级和检索孪生图中的信息，请参阅以下内容：

* [*操作说明：管理数字克隆*](how-to-manage-twin.md)
* [*操作方法：查询双子图形*](how-to-query-graph.md)