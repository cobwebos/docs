---
title: 快速入门：使用 .NET Core 新建策略分配
description: 本快速入门介绍如何使用 .NET Core 创建 Azure Policy 分配以识别不合规资源。
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: c4c8f8e9df544b6fc00b5b7701435f5a606f9764
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91604536"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-net-core"></a>快速入门：使用 .NET Core 创建策略分配以识别不合规资源

若要了解 Azure 中的符合性，第一步是确定资源的状态。 在本快速入门中，我们将创建策略分配，以识别未使用托管磁盘的虚拟机。 完成后，我们便可以识别不合规的虚拟机。 

.NET Core 库用于管理 Azure 资源。 本指南介绍如何使用适用于 Azure Policy 的 .NET Core 库来创建策略分配。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。
- Azure 服务主体，包括 clientId 和 clientSecret 。 如果你没有与 Azure Policy 一起使用的服务主体，或想要创建一个新的服务主体，请参阅[用于 .NET 身份验证的 Azure 管理库](/dotnet/azure/sdk/authentication#mgmt-auth)。
  跳到安装 .NET Core 包的步骤，我们将在接下来的步骤中执行此安装。

## <a name="create-the-azure-policy-project"></a>创建 Azure Policy 项目

若要使 .NET Core 能够管理 Azure Policy，请创建新的控制台应用程序并安装所需的包。

1. 请确保安装最新的 .NET Core（至少为 3.1.8）。 如果尚未安装，请前往 [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core) 下载。

1. 初始化名为“policyAssignment”的新 .NET Core 控制台应用程序：

   ```dotnetcli
   dotnet new console --name "policyAssignment"
   ```

1. 将目录切换到新项目文件夹，并安装 Azure Policy 所需的包：

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceManager --version 3.10.0-preview

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
   using Microsoft.Azure.Management.ResourceManager;
   using Microsoft.Azure.Management.ResourceManager.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
               string strDisplayName = args[5];
               string strPolicyDefID = args[6];
               string strDescription = args[7];
               string strScope = args[8];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));

               using (var client = new PolicyClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyAssignment = new PolicyAssignment
                   {
                       DisplayName = strDisplayName,
                       PolicyDefinitionId = strPolicyDefID,
                       Description = strDescription
                   };
                   var response = await client.PolicyAssignments.CreateAsync(strScope, strName, policyAssignment);
               }
           }
       }
   }
   ```

1. 生成并发布 `policyAssignment` 控制台应用程序：

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-a-policy-assignment"></a>创建策略分配

本快速入门将创建一个策略分配，并分配“审核未使用托管磁盘的 VM”(`06a78e20-9358-41c9-923c-fb736d382a4d`) 定义。 此策略定义可识别不符合策略定义中设置的条件的资源。

1. 将目录更改为使用先前的 `dotnet publish` 命令定义的 `{run-folder}`。

1. 在终端中输入以下命令：

   ```bash
   policyAssignment.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks" `
      "Audit VMs without managed disks Assignment" `
      "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      "Shows all virtual machines not using managed disks" `
      "{scope}"
   ```

上述命令使用以下信息：

- `{tenantId}` - 替换为租户 ID
- `{clientId}` -替换为服务主体的客户端 ID
- `{clientSecret}` -替换为服务主体的客户端机密
- `{subscriptionId}` - 替换为订阅 ID
- **name** - 策略分配对象的唯一名称。 上面的示例使用 audit-vm-manageddisks。
- **displayName** - 策略分配的显示名称。 本例使用了“审核未使用托管磁盘分配的虚拟机”  。
- **policyDefID** - 策略定义路径，用作创建分配的依据。 在本例中，它为策略定义“审核未使用托管磁盘的 VM”的 ID  。
- **说明** - 有关策略用途或将其分配到此范围的原因的更深入说明。
- **作用域** - 作用域确定要对哪些资源或资源分组实施策略分配。 它的范围可以从管理组到单个资源。 请确保将 `{scope}` 替换为以下某个模式：
  - 管理组：`/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - 订阅：`/subscriptions/{subscriptionId}`
  - 资源组：`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - 资源：`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

你现已准备好识别不合规的资源，了解环境的符合性状态。

## <a name="identify-non-compliant-resources"></a>识别不合规的资源

创建策略分配后，可以识别不合规的资源。

1. 初始化名为“policyCompliance”的新 .NET Core 控制台应用程序：

   ```dotnetcli
   dotnet new console --name "policyCompliance"
   ```

1. 将目录切换到新项目文件夹，并安装 Azure Policy 所需的包：

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.PolicyInsights --version 3.1.0

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
   using Microsoft.Azure.Management.PolicyInsights;
   using Microsoft.Azure.Management.PolicyInsights.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new PolicyInsightsClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyQueryOptions = new QueryOptions
                   {
                       Filter = $"IsCompliant eq false and PolicyAssignmentId eq '{strName}'",
                       Apply = "groupby(ResourceId)"
                   };
   
                   var response = await client.PolicyStates.ListQueryResultsForSubscriptionAsync(
                       "latest", strSubscriptionId, policyQueryOptions);
                   Console.WriteLine(response.Odatacount);
               }
           }
       }
   }
   ```

1. 生成并发布 `policyAssignment` 控制台应用程序：

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

1. 将目录更改为使用先前的 `dotnet publish` 命令定义的 `{run-folder}`。

1. 在终端中输入以下命令：

   ```bash
   policyCompliance.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks"
   ```

上述命令使用以下信息：

- `{tenantId}` - 替换为租户 ID
- `{clientId}` -替换为服务主体的客户端 ID
- `{clientSecret}` -替换为服务主体的客户端机密
- `{subscriptionId}` - 替换为订阅 ID
- **name** - 策略分配对象的唯一名称。 上面的示例使用 audit-vm-manageddisks。

`response` 中的结果与 Azure 门户视图中策略分配的“资源符合性”选项卡中显示的内容相匹配。

## <a name="clean-up-resources"></a>清理资源

- 通过门户删除策略分配“审核不带托管磁盘分配的 VM”。 策略定义是内置的，因此没有要删除的定义。

- 如果要删除 .NET Core 控制台应用程序和已安装的包，可以删除 `policyAssignment` 和 `policyCompliance` 项目文件夹。

## <a name="next-steps"></a>后续步骤

本快速入门已分配一个策略定义用于识别 Azure 环境中的不合规资源。

要了解有关分配策略定义以验证新资源是否符合要求的详细信息，请继续以下教程：

> [!div class="nextstepaction"]
> [创建和管理策略](./tutorials/create-and-manage.md)
