---
title: "Azure AD v2 Windows 桌面获取已启动 – 设置 |Microsoft 文档"
description: "如何 Windows 桌面.NET (XAML) 应用程序可以调用一个 API，由 Azure Active Directory v2 终结点需要访问令牌"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 4065727aef04d7969d438c6ef79127bb44568be1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
## <a name="set-up-your-project"></a>设置你的项目

本部分提供有关如何创建新项目的分步说明来演示如何将 Windows 桌面.NET 应用程序 (XAML) 集成*在使用 Microsoft 登录*，它可以查询需要使用令牌的 Web Api。

本指南所创建的应用程序公开用于图形，并在屏幕和注销按钮上显示结果的按钮。

> 希望改为下载此示例的 Visual Studio 项目？ [下载的项目](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip)并跳到[配置步骤](#create-an-application-express)配置在执行之前的代码示例。


### <a name="create-your-application"></a>创建你的应用程序
1. 在 Visual Studio 中：`File` > `New` > `Project`<br/>
2. 下*模板*，选择`Visual C#`
3. 选择`WPF App`(或*WPF 应用程序*具体取决于你的 Visual Studio 的版本)

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>将 Microsoft 身份验证库 (MSAL) 添加到你的项目
1. 在 Visual Studio 中：`Tools` > `Nuget Package Manager` > `Package Manager Console`
2. 复制/粘贴程序包管理器控制台窗口中的以下：

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

> 上述程序包将安装 Microsoft 身份验证库 (MSAL)。 MSAL 处理获取、 缓存和刷新用户 toskens 用于访问受 Azure Active Directory v2 的 Api。

## <a name="add-the-code-to-initialize-msal"></a>添加代码以初始化 MSAL
此步骤将帮助你创建一个类以处理与 MSAL 库交互，如对令牌的处理。

1. 打开`App.xaml.cs`文件并将 MSAL 库的引用添加到类：

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
更新为以下 App 类：
</li>
</ol>

```csharp
public partial class App : Application
{
    //Below is the clientId of your app registration. 
    //You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";

    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);

}
```

## <a name="create-your-applications-ui"></a>创建你的应用程序 UI
下面的部分说明如何应用程序可以查询受保护后端服务器，例如，Microsoft Graph。 MainWindow.xaml 文件自动应作为你的项目模板的一部分创建。 打开此文件的此文件，然后执行下面的说明：

将你的应用程序`<Grid>`替换为以下：

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```
