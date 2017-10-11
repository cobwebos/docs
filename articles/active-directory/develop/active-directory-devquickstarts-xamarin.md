---
title: "Azure AD Xamarin 入门 |Microsoft 文档"
description: "构建 Xamarin 应用程序将与登录的 Azure AD 集成和调用使用 OAuth 的 Azure AD 保护 Api。"
services: active-directory
documentationcenter: xamarin
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 54ee403f283bc5dc79911e2e813dd513ff595828
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="integrate-azure-ad-with-xamarin-apps"></a>将 Azure AD 集成适用于 Xamarin 应用
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

借助 Xamarin，你可以编写移动应用，在 C# 中，可以在 iOS、 Android 和 Windows （移动设备和电脑） 上运行。 如果你正在生成应用程序使用 Xamarin，Azure Active Directory (Azure AD) 非常容易地使用其 Azure AD 帐户的用户进行身份验证。 应用程序可以还安全地使用由 Azure AD 中，例如 Office 365 Api 或 Azure API 来保护任何 web API。

对于需要访问受保护的资源的 Xamarin 应用程序，Azure AD 提供 Active Directory 身份验证库 (ADAL)。 ADAL 的唯一目的是方便应用程序，以便获取访问令牌。 为了演示是多么容易，本文将说明如何构建 DirectorySearcher 应用程序：

* 在 iOS、 Android、 Windows 桌面、 Windows Phone 和 Windows 应用商店上运行。
* 使用单个可移植类库 (PCL) 用户进行身份验证并获取 Azure AD Graph api 的令牌。
* 搜索具有给定 UPN 的用户的目录。

## <a name="before-you-get-started"></a>开始之前
* 下载[主干项目](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip)，或下载[已完成的示例](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip)。 每个下载是一个 Visual Studio 2013 解决方案。
* 你还需要在其中创建用户和注册应用程序的 Azure AD 租户。 如果你还没有租户，[了解如何获取租户](active-directory-howto-tenant.md)。

当你准备好，请按照以下 4 个部分中的过程。

## <a name="step-1-set-up-your-xamarin-development-environment"></a>步骤 1： 设置 Xamarin 开发环境
由于本教程包括以下项目类型提供的 iOS、 Android 和 Windows，你将需要 Visual Studio 和 Xamarin。 若要创建必要的环境，完成中的过程[设置并安装 Visual Studio 和 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) MSDN 上。 说明包括你可以查阅的用来了解有关 Xamarin 的详细信息，正在等待安装完成时的材料。

已完成安装程序后，请在 Visual Studio 中打开解决方案。 存在，将看到六个项目： 五个特定于平台的项目和一个 PCL 中，DirectorySearcher.cs，将在所有平台之间共享。

