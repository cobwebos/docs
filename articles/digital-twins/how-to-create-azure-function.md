---
title: 设置用于处理数据的 Azure 函数
titleSuffix: Azure Digital Twins
description: 请参阅如何创建可通过数字孪生访问和触发的 Azure 函数。
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 11034a585db6ef3a3e1e445bbed8a1e51f83948d
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91949780"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>连接 Azure Functions 应用以处理数据

通过计算资源（如[Azure Functions](../azure-functions/functions-overview.md)）使用[**事件路由**](concepts-route-events.md)来处理基于数据的数字孪生。 Azure 函数可用于更新数字克隆，以响应：
* 来自 IoT 中心的设备遥测数据
* 属性更改或其他数据来自于克隆图形中的其他数字输出

本文逐步讲解如何创建 Azure 功能，以便与 Azure 数字孪生配合使用。 

下面概述了它包含的步骤：

1. 在 Visual Studio 中创建 Azure Functions 应用
2. 使用 [事件网格](../event-grid/overview.md) 触发器编写 Azure 函数
3. 将身份验证代码添加到函数 (以便能够访问 Azure 数字孪生) 
4. 将函数应用发布到 Azure
5. 设置 Azure function app 的 [安全](concepts-security.md) 访问权限

## <a name="create-an-azure-functions-app-in-visual-studio"></a>在 Visual Studio 中创建 Azure Functions 应用

在 Visual Studio 2019 中，选择 " _文件" > 新建 > 项目_ "并搜索 _Azure Functions_ 模板，然后选择" _下一步_"。

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 " _创建_"。

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 " _创建_"。

:::image type="content" source="media/how-to-create-azure-function/eventgridtrigger-function.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 ":::

创建 function app 后，visual studio 将在项目文件夹中的 **function.cs** 文件中自动填充代码示例。 此 short Azure 函数用于记录事件。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 ":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>使用事件网格触发器编写 Azure 函数

可以通过将 SDK 添加到 function app 来编写 Azure function。 Function app 使用 [适用于 (.net 的 Azure IoT 数字克隆客户端库 ) ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)与 Azure 数字孪生交互。 

若要使用 SDK，需要在项目中包含以下包。 可以使用 visual studio NuGet 包管理器安装包，也可以使用 `dotnet` 命令行工具添加包。 选择以下方法之一： 

**选项1。使用 Visual Studio 包管理器添加包：**
    
为此，可以在项目中右键选择，并从列表中选择 " _管理 NuGet 包_ "。 然后，在打开的窗口中，选择 " _浏览_ " 选项卡并搜索以下包。 选择 " _安装_ 并 _接受_ 许可协议" 以安装包。

* `Azure.DigitalTwins.Core`
* `Azure.Identity` 

若要配置 Azure SDK 管道以便正确设置 Azure Functions，还需要以下包。 重复上述过程，以安装所有包。

* `System.Net.Http`
* `Azure.Core.Pipeline`

**选项2。使用 `dotnet` 命令行工具添加包：**

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
dotnet add package Azure.identity --version 1.2.2
dotnet add package System.Net.Http
dotnet add package Azure.Core.Pipeline
```
接下来，在 Visual Studio 解决方案资源管理器中，打开 _function.cs_ 文件，其中包含示例代码，并向 Azure 函数添加以下 _using_ 语句。 

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;
```
## <a name="add-authentication-code-to-the-azure-function"></a>将身份验证代码添加到 Azure 函数

现在，你将声明类级别变量并添加允许函数访问 Azure 数字孪生的身份验证代码。 你将在 {你的函数名} .cs 文件中的 Azure 函数中添加以下项。

* 读取 ADT service URL 作为环境变量。 最好从环境变量中读取服务 URL，而不是在函数中对其进行硬编码。
```csharp     
private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
```
* 用于保存 HttpClient 实例的静态变量。 创建 HttpClient 的成本相对较高，因此，我们希望避免对每个函数调用执行此操作。
```csharp
private static readonly HttpClient httpClient = new HttpClient();
```
* 可以在 Azure 函数中使用托管标识凭据。
```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
```
* 在函数中添加一个本地变量 _DigitalTwinsClient_ ，以将 Azure 数字孪生客户端实例保存到函数项目。 不要 *使此* 变量在类中是静态的。
```csharp
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
```
* 为 _adtInstanceUrl_ 添加 null 检查，并在 try catch 块中包装函数逻辑以捕获任何异常。

完成这些更改后，函数代码将类似于以下内容：

```csharp
// Default URL for triggering event grid function in the local environment.
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;

namespace adtIngestFunctionSample
{
    public class Function1
    {
        //Your Digital Twin URL is stored in an application setting in Azure Functions
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("TwinsFunction")]
        public void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");
            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
                /*
                * Add your business logic here
                */
            }
            catch (Exception e)
            {
                log.LogError(e.Message);
            }

        }
    }
}
```

## <a name="publish-the-function-app-to-azure"></a>将函数应用发布到 Azure

若要将函数应用发布到 Azure，请右键选择函数项目 (不是解决方案资源管理器中的解决方案) ，然后选择 " **发布**"。

> [!IMPORTANT] 
> 发布 Azure 函数将产生额外费用，与 Azure 数字孪生无关。

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 " **下一步**"。

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-1.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 ":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-2.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 ":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-3.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 ":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-4.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 ":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-5.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 ":::

在以下页面上，为新的 function app、资源组和其他详细信息输入所需的名称。
为了使函数应用能够访问 Azure 数字孪生，它需要具有系统管理的标识并且有权访问 Azure 数字孪生实例。

