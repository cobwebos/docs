---
title: 为客户端应用编写代码
titleSuffix: Azure Digital Twins
description: 使用 .NET (C#) SDK 为客户端应用编写基础代码的教程。
author: cschormann
ms.author: cschorm
ms.date: 05/05/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 7e057d6d973eedd3ac53fd7b2ea228470e9123d7
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2020
ms.locfileid: "84611480"
---
# <a name="coding-with-the-azure-digital-twins-apis"></a>使用 Azure 数字孪生 API 编写代码

开发人员使用 Azure 数字孪生编写客户端应用程序，以与其 Azure 数字孪生服务实例交互，这是很常见的情况。 这篇面向开发人员的教程介绍如何使用[适用于 .NET 的 Azure IoT 数字孪生客户端库 (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) 对 Azure 数字孪生服务进行编程。 本教程会逐步引导你从头开始编写 C# 控制台客户端应用。

## <a name="prerequisites"></a>先决条件

本教程使用命令行执行设置和项目工作。 因此，你可以使用任何代码编辑器来逐步完成练习。

准备工作：
* 任何代码编辑器
* 开发电脑上安装有 .NET Core 3.1。 你可以从[下载 .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) 下载适用于多个平台的此版 .NET Core SDK。

[!INCLUDE [Azure Digital Twins tutorials: instance prereq](../../includes/digital-twins-tutorial-prereq-instance.md)]

## <a name="set-up-project"></a>设置项目

准备好使用 Azure 数字孪生实例后，请开始设置客户端应用项目。 

在计算机上打开命令提示符或其他控制台窗口，创建用于存储本教程工作的空项目目录。 将目录命名为任何你喜欢的名称（例如，DigitalTwinsCodeTutorial）。

导航到新目录。

进入项目目录后，创建一个空的 .NET 控制台应用项目。 在命令窗口中运行以下命令，为控制台创建基础 C# 项目：

```cmd/sh
dotnet new console
```

这会在你的目录中创建多个文件，其中包括一个名为 Program.cs 的文件，你将在其中编写大部分代码。

接下来，添加两个使用 Azure 数字孪生所必须的依赖项：

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.2
dotnet add package Azure.identity
```

第一个依赖项是[适用于 .NET 的 Azure IoT 数字孪生客户端库](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)。 第二个依赖项会提供可帮助进行 Azure 身份验证的工具。

让命令窗口保持打开状态，因为整个教程都要继续使用该窗口。

## <a name="get-started-with-project-code"></a>开始使用项目代码

在本部分中，你将开始编写新应用项目的代码以使用 Azure 数字孪生。 涵盖的操作包括：
* 对服务进行身份验证
* 上传模型
* 捕获错误
* 创建数字孪生
* 创建关系
* 查询数字孪生

在本教程末尾，另有一节显示了完整的代码。 你可以将其用作参考，随时检查自己的程序。

开始前，请在任何代码编辑器中打开 Program.cs 文件。 你将看到如下所示的基础代码模板：

```csharp
using System;

namespace DigitalTwinsCodeTutorial
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

首先，在代码的顶部添加一些 `using` 行，以拉取必需的依赖项。

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
```

接下来，需要向此文件添加代码以扩充某些功能。 

### <a name="authenticate-against-the-service"></a>对服务进行身份验证

应用必须执行的第一项操作是对 Azure 数字孪生服务进行身份验证。 接着，你可以创建服务客户端类来访问 SDK 函数。

为进行身份验证，需要以下三条信息：
* 订阅的目录（租户）ID
* 在之前设置服务实例时创建的应用程序（客户端）ID
* 服务实例的主机名

>[!TIP]
> 如果不知道自己的目录（租户）ID，可以在 [Azure Cloud Shell](https://shell.azure.com) 中运行以下命令来获取该 ID：
> 
> ```azurecli-interactive
> az account show --query tenantId
> ```

在 Program.cs 中，将以下代码粘贴到 `Main` 方法中的“Hello, World!”输出行下方。 将 `adtInstanceUrl` 的值设置为 Azure 数字孪生实例的主机名，将 `clientId` 设置为应用程序 ID，并将 `tenantId` 设置为目录 ID  。

```csharp
string clientId = "<your-application-ID>";
string tenantId = "<your-directory-ID>";
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
Console.WriteLine($"Service client created – ready to go");
```

保存文件。 

请注意，此示例使用交互式浏览器凭据：
```csharp
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
```

这种类型的凭据将导致浏览器窗口打开，并要求你提供 Azure 凭据。 

>[!NOTE]
> 要了解其他类型的凭据，请参阅 [Microsoft 标识平台身份验证库](../active-directory/develop/reference-v2-libraries.md)文档。

在命令窗口中，使用以下命令运行代码： 

```cmd/sh
dotnet run
```

首次运行时，这会还原依赖项，然后执行程序。 
* 如果未发生错误，程序将输出“Service client created - ready to go”（服务客户端已创建 - 准备就绪）。
* 由于此项目中没有任何错误处理机制，因此如果出现问题，你将看到代码引发的异常。

### <a name="upload-a-model"></a>上传模型

Azure 数字孪生没有内部域词汇。 环境中可在 Azure 数字孪生中表示的元素类型由你使用模型定义。 [模型](concepts-models.md)类似于面向对象的编程语言中的类；它们为[数字孪生](concepts-twins-graph.md)提供了日后可遵循并实例化的用户定义的模板。 它们以类似于 JSON 的语言编写，这种语言称为数字孪生定义语言 (DTDL)。

创建 Azure 数字孪生解决方案的第一步是在 DTDL 文件中至少定义一个模型。

在创建项目的目录中，创建名为 SampleModel.json 的新 .json 文件 。 粘贴以下文件主体： 

```json
{
  "@id": "dtmi:com:contoso:SampleModel;1",
  "@type": "Interface",
  "displayName": "SampleModel",
  "contents": [
    {
      "@type": "Relationship",
      "name": "contains"
    },
    {
      "@type": "Property",
      "name": "data",
      "schema": "string"
    }
  ],
  "@context": "dtmi:dtdl:context;2"
}
```

> [!TIP]
> 如果在本教程中使用 Visual Studio，建议你选择新创建的 JSON 文件，并将属性检查器中的“复制到输出目录”属性设置为“有更新时才复制”或“始终复制”  。 当你在本教程的其余部分使用 F5 执行程序时，这可让 Visual Studio 找到具有默认路径的 JSON 文件。

> [!TIP] 
> 有一种与语言无关的 [DTDL 验证程序示例](https://github.com/Azure-Samples/DTDL-Validator)，可用来检查模型文件，以确保 DTDL 有效。 它是在 DTDL 分析程序库的基础上构建的，详情请参阅[操作方法：分析和验证模型](how-to-use-parser.md)。

接下来，向 Program.cs 添加更多代码，将刚才创建的模型上传到 Azure 数字孪生实例。

首先，在文件顶部添加几个 `using` 语句：

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Models;
```

接下来，将 `Main` 方法签名更改为允许异步执行，准备使用 C# 服务 SDK 中的异步方法。 

```csharp
static async Task Main(string[] args)
```

> [!NOTE]
> 因为 SDK 也提供所有调用的同步版本，所以不一定非使用 `async` 不可。 本教程的练习使用 `async`。

接下来是与 Azure 数字孪生服务交互的第一段代码。 此代码将加载你从磁盘创建的 DTDL 文件，然后将其上传到 Azure 数字孪生服务实例。 

将以下代码粘贴到前面添加的授权代码下。

```csharp
Console.WriteLine();
Console.WriteLine($"Upload a model");
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
// Upload the model to the service
await client.CreateModelsAsync(typeList);
```

在命令窗口中，使用以下命令运行程序： 

```cmd/sh
dotnet run
```

你会发现，此时没有任何指示调用成功的输出。 

若要添加一个指示模型是否确实已成功上传的 print 语句，请在上一段代码后面添加以下代码：

```csharp
// Read a list of models back from the service
AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
await foreach (ModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

再次运行程序以测试这段新代码之前，请记得，在上次运行该程序时，已上传了模型。 Azure 数字孪生不允许将相同的模型上传两次，因此，在重新运行此程序时，应该会看到异常。

现在，在命令窗口中，再次使用以下命令运行程序：

```cmd/sh
dotnet run
```

程序应该会引发异常。 当你尝试上传已上传过的模型时，服务会通过 REST API 返回“请求错误”错误。 因此，Azure 数字孪生客户端 SDK 会针对不成功的每个服务返回代码引发异常。 

下一节将讨论与此类似的异常以及如何在代码中予以处理。

### <a name="catch-errors"></a>捕获错误

为了防止程序崩溃，可以在模型上传代码周围添加异常代码。 在 try/catch 处理程序中包装现有的客户端调用 `client.CreateModelsAsync`，如下所示：

```csharp
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
```

如果在命令窗口中使用 `dotnet run` 运行程序，则会看到返回的错误代码。 输出结果如下所示：

```cmd/sh
Hello World!
Service client created - ready to go

Upload a model
Load model: 409:Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"DocumentAlreadyExists","message":"A document with same identifier already exists.","details":[]}}

