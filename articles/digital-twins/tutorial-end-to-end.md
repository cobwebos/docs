---
title: 连接端到端解决方案
titleSuffix: Azure Digital Twins
description: 扩建由设备数据驱动的端到端 Azure 数字孪生解决方案的教程。
author: baanders
ms.author: baanders
ms.date: 4/15/2020
ms.topic: tutorial
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: f83eb6a0cab8bdf2f26888f895aeac8a8fa796bb
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2020
ms.locfileid: "85296978"
---
# <a name="build-out-an-end-to-end-solution"></a>扩建端到端解决方案

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

若要设置由环境中的实时数据驱动的完整端到端解决方案，可以将 Azure 数字孪生实例连接到其他 Azure 服务，以管理设备和数据。

在本教程中，你将...
* 设置 Azure 数字孪生实例
* 了解示例建筑方案，并实例化预先编写的组件
* 使用 [Azure Functions](../azure-functions/functions-overview.md) 应用将模拟的遥测从 [IoT 中心](../iot-hub/about-iot-hub.md)设备路由到数字孪生属性
* 通过使用 Azure Functions、终结点和路由处理数字孪生通知，通过孪生图传播更改

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="get-started-with-the-building-scenario"></a>开始使用建筑方案

本教程中使用的示例项目代表真实的建筑方案，包含楼层、房间和温控设备。 Azure 数字孪生实例将以数字形式表示这些组件，然后连接到 [IoT 中心](../iot-hub/about-iot-hub.md)、[事件网格](../event-grid/overview.md)和两个 [Azure 函数](../azure-functions/functions-overview.md)以加快数据移动。

下面是表示完整方案的关系图。 

首先，你将创建 Azure 数字孪生实例（图中的 A 部分），然后设置传入数字孪生的遥测数据流（箭头 B），再设置通过孪生图进行的数据传播（箭头 C）  。

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="完整的建筑方案图。描绘了从设备流向 IoT 中心的数据，该数据通过 Azure 函数（箭头 B）传到 Azure 数字孪生实例（A 部分），然后通过事件网格传到另一个 Azure 函数进行处理（箭头 C）":::

为逐步完成此方案，需要与先前下载的预先编写的示例应用程的组件进行交互。

下面是建筑方案 AdtSampleApp 示例应用实现的组件：
* 设备身份验证 
* [.NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) 用法示例（参见 CommandLoop.cs）
* 调用 Azure 数字孪生 API 的控制台接口
* SampleClientApp - Azure 数字孪生解决方案示例
* SampleFunctionsApp - Azure Functions 应用，可将 Azure 数字孪生图更新为来自 IoT 中心和 Azure 数字孪生事件的遥测结果

示例项目还包含交互式授权组件。 每次启动项目时，浏览器窗口都会随即打开，提示你使用 Azure 帐户登录。

### <a name="instantiate-the-pre-created-twin-graph"></a>实例化预先创建的孪生图

首先，你将使用示例项目中的 AdtSampleApp 解决方案构建端到端方案的 Azure 数字孪生部分（A 部分）：

:::image type="content" source="media/tutorial-end-to-end/building-scenario-a.png" alt-text="完整建筑方案图的摘录，其中突出显示了 A 部分（Azure 数字孪生实例）":::

在打开 AdtE2ESample 项目的 Visual Studio 窗口中，使用工具栏中的此按钮运行该项目__：

:::image type="content" source="media/tutorial-end-to-end/start-button-sample.png" alt-text="Visual Studio 开始按钮（SampleClientApp 项目）":::

控制台窗口随即打开，执行身份验证，然后等待命令。 在此控制台中，运行下一个命令来实例化示例 Azure 数字孪生解决方案。

> [!IMPORTANT]
> 如果 Azure 数字孪生实例中已经有数字孪生和关系，运行此命令会将其删除，并替换为示例方案的孪生和关系。

```cmd/sh
SetupBuildingScenario
```

此命令的输出是一系列确认消息，因为 Azure 数字孪生实例中创建和连接了三个[数字孪生](concepts-twins-graph.md)：名为 floor1 的楼层、名为 room21 的房间和名为 thermostat67 的温度传感器  。 这些数字孪生表示真实环境中可能存在的实体。

