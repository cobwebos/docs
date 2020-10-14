---
title: 快速入门：第一个 .NET Core 查询
description: 本快速入门介绍为 .NET Core 启用 Resource Graph NuGet 包并运行第一个查询的步骤。
ms.date: 10/14/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 5f825b22712956c0b94531ddb163301167f30f00
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057461"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-net-core"></a>快速入门：使用 .NET Core 运行你的第一个 Resource Graph 查询

使用 Azure Resource Graph 的第一步是确保为 .NET Core 安装了所需的包。 本快速入门将指导你完成将该包添加到 .NET Core 安装的过程。

在此过程结束时，你已将该包添加到 .NET Core 安装中，并将运行你的第一个 Resource Graph 查询。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。
- Azure 服务主体，包括 clientId 和 clientSecret 。 如果你没有与 Resource Graph 一起使用的服务主体，或想要创建一个新的服务主体，请参阅[用于 .NET 身份验证的 Azure 管理库](/dotnet/azure/sdk/authentication#mgmt-auth)。
  跳到安装 .NET Core 包的步骤，我们将在接下来的步骤中执行此安装。

## <a name="create-the-resource-graph-project"></a>创建 Resource Graph 项目

若要使 .NET Core 能够查询 Azure Resource Graph，请创建新的控制台应用程序并安装所需的包。

1. 请确保安装最新的 .NET Core（至少为 3.1.5）。 如果尚未安装，请前往 [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core) 下载。

1. 初始化名为“argQuery”的新 .NET Core 控制台应用程序：

   ```dotnetcli
   dotnet new console --name "argQuery"
   ```

1. 将目录切换到新项目文件夹，并安装 Azure Resource Graph 所需的包：

   ```dotnetcli
   # Add the Resource Graph package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceGraph --version 2.0.0

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. 用以下代码替换默认的 `program.cs`，并保存更新的文件：

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ResourceGraph;
   using Microsoft.Azure.Management.ResourceGraph.Models;

   namespace argQuery
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strQuery = args[4];

               AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/" + strTenant);
               AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://management.core.windows.net", new ClientCredential(strClientId, strClientSecret));
               ServiceClientCredentials serviceClientCreds = new TokenCredentials(authResult.AccessToken);

               ResourceGraphClient argClient = new ResourceGraphClient(serviceClientCreds);
               QueryRequest request = new QueryRequest();
               request.Subscriptions = new List<string>(){ strSubscriptionId };
               request.Query = strQuery;

               QueryResponse response = argClient.Resources(request);
               Console.WriteLine("Records: " + response.Count);
               Console.WriteLine("Data:\n" + response.Data);
           }
       }
   }
   ```

1. 生成并发布 `argQuery` 控制台应用程序：

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="run-your-first-resource-graph-query"></a>运行首个 Resource Graph 查询

生成并发布 .NET Core 控制台应用程序之后，即可尝试创建简单的 Resource Graph 查询。 该查询返回前五个 Azure 资源，以及每个资源的名称和资源类型 。

在对 `argQuery` 的每次调用中，都包含替换为自己的值时需要使用的变量：

- `{tenantId}` - 替换为租户 ID
- `{clientId}` -替换为服务主体的客户端 ID
- `{clientSecret}` -替换为服务主体的客户端机密
- `{subscriptionId}` - 替换为订阅 ID

1. 将目录更改为使用先前的 `dotnet publish` 命令定义的 `{run-folder}`。

1. 使用已编译的 .NET Core 控制台应用程序运行首个 Azure Resource Graph 查询：

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > 由于此查询示例未提供排序修饰符（例如 `order by`），因此多次运行此查询可能会为每个请求生成一组不同的资源。

1. 将最后一个参数更改为 `argQuery.exe`，并将查询更改为按 Name 属性应用 `order by`：

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > 与第一个查询一样，多次运行此查询可能会为每个请求生成一组不同的资源。 查询命令的顺序非常重要。 在本例中，`order by` 位于 `limit` 之后。 命令按此顺序执行，首先会限制查询结果，然后对它们进行排序。

1. 将最后一个参数更改为 `argQuery.exe`，并将查询更改为先按 Name 属性应用 `order by`，然后对前五个结果应用 `limit`：

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

假设环境中没有任何变化，则多次运行最后一个查询时，返回的结果将是一致的且按 Name 属性排序，但仍限制为前五个结果。

## <a name="clean-up-resources"></a>清理资源

如果要删除 .NETCore 控制台应用程序和已安装的包，可以通过删除 `argQuery` 项目文件夹来完成。

## <a name="next-steps"></a>后续步骤

在此快速入门中，你已经创建了一个使用所需 Resource Graph 包的 .NET Core 控制台应用程序，并运行了第一个查询。 若要详细了解 Resource Graph 语言，请继续阅读查询语言详细信息页。

> [!div class="nextstepaction"]
> [获取有关查询语言的详细信息](./concepts/query-language.md)