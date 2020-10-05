---
title: 快速入门：使用 .NET Core 创建管理组
description: 在本快速入门中，你将使用 .NET Core 创建管理组，将资源整理到资源层次结构中。
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 138998fdc23fd8a296ca50093e2952017888041f
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91604533"
---
# <a name="quickstart-create-a-management-group-with-net-core"></a>快速入门：使用 .NET Core 创建管理组

管理组是一些容器，可以帮助跨多个订阅管理访问权限、策略和符合性。 可以创建这些容器来构建可以与 [Azure Policy](../policy/overview.md) 和 [Azure 基于角色的访问控制](../../role-based-access-control/overview.md)配合使用的有效且高效的层次结构。 若要详细了解管理组，请参阅[使用 Azure 管理组整理资源](overview.md)。

在目录中创建的第一个管理组可能需要最多 15 分钟才能完成。 一些进程会首次运行以在 Azure 中为目录设置管理组服务。 在进程完成后将显示通知。 有关详细信息，请参阅[管理组的初始设置](./overview.md#initial-setup-of-management-groups)。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

- Azure 服务主体，包括 clientId 和 clientSecret 。 如果你没有与 Azure Policy 一起使用的服务主体，或想要创建一个新的服务主体，请参阅[用于 .NET 身份验证的 Azure 管理库](/dotnet/azure/sdk/authentication#mgmt-auth)。
  跳到安装 .NET Core 包的步骤，我们将在接下来的步骤中执行此安装。

- 如果未启用[层次结构保护](./how-to/protect-resource-hierarchy.md#setting---require-authorization)，则租户中的任何 Azure AD 用户即使未分配有管理组写入权限，也可创建管理组。 这个新的管理组将成为根管理组的子级或[默认管理组](./how-to/protect-resource-hierarchy.md#setting---default-management-group)，并将为创建者分配“所有者”角色。 管理组服务允许此功能，因此不需要在根级别分配角色。 创建根管理组时，用户没有访问权限。 为避免在查找 Azure AD 全局管理员以开始使用管理组方面遇到阻碍，我们允许在根级别创建初始管理组。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>应用程序设置

若要使 .NET Core 能够管理管理组，请创建新的控制台应用程序并安装所需的包。

1. 请确保安装最新的 .NET Core（至少为 3.1.8）。 如果尚未安装，请前往 [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core) 下载。

1. 初始化名为“mgCreate”的新 .NET Core 控制台应用程序：

   ```dotnetcli
   dotnet new console --name "mgCreate"
   ```

1. 将目录切换到新项目文件夹，并安装 Azure Policy 所需的包：

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ManagementGroups --version 1.1.1-preview

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
   using Microsoft.Azure.Management.ManagementGroups;
   using Microsoft.Azure.Management.ManagementGroups.Models;
   
   namespace mgCreate
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strGroupId = args[3];
               string strDisplayName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                      "https://management.core.windows.net",
                      new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new ManagementGroupsAPIClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var mgRequest = new CreateManagementGroupRequest
                   {
                       DisplayName = strDisplayName
                   };
                   var response = await client.ManagementGroups.CreateOrUpdateAsync(strGroupId, mgRequest);
               }
           }
       }
   }
   ```

1. 生成并发布 `mgCreate` 控制台应用程序：

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-the-management-group"></a>创建管理组

在本快速入门中，你将在根管理组中创建一个新的管理组。

1. 将目录更改为使用先前的 `dotnet publish` 命令定义的 `{run-folder}`。

1. 在终端中输入以下命令：

   ```bash
   mgCreate.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{groupID}" `
      "{displayName}"
   ```

上述命令使用以下信息：

- `{tenantId}` - 替换为租户 ID
- `{clientId}` -替换为服务主体的客户端 ID
- `{clientSecret}` -替换为服务主体的客户端机密
- `{groupID}` - 替换为新的管理组的 ID
- `{displayName}` - 替换为新的管理组的易记名称

结果是根管理组中的一个新的管理组。

## <a name="clean-up-resources"></a>清理资源

- 通过门户删除新的资源组。

- 如果要删除 .NET Core 控制台应用程序和已安装的包，可以删除 `mgCreate` 项目文件夹。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个管理组来整理资源层次结构。 管理组可以包含订阅或其他管理组。

要详细了解管理组以及如何管理资源层次结构，请继续执行以下操作：

> [!div class="nextstepaction"]
> [使用管理组管理资源](./manage.md)