它们通过关系连接到以下[孪生图](concepts-twins-graph.md)。 孪生图表示整个环境，包括实体之间的交互和关联方式。

:::image type="content" source="media/tutorial-end-to-end/building-scenario-graph.png" alt-text="该图显示 floor1 包含 room21 且 room21 包含 thermostat67" border="false":::

通过运行以下命令可以验证孪生是否已创建，这样会查询已连接的 Azure 数字孪生实例中所包含的所有数字孪生：

```cmd/sh
Query
```

此后，就可以停止运行该项目。 不过，请在 Visual Studio 中使解决方案保持打开状态，因为本教程还将继续使用它。

## <a name="set-up-the-sample-function-app"></a>设置示例函数应用

下一步是设置将在本教程中用来处理数据的 [Azure Functions 应用](../azure-functions/functions-overview.md)。 函数应用 SampleFunctionsApp 包含两个函数：
* ProcessHubToDTEvents：处理传入的 IoT 中心数据，并相应地更新 Azure 数字孪生
* ProcessDTRoutedData：处理来自数字孪生的数据，并相应地更新 Azure 数字孪生中的父级孪生

在本部分中，你将发布预先编写的函数应用，并确保该函数应用可通过向其分配 Azure Active Directory (AAD) 标识来访问 Azure 数字孪生。 完成这些步骤后，本教程的其余部分即可使用函数应用中的函数。 

### <a name="publish-the-app"></a>发布应用

返回到打开 AdtE2ESample 项目的 Visual Studio 窗口中，从“解决方案资源管理器”窗格中，右键选择 SampleFunctionsApp 项目文件，然后点击“发布”____。

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Visual Studio：发布项目":::

在接下来的“发布”页中，保留选择的默认 Azure 目标，然后点击“下一步”。 

对于特定目标，请选择“Azure 函数应用(Windows)”，然后点击“下一步”。

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="在 Visual Studio 中发布 Azure 函数：特定目标":::

在“Functions 实例”页上，选择你的订阅。 这应该会使订阅中资源组填入方框。

选择实例的资源组，然后点击“+ 创建新的 Azure 函数…”。

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="在 Visual Studio 中发布 Azure 函数：Functions 实例（在函数应用之前）":::

在“函数应用(Windows) - 新建”窗口中，按如下所示填写字段：
* “名称”是 Azure 将用于托管 Azure Functions 应用的消耗计划的名称。 这也将成为保存实际函数的函数应用的名称。 你可以选择自己的唯一值，也可以保留默认建议。
* 确保“订阅”与要使用的订阅匹配 
* 确保“资源组”是你想要使用的资源组
* 将“计划类型”保留为“消耗”
* 在“位置”中，选择符合资源组位置的位置
* 使用“新建…”链接创建新的 Azure 存储资源。 设置与资源组相匹配的位置，使用其他默认值，然后点击“确定”。

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="在 Visual Studio 中发布 Azure 函数：函数应用(Windows) - 新建":::

然后选择“创建”。

这会使你返回到“Functions 实例”页，此时新函数应用会显示在资源组下。 点击“完成”。

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="在 Visual Studio 中发布 Azure 函数：Functions 实例（在函数应用之后）":::

在 Visual Studio 主窗口重新打开的“发布”窗格中，检查所有信息是否都正确无误，然后选择“发布”。

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="在 Visual Studio 中发布 Azure 函数：发布":::

> [!NOTE]
> 你可能会看到如下所示的弹出窗口：:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="在 Visual Studio 中发布 Azure 函数：发布凭据" border="false":::
> 如果出现弹出窗口，请依次选择“尝试从 Azure 检索凭据”和“保存” 。
>
> 若看到“你的 Functions 运行时版本与在 Azure中运行的版本不匹配”警告，请按照提示升级到最新的 Azure Functions 运行时版本。 若使用旧版本的 Visual Studio 而不是本教程开头的“先决条件”部分中推荐的版本，则可能出现此问题。

### <a name="assign-permissions-to-the-function-app"></a>向函数应用分配权限

为了使函数应用能够访问 Azure 数字孪生，下一步是配置应用设置，为应用分配系统管理的 AAD 标识，并为此标识授予 Azure 数字孪生实例的“所有者”权限。

