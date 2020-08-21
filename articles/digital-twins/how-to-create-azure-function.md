---
title: 设置用于处理数据的 Azure 函数
titleSuffix: Azure Digital Twins
description: 请参阅如何创建可通过数字孪生访问和触发的 Azure 函数。
author: cschormann
ms.author: cschorm
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: ab013a310997f43d1019d849e87c0cf1b0d151ee
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661089"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>连接 Azure Functions 应用以处理数据

在预览期间，将使用 [**事件路由**](concepts-route-events.md) 通过计算资源（如 [Azure Functions](../azure-functions/functions-overview.md)）来处理更新基于数据的数字孪生。 Azure function 可用于更新数字克隆，以响应：
* 来自 IoT 中心的设备遥测数据
* 属性更改或其他数据来自于克隆图形中的其他数字输出

本文逐步讲解如何创建 Azure 功能，以便与 Azure 数字孪生配合使用。 

下面概述了它包含的步骤：

1. 在 Visual Studio 中创建 Azure Functions 应用
2. 使用 [事件网格](../event-grid/overview.md) 触发器编写 Azure 函数
3. 将身份验证代码添加到函数 (以便能够访问 Azure 数字孪生) 
4. 将函数应用发布到 Azure
5. 设置 [安全](concepts-security.md) 访问权限。 为了使 Azure 函数在运行时接收访问令牌来访问服务，你需要为函数应用配置托管服务标识。

本文的其余部分逐步介绍 Azure function 设置步骤，一次一个。

## <a name="create-an-azure-functions-app-in-visual-studio"></a>在 Visual Studio 中创建 Azure Functions 应用

在 Visual Studio 2019 中，选择 " *文件" > "新建项目*"。 搜索 *Azure Functions* 模板，将其选中，然后按 "下一步"。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-new-project.png" alt-text="Visual Studio： 新建项目 对话框":::

指定函数应用的名称，并按 "创建"。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-config.png" alt-text="Visual Studio： 配置项目 对话框":::

选择 " *事件网格" 触发器* 并按 "创建"。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-trigger.png" alt-text="Visual Studio： Azure 函数项目触发器对话框":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>使用事件网格触发器编写 Azure 函数

以下代码将创建一个可用于记录事件的 short Azure 函数： 

```csharp
// Default URL for triggering Event Grid function in the local environment
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;

namespace FunctionSample
{
    public static class FooFunction    {
        [FunctionName("Foo")]
        public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

这是你的基本 Azure 功能。

### <a name="run-and-debug-the-azure-function-app"></a>运行和调试 Azure function app

你现在可以编译和运行函数。 尽管 Azure 函数最终打算在云中运行，但你也可以在本地运行和调试 Azure 函数。

有关此内容的详细信息，请参阅在 [*本地调试事件网格触发器*](../azure-functions/functions-debug-event-grid-trigger-local.md)。

### <a name="add-the-azure-digital-twins-sdk-to-your-azure-function-app"></a>将 Azure 数字孪生 SDK 添加到 Azure function app

Function app 使用 [适用于 (.net 的 Azure IoT 数字克隆客户端库 ) ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)与 Azure 数字孪生交互。 若要使用 SDK，需要在项目中包含以下包：
* `Azure.DigitalTwins.Core` (版本 `1.0.0-preview.2`) 
* `Azure.Identity` (版本 `1.1.1`) 

若要配置 Azure SDK 管道以便正确设置 Azure Functions，还需要：
* `Azure.Net.Http`
* `Azure.Core`

根据您选择的工具，您可以通过 Visual Studio 包管理器或 `dotnet` 命令行工具执行此操作。 

向 Azure 函数添加以下 using 语句。

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;
```

## <a name="add-authentication-code-to-the-azure-function"></a>将身份验证代码添加到 Azure 函数

接下来，添加允许函数访问 Azure 数字孪生的身份验证代码。

将变量添加到函数类以实现这些值： 
* Azure 数字孪生应用 ID
* Azure 数字孪生实例的 URL。 最好从环境变量中读取服务 URL，而不是在函数中对其进行硬编码。
* 用于保存 HttpClient 实例的静态变量。 创建 HttpClient 的成本相对较高，因此，我们希望避免对每个函数调用执行此操作。

此外，在函数中添加一个本地变量，以将 Azure 数字孪生客户端实例保存到函数项目。 不要 *将此变量设置为* 类中的静态变量。

最后，将函数签名更改为异步。

完成这些更改后，函数代码应类似于以下内容：

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

