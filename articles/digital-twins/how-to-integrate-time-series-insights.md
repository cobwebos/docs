---
title: 与时序见解集成
titleSuffix: Azure Digital Twins
description: 请参阅如何设置从 Azure 数字孪生到 Azure 时序见解的事件路由。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 7/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9be44d29ab08a7404bfb14d691bb5bb339284714
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131593"
---
# <a name="integrate-digital-twins-with-azure-time-series-insights"></a>将数字孪生与 Azure 时序见解集成

在此参考中，你将了解如何将 Azure 数字孪生与[Azure 时序见解（TSI）](../time-series-insights/overview-what-is-tsi.md)集成。 此解决方案可让你收集和分析有关 IoT 解决方案的历史数据。 Azure 数字孪生非常适合用于将数据送入时序见解，因为它允许你关联多个数据流，并在将信息发送到时序见解之前将其标准化。 

## <a name="solution-architecture"></a>解决方案体系结构

你将通过以下路径向 Azure 数字孪生附加时序见解。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="在端到端方案中显示 Azure 服务的视图，突出显示时序见解" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="prerequisites"></a>先决条件

* 需要一个 Azure 数字孪生实例，该实例只需多次更新一次克隆信息，即可查看时序见解中跟踪的数据。 
    * 如果你没有，请按照 Azure 数字孪生[*教程：连接端到端解决方案*](./tutorial-end-to-end.md)来设置 Azure 数字孪生实例，并使用虚拟 IoT 设备生成克隆更改。

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>创建路由并筛选到克隆更新通知

每当更新了克隆的状态时，Azure 数字孪生实例就可以发出[双子次更新事件](how-to-interpret-event-data.md)。 你将创建一个路由，该路由会将这些更新事件定向到事件中心，以便进一步处理。

Azure 数字孪生[*教程：连接端到端解决方案*](./tutorial-end-to-end.md)演练了一个方案，其中的温度计用于更新附加到房间克隆的温度属性。 此模式使用克隆的更新，而不是从 IoT 设备转发遥测，这使你可以灵活地更改基础数据源，而无需更新时序见解逻辑。

1. 创建一个事件中心命名空间，该命名空间将从你的 Azure 数字孪生实例接收事件。 你可以使用以下 Azure CLI 说明，或使用 Azure 门户：[*快速入门：使用 Azure 门户创建事件中心*](../event-hubs/event-hubs-create.md)。

    ```azurecli-interactive
    # Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
    az eventhubs namespace create --name <Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
    ```

2. 创建事件中心。

    ```azurecli-interactive
    # Create an event hub to receive twin change events. Specify a name for the event hub. 
    az eventhubs eventhub create --name <event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
    ```

3. 使用发送和接收权限创建[授权规则](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create)。

    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <twins event hub name> --name <twins auth rule>
    ```

4. 创建终结点，将事件网格主题链接到 Azure 数字孪生。

    ```azurecli
    az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace> --eventhub <twins event hub name> --eventhub-policy <twins auth rule> -n <your-Azure-Digital-Twins-instance-name>
    ```

5. 在 Azure 数字孪生中创建路由，以将克隆更新事件发送到终结点。 此路由中的筛选器将仅允许向您的终结点传递一条不成对的更新消息。

    ```azurecli
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Hub-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-an-azure-function"></a>创建 Azure 函数 

接下来，你将在新的函数应用中创建一个事件中心触发的函数，从端到端教程（[*教程：连接端到端解决方案*](./tutorial-end-to-end.md)）中的函数应用。 此函数将这些更新从其原始格式转换为 json 修补程序文档，仅包含孪生中更新和添加的值。

有关将事件中心与 Azure 函数结合使用的详细信息，请参阅[*适用于 Azure Functions 的 Azure 事件中心触发器*](../azure-functions/functions-bindings-event-hubs-trigger.md)。

在已发布的函数应用中，将函数代码替换为以下代码。

```C#
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System.Threading.Tasks;
using System.Text;
using System.Collections.Generic;

namespace SampleFunctionsApp
{
    public static class ProcessDTUpdatetoTSI
    { 
        [FunctionName("ProcessDTUpdatetoTSI")]
        public static async Task Run(
            [EventHubTrigger("twins-fx-hub", Connection = "EventHubAppSetting-Twins")]EventData myEventHubMessage, 
            [EventHub("alkarche-tsi-demo-hub", Connection = "EventHubAppSetting-TSI")]IAsyncCollector<string> outputEvents, 
            ILogger log)
        {
            JObject message = (JObject)JsonConvert.DeserializeObject(Encoding.UTF8.GetString(myEventHubMessage.Body));
            log.LogInformation("Reading event:" + message.ToString());

            // Read values that are replaced or added
            Dictionary<string, object> tsiUpdate = new Dictionary<string, object>();
            foreach (var operation in message["patch"]) {
                if (operation["op"].ToString() == "replace" || operation["op"].ToString() == "add")
                    //Convert from JSON patch path to a flattened property for TSI
                    //Example input: /Front/Temperature
                    //        output: Front.Temperature
                    string path = operation["path"].ToString().Substring(1);                    
                    path = path.Replace("/", ".");                    
                    tsiUpdate.Add(path, operation["value"]);
            }
            //Send an update if updates exist
            if (tsiUpdate.Count>0){
                tsiUpdate.Add("$dtId", myEventHubMessage.Properties["cloudEvents:subject"]);
                await outputEvents.AddAsync(JsonConvert.SerializeObject(tsiUpdate));
            }
        }
    }
}
```