Headers:
api-supported-versions: REDACTED
Date: Tue, 05 May 2020 01:57:51 GMT
Content-Length: 115
Content-Type: application/json; charset=utf-8

Type name: : dtmi:com:contoso:SampleModel;1
```

从此时起，本教程会将所有对服务方法的调用包装在 try/catch 处理程序中。

### <a name="create-digital-twins"></a>创建数字孪生

将模型上传到 Azure 数字孪生后，可以使用此模型定义创建数字孪生。 [数字孪生](concepts-twins-graph.md)是模型的实例，表示业务环境中的实体，例如农场中的传感器、大楼中的房间或汽车上的灯。 本部分将在你之前上传的模型的基础上创建几个数字孪生。

在顶部添加新的 `using` 语句，因为在 `System.Text.Json` 中需要内置的 .NET Json 序列化程序：

```csharp
using System.Text.Json;
```

然后，将以下代码添加到 `Main` 方法的末尾，以根据此模型创建和初始化三个数字孪生。

```csharp
// Initialize twin metadata
var meta = new Dictionary<string, object>
{
    { "$model", "dtmi:com:contoso:SampleModel;1" },
};
// Initialize the twin properties
var initData = new Dictionary<string, object>
{
    { "$metadata", meta },
    { "data", "Hello World!" }
};
string prefix="sampleTwin-";
for(int i=0; i<3; i++) {
    try {
        await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(initData));
        Console.WriteLine($"Created twin: {prefix}{i}");
    } catch(RequestFailedException rex) {
        Console.WriteLine($"Create twin: {rex.Status}:{rex.Message}");  
    }
}
```

在命令窗口中，使用 `dotnet run` 运行程序。 然后，再次运行该程序。 

请注意，第二次创建孪生时，即使孪生在第一次运行后已经存在，也不会引发任何错误。 与创建模型不同，在 REST 级别上，创建孪生是 PUT 调用加上 upsert 语义 。 这意味着，如果已存在一个孪生，尝试再次创建将会替换该孪生。 不需要有任何错误。

### <a name="create-relationships"></a>创建关系

接下来，你可以在已创建的孪生之间创建关系，将它们连接到孪生图 。 [孪生图](concepts-twins-graph.md)用于表示整个环境。

为了能够创建关系，请在 SDK 中为关系基类型添加 `using` 语句：
```csharp
using Azure.DigitalTwins.Core.Serialization;
```

接下来，将新的静态方法添加到 `Main` 方法下的 `Program` 类：
```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