## <a name="step-2-register-the-directorysearcher-app"></a>步骤 2： 注册 DirectorySearcher 应用程序
若要启用应用程序以获取令牌，首先需要在你的 Azure AD 租户中注册并授予它访问 Azure AD Graph API 的权限。 操作方法如下：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在顶部栏上，单击你的帐户。 然后，在**目录**列表中，选择你想要将应用程序注册的 Active Directory 租户。
3. 单击**更服务**在左窗格中，，然后选择**Azure Active Directory**。
4. 单击**应用程序注册**，然后选择**添加**。
5. 若要创建一个新**本机客户端应用程序**，按照提示进行操作。
  * **名称**描述用户应用程序。
  * **重定向 URI**是 Azure AD 使用来返回令牌响应的方案与字符串组合。 输入的值 (例如，http://DirectorySearcher)。
6. 已完成注册后，Azure AD 会为分配应用程序是唯一的应用程序 id。 将复制从值**应用程序**选项卡上，因为稍后您将需要它。
7. 上**设置**页上，选择**所需的权限**，然后选择**添加**。
8. 选择**Microsoft Graph**作为 API。 下**委派权限**，添加**读取目录数据**权限。  
此操作使应用程序查询 Graph API 的用户。

## <a name="step-3-install-and-configure-adal"></a>步骤 3： 安装并配置 ADAL
现在，你在 Azure AD 中拥有应用，你可以安装 ADAL 并编写标识相关的代码。 若要启用 ADAL 与 Azure AD 通信，为其提供一些有关应用程序注册信息。

1. 使用 Package Manager Console 将 ADAL 添加到 DirectorySearcher 项目。

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
    `

    请注意两个库引用将添加到每个项目： ADAL 和特定于平台的一部分的 PCL 部分。
2. 在 DirectorySearcherLib 项目中，打开 DirectorySearcher.cs。
3. 类成员值替换为你在 Azure 门户中输入的值。 只要使用 ADAL，你的代码引用对这些值。

  * *租户*是 Azure AD 租户 (例如，contoso.onmicrosoft.com) 的域。
  * *ClientId*是从门户复制的应用的客户端 ID。
  * *ReturnUri*是重定向在门户 (例如，http://DirectorySearcher) 中输入的 URI。

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>步骤 4： 使用 ADAL 从 Azure AD 获取令牌
几乎所有应用程序的身份验证逻辑在于`DirectorySearcher.SearchByAlias(...)`。 只需要特定于平台的项目中是将传递到上下文参数`DirectorySearcher`PCL。

1. 打开 DirectorySearcher.cs，，然后添加一个新参数`SearchByAlias(...)`方法。 `IPlatformParameters`是上下文参数，用于封装 ADAL 需要对其执行身份验证的特定于平台的对象。

    ```C#
    public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
    {
    ```

2. 初始化`AuthenticationContext`，这是 ADAL 的主类。  
此操作将传递 ADAL 与 Azure AD 通信所需要的坐标。
3. 调用`AcquireTokenAsync(...)`，这样便可以接受`IPlatformParameters`对象并调用需要向应用程序返回令牌的身份验证流。

    ```C#
    ...
        AuthenticationResult authResult = null;
        try
        {
            AuthenticationContext authContext = new AuthenticationContext(authority);
            authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
        }
        catch (Exception ee)
        {
            results.Add(new User { error = ee.Message });
            return results;
        }
    ...
    ```

    `AcquireTokenAsync(...)`首先尝试返回请求的资源 (在本例中为 Graph API) 的令牌，而不提示用户输入其凭据 （通过缓存或刷新旧令牌）。 根据需要，它向用户显示 Azure AD 在登录页面才会获取请求的令牌。
4. 将访问令牌附加到中的 Graph API 请求**授权**标头：

    ```C#
    ...
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
    ...
    ```

这就是所有的`DirectorySearcher`PCL 和应用程序的标识相关代码。 剩下的就是调用`SearchByAlias(...)`在每个平台的视图中的方法并在必要时，将添加代码来正确处理 UI 生命周期。

### <a name="android"></a>Android
1. 在 MainActivity.cs，添加对的调用`SearchByAlias(...)`在按钮单击处理程序：

    ```C#
    List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
    ```
2. 重写`OnActivityResult`生命周期方法，以转发任何身份验证重定向回相应的方法。 ADAL 在 Android 中为此提供一个帮助器方法：

    ```C#
    ...
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ...
    ```

### <a name="windows-desktop"></a>Windows 桌面
在 MainWindow.xaml.cs，请调用`SearchByAlias(...)`通过传递`WindowInteropHelper`在桌面的`PlatformParameters`对象：

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="ios"></a>iOS
在 DirSearchClient_iOSViewController.cs，iOS`PlatformParameters`对象将视图控制器的引用：

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

### <a name="windows-universal"></a>Windows 通用
在 Windows 通用，打开 MainPage.xaml.cs 中，然后实现`Search`方法。 此方法使用共享项目中的一个帮助器方法来根据需要更新 UI。

```C#
...
List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

## <a name="whats-next"></a>后续步骤
你现在拥有有效的 Xamarin 应用程序可以对用户进行身份验证和安全地通过使用 OAuth 2.0 在五个不同的平台调用 web Api。

如果你尚未尚未填充你的租户的用户，现在是进行这些操作的时间。

1. 运行你的 DirectorySearcher 应用程序，然后使用用户之一进行登录。
2. 根据其 UPN 其他用户的搜索。

使用 ADAL 可以轻松地将常见标识功能合并到应用程序。 它负责所有的繁琐工作，如缓存管理、 OAuth 协议支持、 向用户显示登录 UI，并刷新过期的令牌。 你需要知道仅一次的 API 调用， `authContext.AcquireToken*(…)`。

作为参考，下载[已完成的示例](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip)（而无需你的配置值）。

现在，你可以转到其他标识方案。 例如，尝试[保护.NET Web API 与 Azure AD](active-directory-devquickstarts-webapi-dotnet.md)。

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