然后，该函数会将它创建的 JSON 对象发送到第二个事件中心，你将连接到时序见解。

## <a name="send-telemetry-to-an-event-hub"></a>将遥测发送到事件中心

现在，你将创建另一个事件中心，并将你的函数配置为将其输出流式传输到该事件中心。 此事件中心随后会连接到时序见解。

### <a name="create-an-event-hub"></a>创建事件中心

你可以使用以下 Azure CLI 说明，或使用 Azure 门户：[*快速入门：使用 Azure 门户创建事件中心*](../event-hubs/event-hubs-create.md)。

1. 准备您之前的事件中心命名空间和资源组名称 

2. 创建事件中心
    ```azurecli-interactive
    # Create an event hub. Specify a name for the event hub. 
    az eventhubs eventhub create --name <TSI event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
    ```
3. 使用发送和接收权限创建[授权规则](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create)
    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <event hub name> --name <TSI auth rule>
    ```

### <a name="configure-your-function"></a>配置函数

您需要在函数应用程序中设置一个环境变量，该变量包含事件中心连接字符串。

#### <a name="set-the-time-series-insights-event-hub-connection-string"></a>设置时序见解事件中心连接字符串

1. 获取上面为时序见解中心创建的授权规则的[事件中心连接字符串](../event-hubs/event-hubs-get-connection-string.md)：

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <twins event hub name> --name <twins auth rule>
    ```

2. 在函数应用中，创建一个包含连接字符串的应用设置：

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-event-hub-connection-string> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
    ```

#### <a name="set-the-twins-event-hub-connection-string"></a>设置孪生事件中心连接字符串

1. 获取上面为孪生中心创建的授权规则的[事件中心连接字符串](../event-hubs/event-hubs-get-connection-string.md)。

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. 在函数应用中，创建一个包含连接字符串的应用设置：

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-event-hub-connection-string> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>创建和连接时序见解实例

接下来，您将设置一个时序见解实例，以便从第二个事件中心接收数据。 有关此过程的更多详细信息，请参阅[*教程：设置 Azure 时序见解 Gen2 PAYG 环境*](../time-series-insights/tutorials-set-up-tsi-environment.md)

1. 在 Azure 门户中，开始创建时序见解资源。 
    1. 选择 " **PAYG （预览版）** " 定价层。
    2. 需要为此环境选择时序 ID。 时序 ID 最多可以有三个值，你将使用这些值在时序见解中搜索数据。 对于本教程，可以使用 **$dtId**。 有关选择[*时序 id 的最佳实践，*](https://docs.microsoft.com/azure/time-series-insights/how-to-select-tsid)请阅读有关选择 ID 值的详细信息。
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-twin-id.png" alt-text="时序见解环境的创建门户 UX。选择 PAYG （预览版）定价层，并 $dtId 时序 ID 属性名称":::

2. 选择 "**下一步：事件源**"，然后从上面选择事件中心信息。 还需要创建新的事件中心使用者组。
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="时序见解环境事件源的创建门户 UX。正在使用上面的事件中心信息创建事件源。你还将创建一个新的使用者组。":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>开始将 IoT 数据发送到 Azure 数字孪生

若要开始将数据发送到时序见解，需要开始更新数据值的数字克隆属性。 使用[az dt 双子端更新](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest#ext-azure-iot-az-dt-twin-update)命令。

如果你使用的是端到端教程来帮助进行环境设置，则可以通过运行 `DeviceSimulator` Azure 数字孪生教程中的项目开始发送模拟 IoT 数据[*教程：连接端到端解决方案*](tutorial-end-to-end.md)。 本教程的[*配置和运行模拟*](tutorial-end-to-end.md#configure-and-run-the-simulation)部分介绍了相关说明。

## <a name="visualize-your-data-in-time-series-insights"></a>直观显示时序见解中的数据

现在，数据应流向时序见解实例，并准备好进行分析。 请按照以下步骤来浏览传入的数据。

1. 在 Azure 门户中打开时序见解实例。 请访问概述中所示的时序见解资源管理器 URL。
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="在时序见解环境的 "概述" 选项卡中，单击 "时序见解资源管理器 URL"":::

2. 在资源管理器中，你将看到显示在左侧的三个孪生。 单击 " **thermostat67**"，选择 " **patch_value**"，然后单击 "**添加**"。

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="单击 "thermostat67"，选择 "温度 * *"，然后单击 "添加"。":::

3. 你现在应看到恒温器的初始温度读数，如下所示。 将为 room21 和 floor1 更新相同的温度读数，并以串联方式可视化这些数据流。
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="在 TSI 资源管理器中将初始温度数据绘入图表。它是介于68和85之间的随机值的行":::

4. 如果允许模拟运行的时间更长，可视化效果将如下所示：
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="每个克隆的温度数据都以不同颜色的三个平行线为绘图。":::

## <a name="next-steps"></a>后续步骤

默认情况下，数字孪生存储为时序见解中的平面层次结构，但可通过模型信息和组织的多级别层次结构进行丰富。 若要了解有关此过程的详细信息，请阅读： 

* [*教程：定义和应用模型*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

你可以编写自定义逻辑，使用 Azure 数字孪生中存储的模型和图形数据自动提供此信息。 若要详细了解如何管理、升级和检索孪生图中的信息，请参阅以下参考资料：

* [*操作说明：管理数字克隆*](./how-to-manage-twin.md)
* [*操作方法：查询双子图形*](./how-to-query-graph.md)