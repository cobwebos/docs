---
title: 适用于 .NET 的 Azure CDN 库入门 | Microsoft Docs
description: 了解如何编写 .NET 应用程序以使用 Visual Studio 管理 Azure CDN。
services: cdn
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 63cf4101-92e7-49dd-a155-a90e54a792ca
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.custom: has-adal-ref
ms.openlocfilehash: e03616bf0d02f7ce063c027912cba4ab4e8f8d3f
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611460"
---
# <a name="get-started-with-azure-cdn-development"></a>Azure CDN 开发入门
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
>
>

你可以使用[适用于 .net 的 Azure CDN 库](/dotnet/api/overview/azure/cdn)来自动创建和管理 CDN 配置文件和终结点。  本教程介绍一个简单的 .NET 控制台应用程序的创建示例，演示几个可用的操作。  本教程不打算详细描述适用于 .NET 的 Azure CDN 库的所有方面。

需要 Visual Studio 2015 来完成本教程。  可免费下载 [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)。

> [!TIP]
> 可在 MSDN 上下载[本教程中已完成的项目](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c)。
>
>

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>创建项目并添加 Nuget 包
现在我们已经为 CDN 配置文件创建了一个资源组，并已将管理该组中的 CDN 配置文件和终结点的权限授予 Azure AD 应用程序，我们可以开始创建我们的应用程序。

在 Visual Studio 2015 中依次单击“**文件**”、“**新建**”和“**项目...**”以打开新项目对话框。  展开“**Visual C#**”，并在左侧窗格中选择“**Windows**”。  在中心窗格中单击“**控制台应用程序**”。  命名项目，然后单击“**确定**”。

![新建项目](./media/cdn-app-dev-net/cdn-new-project.png)

我们的项目将使用 Nuget 包中包含的部分 Azure 库。  让我们将它们添加到项目。