然后，将以下代码添加到 `Main` 方法的末尾，以调用 `CreateRelationship` 代码：
```csharp
// Connect the twins with relationships
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");
```

在命令窗口中，使用 `dotnet run` 运行程序。

请注意，如果已存在 ID 相同的关系，Azure 数字孪生将不会让你创建关系；因此，如果多次运行程序，你将会看到有关创建关系的异常。 此代码会捕获异常并将其忽略。 

### <a name="list-relationships"></a>列出关系

你将添加的下一段代码可让你查看已创建的关系列表。

将以下新方法添加到 `Program` 类：

```csharp
public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
{
    try {
        AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
        Console.WriteLine($"Twin {srcId} is connected to:");
        await foreach (string rel in results)
        {
            var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
            Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
        }
    } catch (RequestFailedException rex) {
        Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
    }
}
```

然后，将以下代码添加到 `Main` 方法的末尾，以调用 `ListRelationships` 代码：

```csharp
//List the relationships
await ListRelationships(client, "sampleTwin-0");
```

在命令窗口中，使用 `dotnet run` 运行程序。 你应该会看到已创建的所有关系列表。

### <a name="query-digital-twins"></a>查询数字孪生

Azure 数字孪生的主要功能是能够轻松有效地[查询](concepts-query-language.md)孪生图，以解答有关环境的问题。

要在本教程中添加的最后一段代码会对对 Azure 数字孪生实例运行查询。 本示例中使用的查询会返回此实例中的所有数字孪生。