在 Azure Cloud Shell 中，使用以下命令设置一个应用程序设置，供函数应用用来引用数字孪生实例。

```azurecli-interactive
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=<your-digital-twin-instance-URL>"
```

使用以下命令创建系统管理的标识。 记下输出中的 principalId 字段。

```azurecli-interactive
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

在以下命令中，使用 principalId 值将函数应用的标识分配给 Azure 数字孪生实例的“所有者”角色 ：

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```

此命令的结果是已创建的角色分配的输出信息。 函数应用现在有权访问 Azure 数字孪生实例。

## <a name="process-simulated-telemetry-from-an-iot-hub-device"></a>处理来自 IoT 中心设备的模拟遥测数据

Azure 数字孪生图将由真实设备的遥测驱动。 

在此步骤中，需要将在 [IoT 中心](../iot-hub/about-iot-hub.md)内注册的模拟温控设备连接到在 Azure 数字孪生中表示它的数字孪生。 当模拟设备发出遥测时，系统将通过可触发数字孪生中相应更新的 ProcessHubToDTEvents Azure 函数定向数据。 通过这种方式，数字孪生就可与真实设备的数据保持一致。 在 Azure 数字孪生中，将事件数据从一个位置定向到另一个位置的过程称为[路由事件](concepts-route-events.md)。

该过程发生在端到端方案的这一部分（箭头 B）：

:::image type="content" source="media/tutorial-end-to-end/building-scenario-b.png" alt-text="完整建筑方案图的摘录，其中突出显示了箭头 B（Azure 数字孪生前的元素：设备、IoT 中心和第一个 Azure 函数）":::

下面是设置此设备连接需要完成的操作：
1. 创建将管理模拟设备的 IoT 中心
2. 设置事件订阅，将 IoT 中心连接到相应的 Azure 函数
3. 在 IoT 中心注册模拟设备
4. 运行模拟设备并生成遥测
5. 查询 Azure 数字孪生以查看实时结果

### <a name="create-an-iot-hub-instance"></a>创建 IoT 中心实例

Azure 数字孪生旨在搭配 [IoT 中心](../iot-hub/about-iot-hub.md)使用，后者是用于管理设备及其数据的 Azure 服务。 在此步骤中，你将设置一个 IoT 中心，用于管理本教程中的示例设备。

在 Azure Cloud Shell 中，使用此命令创建新的 IoT 中心：

```azurecli-interactive
az iot hub create --name <name-for-your-IoT-hub> -g <your-resource-group> --sku S1
```

此命令的输出是有关已创建的 IoT 中心的信息。

保存提供给 IoT 中心的名称。 稍后将使用它。

### <a name="connect-the-iot-hub-to-the-azure-function"></a>将 IoT 中心连接到 Azure 函数

接下来，将 IoT 中心连接到之前在函数应用中发布的 ProcessHubToDTEvents Azure 函数，让数据可以通过函数从设备流入 IoT 中心，这一操作将更新 Azure 数字孪生。

为此，需要在 IoT 中心创建一个事件订阅，并将 Azure 函数作为终结点。 这会将函数“订阅”到 IoT 中心发生的事件。

