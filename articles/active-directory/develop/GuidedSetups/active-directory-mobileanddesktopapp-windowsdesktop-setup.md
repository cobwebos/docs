---
title: "Azure AD v2 Windows 桌面入门 - 安装 | Microsoft Docs"
description: "Windows 桌面 .NET (XAML) 应用程序如何通过 Azure Active Directory v2 终结点调用需要访问令牌的 API"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 4065727aef04d7969d438c6ef79127bb44568be1
ms.contentlocale: zh-cn


---

## <a name="set-up-your-project"></a>设置项目

本部分提供分步说明，介绍如何创建新项目，用于演示如何将 Windows 桌面 .NET 应用程序 (XAML) 与“登录 Microsoft”集成，使其能查询需要令牌的 Web API。

本指南创建的应用程序将公开一个用于在屏幕上绘制和显示结果的按钮，以及一个注销按钮。

> 想要改为下载此示例的 Visual Studio 项目？ [下载项目](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip)并跳到[配置步骤](#create-an-application-express)，在执行前配置代码示例。


### <a name="create-your-application"></a>创建应用程序
1. 在 Visual Studio 中：`File` > `New` > `Project`<br/>
2. 在“模板”下，选择 `Visual C#`
3. 选择 `WPF App`（根据所用 Visual Studio 版本，该选项可能显示为“WPF 应用程序”）

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>向项目添加 Microsoft 身份验证库 (MSAL)
1. 在 Visual Studio 中：`Tools` > `Nuget Package Manager` > `Package Manager Console`
2. 在“程序包管理器控制台”窗口中复制/粘贴以下命令：

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

> 以上包将安装 Microsoft 身份验证库 (MSAL)。 MSAL 负责获取、缓存和刷新用于访问受 Azure Active Directory v2 保护的 API 的用户令牌。

## <a name="add-the-code-to-initialize-msal"></a>添加代码以初始化 MSAL
此步骤将帮助你创建用于处理与 MSAL 库的交互（例如令牌处理）的类。

1. 打开 `App.xaml.cs` 文件，将 MSAL 库的引用添加到类：

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
将 App 类更新为：
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

## <a name="create-your-applications-ui"></a>创建应用程序 UI
下一节说明应用程序可如何查询受保护的后端服务器，例如 Microsoft Graph。 项目模板中应自动创建了 MainWindow.xaml 文件。 打开此文件，然后按照下面的说明进行操作：

将应用程序的 `<Grid>` 替换为以下内容：

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