接下来，你可以使用 CLI 或 Azure 门户为函数设置安全访问权限。 选择以下方法之一：

## <a name="set-up-security-access-for-the-azure-function-app"></a>设置 Azure function app 的安全访问权限
可以使用以下选项之一来设置 Azure function app 的安全访问权限：

### <a name="option-1-set-up-security-access-for-the-azure-function-app-using-cli"></a>选项1：使用 CLI 设置 Azure function app 的安全访问权限

以前示例中的 Azure function 主干要求向其传递持有者令牌，以便能够通过 Azure 数字孪生进行身份验证。 若要确保传递此持有者令牌，需要为 function app 设置 [托管服务标识 (MSI) ](../active-directory/managed-identities-azure-resources/overview.md) 。 只需对每个 function app 执行一次此操作。

你可以创建系统管理的标识，并将 function app 的标识分配给 azure 数字孪生实例 _ (预览) _ 角色。 这将为实例中的 function app 权限指定执行数据平面活动。 然后，通过设置环境变量，使 Azure 数字孪生实例的 URL 可供函数访问。

 使用 [Azure Cloud Shell](https://shell.azure.com) 运行命令。

使用以下命令创建系统管理的标识。 记下输出中的 principalId 字段。

```azurecli 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
使用以下命令中的 _principalId_ 值将 function app 的标识分配给 Azure 数字 _孪生所有者 (预览 _ azure 数字孪生实例的) 角色。

```azurecli 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```
最后，可以通过设置环境变量，使 Azure 数字孪生实例的 URL 可供函数访问。 有关设置环境变量的详细信息，请参阅 [*环境变量*](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables)。 

> [!TIP]
> Azure 数字孪生实例的 URL 是通过将 *https://* 添加到 Azure 数字孪生实例的 *主机名*的开头来完成的。 若要查看主机名以及实例的所有属性，可以运行 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` 。

```azurecli 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-azure-function-app-using-azure-portal"></a>选项2：使用 Azure 门户设置 Azure function app 的安全访问权限

系统分配的托管标识使 Azure 资源能够对云服务进行身份验证 (例如 Azure Key Vault) ，而不将凭据存储在代码中。 启用后，可以通过 Azure 基于角色的访问权限授予所有必要的权限。 此类托管标识的生命周期受此资源的生命周期的限制。 此外，每个资源 (例如，虚拟机) 只能有一个系统分配的托管标识。

在 [Azure 门户](https://portal.azure.com/)中，在搜索栏中搜索 " _function app_ "，其中包含之前创建的函数应用名称。 从列表中选择 *Function App* 。 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-functionapp.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 " 并 _保存_ 。 你将看到一个弹出窗口，用于 _启用系统分配的托管标识_。
选择 _"是"_ 按钮。 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 ":::

可以在通知中验证函数已成功注册到 Azure Active Directory。

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 " 页上显示的**对象 ID** ，它将在下一部分中使用。

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 ":::

### <a name="assign-access-roles-using-azure-portal"></a>使用 Azure 门户分配访问角色

选择 " _azure 角色分配_ " 按钮，打开 "azure 角色分配" 页。 然后，选择 " _+ 添加角色分配 (预览") _。

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 " 按钮以保存你的详细信息。

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 ":::

### <a name="configure-application-settings-using-azure-portal"></a>使用 Azure 门户配置应用程序设置

通过设置环境变量，可以使你的函数能够访问 Azure 数字孪生实例的 URL。 有关此内容的详细信息，请参阅 [*环境变量*](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables)。 应用程序设置公开为环境变量以访问数字孪生实例。 

需要 ADT_INSTANCE_URL 创建应用程序设置。

可以通过将 **_https://_** 追加到实例主机名来获取 ADT_INSTANCE_URL。 在 Azure 门户中，可以通过在搜索栏中搜索实例来找到数字孪生实例主机名。 然后，在左侧导航栏中选择 " _概述_ " 以查看 _主机名_。 复制此值以创建应用程序设置。

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 "

:::image type="content" source="media/how-to-create-azure-function/search-for-azure-function.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 ":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 ":::

在打开的窗口中，使用从上面复制的值创建应用程序设置。 \
_名称_  ： ADT_SERVICE_URL \
_值_ ： https：//{孪生}

选择 _"确定"_ 以创建应用程序设置。

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 " 按钮保存应用程序设置。

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 " 以重新启动应用程序。

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 " 图标来查看更新应用程序设置。 如果未创建应用程序设置，则可以按照上述过程重试添加应用程序设置。

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Visual Studio： &quot;新建项目&quot; 对话框&quot;:::

指定函数应用的名称，然后选择 ":::

## <a name="next-steps"></a>后续步骤

本文介绍了设置 Azure 函数以用于 Azure 数字孪生的步骤。 接下来，你可以将 Azure 函数订阅到事件网格，以侦听终结点。 此终结点可能是：
* 附加到 Azure 数字孪生的事件网格终结点，用于处理来自 Azure 数字孪生本身的消息 (例如，属性更改消息、从整数图中的 [数字孪生](concepts-twins-graph.md) 生成的遥测消息或生命周期消息) 
* IoT 中心用来发送遥测数据和其他设备事件的 IoT 系统主题
* 接收来自其他服务的消息的事件网格端点

接下来，请参阅如何在基本 Azure 函数上构建，以将 IoT 中心数据引入 Azure 数字孪生：
* [*如何：从 IoT 中心引入遥测数据*](how-to-ingest-iot-hub-data.md)