在 `Main` 方法的末尾添加以下代码：

```csharp
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    Console.WriteLine("---------------");
}
```

在命令窗口中，使用 `dotnet run` 运行程序。 你应该会在输出中看到此实例中的所有数字孪生。

## <a name="complete-code-example"></a>完整代码示例

到本教程的此阶段，你已有一个完整的客户端应用，能够对 Azure 数字孪生执行基本操作。 下面列出了 Program.cs 中的程序的完整代码供你参考：

```csharp
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Models;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            
            string clientId = "<your-application-ID>";
            string tenantId = "<your-directory-ID>";
            string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
            var credentials = new InteractiveBrowserCredential(tenantId, clientId);
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            Console.WriteLine($"Service client created – ready to go");

            Console.WriteLine();
            Console.WriteLine($"Upload a model");
            var typeList = new List<string>();
            string dtdl = File.ReadAllText("SampleModel.json");
            typeList.Add(dtdl);
            // Upload the model to the service
            try {
                await client.CreateModelsAsync(typeList);
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
            }
            // Read a list of models back from the service
            AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
            await foreach (ModelData md in modelDataList)
            {
                Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
            }

            // Initialize twin metadata
            var meta = new Dictionary<string, object>
            {
                { "$model", "dtmi:com:contoso:SampleModel;1" },
            };
            // Initialize the twin properties
            var initData = new Dictionary<string, object>
            {
                { "$metadata", meta },
                { "data", "Hello World!" }
            };
            string prefix="sampleTwin-";
            for(int i=0; i<3; i++) {
                try {
                    await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(initData));
                    Console.WriteLine($"Created twin: {prefix}{i}");
                } catch(RequestFailedException rex) {
                    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
                }
            }

            // Connect the twins with relationships
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");

            //List the relationships
            await ListRelationships(client, "sampleTwin-0");

            // Run a query    
            AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
            await foreach (string twin in result)
            {
                object jsonObj = JsonSerializer.Deserialize<object>(twin);
                string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
                Console.WriteLine(prettyTwin);
                Console.WriteLine("---------------");
            }
        }

        public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = "contains"
            };
        
            try
            {
                string relId = $"{srcId}-contains->{targetId}";
                await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
                Console.WriteLine("Created relationship successfully");
            }
            catch (RequestFailedException rex) {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
        }
        
        public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
        {
            try {
                AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
                Console.WriteLine($"Twin {srcId} is connected to:");
                await foreach (string rel in results)
                {
                    var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
                    Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
                }
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
            }
        }

    }
}
```
## <a name="clean-up-resources"></a>清理资源
 
本教程中使用的实例可重复用于下一教程[教程：使用示例客户端应用了解基础知识](tutorial-command-line-app.md)。 如果打算继续学习下一个教程，可以保留在此处设置的 Azure 数字孪生实例。
 
如果不再需要本教程中创建的资源，请按照以下步骤将其删除。

利用 [Azure Cloud Shell](https://shell.azure.com)，你可以使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 命令删除资源组中的所有 Azure 资源。 这将删除资源组和 Azure 数字孪生实例。

> [!IMPORTANT]
> 删除资源组的操作不可逆。 资源组以及包含在其中的所有资源将被永久删除。 请确保不会意外删除错误的资源组或资源。 

打开 Azure Cloud Shell 并运行以下命令，以删除资源组及其包含的所有内容。

```azurecli-interactive
az group delete --name <your-resource-group>
```

接下来，使用以下命令删除为客户端应用创建的 Azure Active Directory 应用注册：

```azurecli
az ad app delete --id <your-application-ID>
```

最后，删除在本地计算机上创建的项目文件夹。

## <a name="next-steps"></a>后续步骤

在本教程中，你从头开始创建了 .NET 控制台客户端应用程序。 你为此客户端应用编写了代码，在 Azure 数字孪生实例上执行基本操作。

请继续学习下一个教程，了解可使用这类示例客户端应用执行的操作： 

> [!div class="nextstepaction"]
> [教程：使用示例客户端应用了解基础知识](tutorial-command-line-app.md)

你也可以在操作说明文章中了解更多管理操作，以增加在本教程中编写的代码，或开始查看概念文档，详细了解教程中使用的元素。
* [操作说明：管理孪生模型](how-to-manage-model.md)
* [概念：自定义模型](concepts-models.md)