1. 依次单击“**工具**”菜单、“**Nuget 包管理器**”和“**包管理器控制台**”。

    ![管理 NuGet 包](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. 在“包管理器控制台”中，执行以下命令以安装 **Active Directory 验证库 (ADAL)**：

    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. 执行以下命令来安装 **Azure CDN 管理库**:

    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>指令、常量、主方法和帮助器方法
让我们来编写程序的基本结构。

1. 返回“Program.cs”选项卡，将顶部的 `using` 指令替换为以下内容：

    ```csharp
    using System;
    using System.Collections.Generic;
    using Microsoft.Azure.Management.Cdn;
    using Microsoft.Azure.Management.Cdn.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
2. 我们需要定义我们的方法将使用的一些常量。  在 `Program` 类中，但在 `Main` 方法之前，添加以下内容。  请确保将占位符（包括** &lt;尖括号&gt;**）替换为所需的值。

    ```csharp
    //Tenant app constants
    private const string clientID = "<YOUR CLIENT ID>";
    private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";

    //Application constants
    private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
    private const string profileName = "CdnConsoleApp";
    private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
    private const string resourceGroupName = "CdnConsoleTutorial";
    private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. 也要在该类级别上定义这两个变量。  我们稍后将使用这些变量来确定我们的配置文件和终结点是否已存在。

    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```
4. 按如下所示替换 `Main` 方法：

   ```csharp
   static void Main(string[] args)
   {
       //Get a token
       AuthenticationResult authResult = GetAccessToken();

       // Create CDN client
       CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
           { SubscriptionId = subscriptionId };

       ListProfilesAndEndpoints(cdn);

       // Create CDN Profile
       CreateCdnProfile(cdn);

       // Create CDN Endpoint
       CreateCdnEndpoint(cdn);

       Console.WriteLine();

       // Purge CDN Endpoint
       PromptPurgeCdnEndpoint(cdn);

       // Delete CDN Endpoint
       PromptDeleteCdnEndpoint(cdn);

       // Delete CDN Profile
       PromptDeleteCdnProfile(cdn);

       Console.WriteLine("Press Enter to end program.");
       Console.ReadLine();
   }
   ```
5. 我们的一些其他方法会用“是/否”问题提示用户。  添加以下方法，使其更容易：

    ```csharp
    private static bool PromptUser(string Question)
    {
        Console.Write(Question + " (Y/N): ");
        var response = Console.ReadKey();
        Console.WriteLine();
        if (response.Key == ConsoleKey.Y)
        {
            return true;
        }
        else if (response.Key == ConsoleKey.N)
        {
            return false;
        }
        else
        {
            // They pressed something other than Y or N.  Let's ask them again.
            return PromptUser(Question);
        }
    }
    ```

既然已经编写了程序的基本结构，我们现在应该创建 `Main` 方法调用的方法。

## <a name="authentication"></a>身份验证
在可以使用 Azure CDN 管理库之前，我们需要验证我们的服务主体并获取身份验证令牌。  此方法使用 ADAL 来检索令牌。

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    ClientCredential credential = new ClientCredential(clientID, clientSecret);
    AuthenticationResult authResult =
        authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

    return authResult;
}
```

如果使用的是个人用户身份验证，`GetAccessToken` 方法看起来会稍有不同。

> [!IMPORTANT]
> 仅选择个人用户身份验证而不是服务主体身份验证时，才使用此代码示例。
>
>

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
        clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

    return authResult;
}
```

在 Azure AD 中注册应用程序时，请确保将输入的重定向 URI 替换为 `<redirect URI>`。

## <a name="list-cdn-profiles-and-endpoints"></a>列出 CDN 配置文件和终结点
现在，我们已准备好执行 CDN 操作。  我们的方法做的第一件事就是列出资源组中的所有配置文件和终结点，并且，如果它找到了我们的常量中指定的配置文件和终结点名称的匹配项，请进行记录以供日后使用，以致我们不会尝试创建重复项。

```csharp
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
    // List all the CDN profiles in this resource group
    var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
    foreach (Profile p in profileList)
    {
        Console.WriteLine("CDN profile {0}", p.Name);
        if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
        {
            // Hey, that's the name of the CDN profile we want to create!
            profileAlreadyExists = true;
        }

        //List all the CDN endpoints on this CDN profile
        Console.WriteLine("Endpoints:");
        var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
        foreach (Endpoint e in endpointList)
        {
            Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
            if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
            {
                // The unique endpoint name already exists.
                endpointAlreadyExists = true;
            }
        }
        Console.WriteLine();
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>创建 CDN 配置文件和终结点
接下来，我们将创建配置文件。

```csharp
private static void CreateCdnProfile(CdnManagementClient cdn)
{
    if (profileAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating profile {0}.", profileName);
        ProfileCreateParameters profileParms =
            new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
        cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
    }
}
```

创建配置文件后，我们将创建终结点。

```csharp
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
    if (endpointAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
        EndpointCreateParameters endpointParms =
            new EndpointCreateParameters()
            {
                Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
                IsHttpAllowed = true,
                IsHttpsAllowed = true,
                Location = resourceLocation
            };
        cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
    }
}
```

> [!NOTE]
> 上面的示例为终结点分配了名为 *Contoso* 的源，主机名为 `www.contoso.com`。  应该将其更改为指向你自己的源的主机名。
>
>

## <a name="purge-an-endpoint"></a>清除终结点
假设已经创建终结点，我们可能想在程序中执行的一个常见任务是清除终结点中的内容。

```csharp
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
    if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
    {
        Console.WriteLine("Purging endpoint. Please wait...");
        cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

> [!NOTE]
> 在上面的示例中，字符串 `/*` 表示我想清除终结点路径根目录中的所有内容。  这相当于在 Azure 门户的“清除”对话框中勾选“**清除所有**”。 在 `CreateCdnProfile` 方法中，我使用代码 `Sku = new Sku(SkuName.StandardVerizon)` 创建了 **Verizon 中的 Azure CDN** 配置文件。  但是，**Akamai 中的 Azure CDN** 配置文件不支持 **清除所有** 功能，因此如果我在本教程中使用 Akamai 配置文件，则需要包含特定的清除路径。
>
>

## <a name="delete-cdn-profiles-and-endpoints"></a>删除 CDN 配置文件和终结点
最后的方法将删除我们的终结点和配置文件。

```csharp
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
    {
        Console.WriteLine("Deleting endpoint. Please wait...");
        cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
    {
        Console.WriteLine("Deleting profile. Please wait...");
        cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

## <a name="running-the-program"></a>运行程序
我们现在可以通过在 Visual Studio 中单击“**启动**”按钮来编译和运行程序。

![程序运行中](./media/cdn-app-dev-net/cdn-program-running-1.png)

当程序达到上述提示时，应该能够返回到 Azure 门户中的资源组，并看到已经创建了配置文件。

![成功！](./media/cdn-app-dev-net/cdn-success.png)

然后，我们可以确认提示，运行程序的其余步骤。

![程序完成](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>后续步骤
要从本演练中查看完成的项目，请[下载示例](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c)。

要查找与适用于 .NET 的 Azure CDN 管理库有关的其他文档，请查看 [MSDN 参考](/dotnet/api/overview/azure/cdn)。

使用 [PowerShell](cdn-manage-powershell.md) 管理 CDN 资源。