为了使函数应用能够访问 Azure 数字孪生，它需要具有系统管理的标识并且有权访问 Azure 数字孪生实例。

使用以下命令创建系统管理的标识。 记下输出中的 principalId 字段。

```azurecli
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

使用以下命令中的 *principalId* 值将 function app 的标识分配给 Azure 数字 *孪生所有者 (预览 * azure 数字孪生实例的) 角色。 这将为实例中的 function app 权限指定执行数据平面活动。

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```

有关托管标识的详细信息，请参阅 [*如何将托管标识用于应用服务和 Azure Functions*](../app-service/overview-managed-identity.md)。

最后，可以通过设置环境变量，使 Azure 数字孪生实例的 URL 可供函数访问。 有关此内容的详细信息，请参阅 [*环境变量*](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables)。

> [!TIP]
> Azure 数字孪生实例的 URL 是通过将 *https://* 添加到 Azure 数字孪生实例的 *主机名*的开头来完成的。 若要查看主机名以及实例的所有属性，可以运行 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` 。

```azurecli
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-URL>"
```

## <a name="publish-the-azure-function-app"></a>发布 Azure function app

若要将函数应用发布到 Azure，请在解决方案资源管理器中右键选择函数项目 (不是解决方案) ，然后选择 " *发布 ( # B3 *"。

将显示以下选项卡：

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-1.png" alt-text="Visual Studio：发布函数对话框，第1页":::

选择或创建要与 Azure Functions 一起使用的应用服务计划。 如果不确定，请使用默认的消耗计划开始。

> [!IMPORTANT] 
> 发布 Azure 函数将产生额外费用，与 Azure 数字孪生无关。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-2.png" alt-text="Visual Studio：发布函数对话框，第2页":::

在以下页面上，为新的 function app、资源组和其他详细信息输入所需的名称。

## <a name="set-up-security-access-for-the-azure-function-app"></a>设置 Azure function app 的安全访问权限

以前示例中的 Azure function 主干要求向其传递持有者令牌，以便能够通过 Azure 数字孪生进行身份验证。 若要确保传递此持有者令牌，需要为 function app 设置 [托管服务标识 (MSI) ](../active-directory/managed-identities-azure-resources/overview.md) 。 只需对每个 function app 执行一次此操作。

若要进行此设置，请转到 [Azure 门户](https://portal.azure.com/) 并导航到 function app。

在 " *平台功能* " 选项卡中，选择 " *标识*"：

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-1.png" alt-text="Azure 门户：选择 Azure 函数的标识":::

在 "标识" 页上，将 " *状态* " 切换为 *"开*"。 

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-2.png" alt-text="Azure 门户：启用标识状态":::

另请注意此页上显示的 **对象 ID** ，这将在下一部分中使用。

### <a name="assign-access-roles"></a>分配访问角色

由于 Azure 数字孪生使用基于角色的访问控制来管理访问 (参阅 [*概念： Azure 数字孪生解决方案的安全性*](concepts-security.md) 有关此) 的详细信息，还需要为每个要允许其访问 Azure 数字的应用程序添加角色。

若要分配角色，需要创建的 Azure 数字孪生实例的 **资源 ID** 。 如果你之前未记录此实例，则可以使用以下命令检索它：

```bash
az dt show --name <your-instance-name> -g <your-resource-group-name>
```
资源 ID 将是输出的一部分，它是名为 "ID"、以字母 "/subscriptions/..." 开头的长字符串。

在下面的命令中，使用资源 ID 以及 Azure 函数的对象 ID，如下所示：

```azurecli
az role assignment create --role "Azure Digital Twins Owner (Preview)" --assignee <object-ID> --scope <resource-ID>
```

## <a name="next-steps"></a>后续步骤

本文介绍了设置 Azure 函数以用于 Azure 数字孪生的步骤。 接下来，你可以将 Azure 函数订阅到事件网格，以侦听终结点。 此终结点可能是：
* 附加到 Azure 数字孪生的事件网格终结点，用于处理来自 Azure 数字孪生本身的消息 (例如，属性更改消息、从整数图中的 [数字孪生](concepts-twins-graph.md) 生成的遥测消息或生命周期消息) 
* IoT 中心用来发送遥测数据和其他设备事件的 IoT 系统主题
* 接收来自其他服务的消息的事件网格端点

接下来，请参阅如何在基本 Azure 函数上构建，以将 IoT 中心数据引入 Azure 数字孪生：
* [*如何：从 IoT 中心引入遥测数据*](how-to-ingest-iot-hub-data.md)