在 [Azure 门户](https://portal.azure.com/)中，导航到新创建的 IoT 中心，方法是在顶部搜索栏中搜索其名称。 从中心菜单中选择“事件”，然后选择“+ 事件订阅” 。

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1.png" alt-text="Azure 门户：IoT 中心事件订阅":::

此时将显示“创建事件订阅”页。

:::image type="content" source="media/tutorial-end-to-end/event-subscription-2.png" alt-text="Azure 门户：创建事件订阅":::

按如下所示填写字段：
* “事件订阅详细信息” > “名称”：为事件订阅指定名称。
* “事件类型” > “筛选事件类型”：从菜单选项中选择“设备遥测”。
* “终结点详细信息” > “终结点类型”：从菜单选项中选择“Azure 函数”。
* “终结点详细信息” > “终结点”：点击“选择终结点”链接。 这会打开“选择 Azure 函数”窗口：:::image type="content" source="media/tutorial-end-to-end/event-subscription-3.png" alt-text="Azure 门户事件订阅：选择 Azure 函数" border="false":::
    - 填写“订阅”、“资源组”、“函数应用”和“函数”(ProcessHubToDTEvents)   。 在选择订阅后，其中一些可能会自动填充。
    - 点击“确认所选内容”。

返回“创建事件订阅”页，点击“创建”。

### <a name="register-the-simulated-device-with-iot-hub"></a>在 IoT 中心注册模拟设备 

本部分在 ID 为 thermostat67 的 IoT 中心内创建设备表示形式。 模拟设备将与其连接，这也是遥测事件从设备进入 IoT 中心的方式，在 IoT 中心中，上一步订阅的 Azure 函数正在侦听，准备获取事件并继续处理。

在 Azure Cloud Shell 中，使用以下命令在 IoT 中心创建设备：

```azurecli-interactive
az iot hub device-identity create --device-id thermostat67 --hub-name <your-IoT-hub-name> -g <your-resource-group>
```

输出是已创建设备的相关信息。

### <a name="configure-and-run-the-simulation"></a>配置和运行模拟

接下来，配置设备模拟器以将数据发送到 IoT 中心实例。

首先，使用以下命令获取 IoT 中心连接字符串：

```azurecli
az iot hub show-connection-string -n <your-IoT-hub-name>
```

然后，使用此命令获取设备连接字符串：

```azurecli
az iot hub device-identity show-connection-string --device-id thermostat67 --hub-name <your-IoT-hub-name>
```

将这些值插入本地项目中的设备模拟器代码中，以将模拟器连接到此 IoT 中心和 IoT 中心设备。

在新的 Visual Studio 窗口中，（从下载的解决方案文件夹）打开“设备模拟器”>“DeviceSimulator.sln”__。

>[!NOTE]
> 你现在应该有两个 Visual Studio 窗口，一个是 DeviceSimulator.sln，另一个是 AdtE2ESample.sln__ __。

在这个新 Visual Studio 窗口的“解决方案资源管理器”窗格中，选择“DeviceSimulator/AzureIoTHub.cs”，在编辑窗口中将其打开__。 将以下连接字符串值更改为前面收集的值：

```csharp
connectionString = <Iot-hub-connection-string>
deviceConnectionString = <device-connection-string>
```

保存文件。

现在，要查看已设置的数据模拟结果，请在工具栏中用此按钮运行 DeviceSimulator 项目：

:::image type="content" source="media/tutorial-end-to-end/start-button-simulator.png" alt-text="Visual Studio 开始按钮（DeviceSimulator 项目）":::

控制台窗口将打开并显示模拟的温度遥测消息。 这些消息将发送到 IoT 中心，由 Azure 函数选取并处理。

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="设备模拟器的控制台输出，其中显示了正在发送的温度遥测":::

你无需此控制台中执行任何其他操作，但在完成后续步骤时，需要让其保持运行状态。

### <a name="see-the-results-in-azure-digital-twins"></a>查看 Azure 数字孪生中的结果

之前发布的 ProcessHubToDTEvents 函数会侦听 IoT 中心数据，并调用 Azure 数字孪生 API 来更新 thermostat67 孪生上的“温度”属性  。

要查看 Azure 数字孪生端的数据，请转到打开 AdtE2ESample 项目的 Visual Studio 窗口，并运行该项目__。

在打开的项目控制台窗口中运行以下命令，以获取数字孪生 thermostat67 所报告的温度：

```cmd
ObserveProperties thermostat67 Temperature
```

你应该会看到来自 Azure 数字孪生实例的实时更新温度正在每隔 10 秒就记录到控制台中。

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry.png" alt-text="控制台输出，其中显示来自数字孪生 thermostat67 的温度消息日志":::

验证此操作成功后，就可以停止运行这两个项目。 让 Visual Studio 窗口保持打开状态，因为在本教程的其余部分将继续使用它们。

## <a name="propagate-azure-digital-twins-events-through-the-graph"></a>通过图形传播 Azure 数字孪生事件

到本教程的此阶段，你已了解如何通过外部设备数据更新 Azure 数字孪生。 接下来，你将了解如何通过 Azure 数字孪生图传播对某个数字孪生的更改，换句话说，就是如何从服务内部数据更新孪生。

为此，在更新已连接的 Thermostat 孪生时，你将使用 ProcessDTRoutedData Azure 函数来更新 Room 孪生  。 该过程发生在端到端方案的这一部分（箭头 C）：

:::image type="content" source="media/tutorial-end-to-end/building-scenario-c.png" alt-text="完整建筑方案图的摘录，其中突出显示了箭头 C（Azure 数字孪生后的元素：事件网格和第二个 Azure 函数）":::

下面是设置此数据流需要完成的操作：
1. 创建一个将实例连接到事件网格的 Azure 数字孪生终结点
2. 在 Azure 数字孪生中设置路由，将孪生属性更改事件发送到终结点
3. 部署在终结点上（通过[事件网格](../event-grid/overview.md)）侦听的 Azure Functions 应用，并相应地更新其他孪生
4. 运行模拟设备并查询 Azure 数字孪生，以查看实时结果

### <a name="set-up-endpoint"></a>设置终结点

[事件网格](../event-grid/overview.md)是一项 Azure 服务，可帮助你将来自 Azure 服务的事件路由和传递到 Azure 中的其他位置。 你可以创建一个[事件网格主题](../event-grid/concepts.md)从源收集特定事件，然后订阅者可以侦听该主题，以便在事件送达时接收事件。

在本部分中，你将创建一个事件网格主题，然后在 Azure 数字孪生内创建一个指向（将事件传送至）该主题的终结点。 

在 Azure Cloud Shell 中运行以下命令，以创建事件网格主题：

```azurecli-interactive
az eventgrid topic create -g <your-resource-group> --name <name-for-your-event-grid-topic> -l <region>
```

> [!TIP]
> 要输出可传递到 Azure CLI 命令中的 Azure 区域名称的列表，请运行以下命令：
> ```azurecli
> az account list-locations -o table
> ```

此命令的输出是已创建的事件网格主题的相关信息。

接下来，创建一个指向事件网格主题的 Azure 数字孪生终结点。 使用下面的命令，根据需要填写占位符字段：

```azurecli
az dt endpoint create eventgrid --dt-name <your-Azure-Digital-Twins-instance> --eventgrid-resource-group <your-resource-group> --eventgrid-topic <your-event-grid-topic> --endpoint-name <name-for-your-Azure-Digital-Twins-endpoint>
```

此命令的输出是已创建的终结点的相关信息。

你也可以执行以下命令在 Azure 数字孪生实例中查询此终结点，从而验证是否成功创建终结点：

```azurecli
az dt endpoint show --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> 
```

在输出中查找 `provisioningState` 字段，检查其值是否为“Succeeded”。

:::image type="content" source="media/tutorial-end-to-end/output-endpoints.png" alt-text="终结点的查询结果，其中显示 provisioningState 为 Succeeded":::

保存提供给事件网格主题和 Azure 数字孪生终结点的名称。 稍后你将用到它们。

### <a name="set-up-route"></a>设置路由

接下来，创建 Azure 数字孪生路由，将事件发送到刚刚创建的 Azure 数字孪生终结点。

```azurecli
az dt route create --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> --route-name <name-for-your-Azure-Digital-Twins-route>
```

此命令的输出是一些有关已创建的路由的信息。

#### <a name="connect-the-function-to-event-grid"></a>将函数连接到事件网格

接下来，请将 ProcessDTRoutedData Azure 函数订阅到前面创建的事件网格主题，让遥测数据可以通过事件网格主题从 thermostat67 孪生流向该函数，该函数会返回到 Azure 数字孪生并相应地更新 room21  。

为此，你需要从事件网格主题中创建对 ProcessDTRoutedData Azure 函数的订阅作为终结点。

在 [Azure 门户](https://portal.azure.com/)中，导航到事件网格主题，方法是在顶部搜索栏中搜索其名称。 选择“+ 事件订阅”。

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1b.png" alt-text="Azure 门户：事件网格事件订阅":::

创建此事件订阅的步骤与之前在本教程中将第一个 Azure 函数订阅到 IoT 中心的步骤类似。 不同之处在于，这次不需要将设备遥测指定为要侦听的事件类型，而且会连接到其他 Azure 函数。

下面是订阅步骤：

在“创建事件订阅”页中，按如下所示填写字段：
* “事件订阅详细信息” > “名称”：为事件订阅指定名称。
* “终结点详细信息” > “终结点类型”：从菜单选项中选择“Azure 函数”。
* “终结点详细信息” > “终结点”：点击“选择终结点”链接。 这会打开“选择 Azure 函数”窗口：
    - 填写“订阅”、“资源组”、“函数应用”和“函数”(ProcessDTRoutedData)   。 在选择订阅后，其中一些可能会自动填充。
    - 点击“确认所选内容”。

返回“创建事件订阅”页，点击“创建”。

### <a name="run-the-simulation-and-see-the-results"></a>运行模拟并查看结果

现在你可以运行设备模拟器，启动已设置的新事件流。 转到打开 DeviceSimulator 项目的 Visual Studio 窗口，然后运行该项目__。

与之前运行设备模拟器一样，控制台窗口将打开并显示模拟温度遥测消息。 这些事件将经过之前设置的流来更新 thermostat67 孪生，然后经过最近设置的流来更新 room21 孪生以进行匹配 。

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="设备模拟器的控制台输出，其中显示了正在发送的温度遥测":::

你无需此控制台中执行任何其他操作，但在完成后续步骤时，需要让其保持运行状态。

若要查看 Azure 数字孪生端的数据，请转到打开 AdtE2ESample 项目的 Visual Studio 窗口，并运行该项目。

在打开的项目控制台窗口中运行以下命令，以获取 thermostat67 和 room21 这两个数字孪生所报告的温度 。

```cmd
ObserveProperties thermostat67 Temperature room21 Temperature
```

你应该会看到来自 Azure 数字孪生实例的实时更新温度正在每隔 10 秒就记录到控制台中。 注意，room21 的温度正在更新，以匹配 thermostat67 的更新 。

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry-b.png" alt-text="控制台输出，其中显示来自温控设备和房间的温度消息日志":::

验证此操作成功后，就可以停止运行这两个项目。 你也可以关闭 Visual Studio 窗口，因为本教程现已完成。

## <a name="review"></a>审阅

下面回顾了在本教程中扩建的方案。

1. Azure 数字孪生实例以数字方式表示楼层、房间和温控设备（如下图中 A 部分所示）
2. 模拟设备遥测发送到 IoT 中心，其中 ProcessHubToDTEvents Azure 函数正在侦听遥测事件。 ProcessHubToDTEvents Azure 函数使用这些事件中的信息来设置 thermostat67（图中箭头 B）的“温度”属性  。
3. Azure 数字孪生的属性更改事件会路由到事件网格主题，其中 ProcessDTRoutedData Azure 函数正在侦听事件。 ProcessDTRoutedData Azure 函数使用这些事件中的信息来设置 room21（图中箭头 C）的“温度”属性  。

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="完整的建筑方案图。描绘了从设备流向 IoT 中心的数据，该数据通过 Azure 函数（箭头 B）传到 Azure 数字孪生实例（A 部分），然后通过事件网格传到另一个 Azure 函数进行处理（箭头 C）":::

## <a name="clean-up-resources"></a>清理资源

如果不再需要本教程中创建的资源，请按照以下步骤将其删除。 

利用 Azure Cloud Shell，你可以使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 命令删除资源组中的所有 Azure 资源。 这会删除资源组；Azure 数字孪生实例；IoT 中心和中心设备注册；事件网格主题和关联的订阅；以及两个 Azure Functions 应用，包括存储等关联资源。

> [!IMPORTANT]
> 删除资源组的操作不可逆。 资源组以及包含在其中的所有资源将被永久删除。 请确保不会意外删除错误的资源组或资源。 

```azurecli-interactive
az group delete --name <your-resource-group>
```

接下来，使用以下命令删除为客户端应用创建的 AAD 应用注册：

```azurecli
az ad app delete --id <your-application-ID>
```

最后，删除从本地计算机下载的项目示例文件夹。

## <a name="next-steps"></a>后续步骤

在本教程中，你创建了一个端到端方案，它显示了由实时设备数据驱动的 Azure 数字孪生。

接下来，请开始查看概念文档，详细了解本教程中所用的元素：
* [概念：自定义模型](concepts-models.md)

或者，你也可以先查看操作方法文章，更深入的了解本教程中的过程：
* [操作说明：使用 Azure 数字孪生 CLI](how-to-use-cli.md)
