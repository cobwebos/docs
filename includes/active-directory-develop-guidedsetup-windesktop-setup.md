---
title: include 文件
description: include 文件
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: d407b015699925a6a3ce6ef9108ace1e9900303f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
## <a name="set-up-your-project"></a>设置项目

本部分将创建新项目，用于演示如何将 Windows 桌面 .NET 应用程序 (XAML) 与“使用 Microsoft 登录”集成，使该应用程序能查询需要令牌的 Web API。

使用本指南创建的应用程序将显示一个用于调用图的按钮、一个用于在屏幕上显示结果的区域和一个注销按钮。

> [!NOTE]
> 想要改为下载此示例的 Visual Studio 项目？ [下载一个项目](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip)，并且在执行该项目之前跳到[配置步骤](#register-your-application)来配置代码示例。
>

若要创建应用程序，请执行以下操作：
1. 在 Visual Studio 中，选择“文件” > “新建” > “项目”。
2. 在“模板”下，选择“Visual C#”。
3. 选择“WPF 应用”或“WPF 应用程序”，具体取决于所使用的 Visual Studio 版本。

## <a name="add-msal-to-your-project"></a>将 MSAL 添加到项目
1. 在 Visual Studio 中，选择“工具” > “NuGet 包管理器”> “包管理器控制台”。
2. 在“包管理器控制台”窗口中，粘贴以下 Azure PowerShell 命令：

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > 此命令将安装 Microsoft 身份验证库。 MSAL 负责获取、缓存和刷新用于访问受 Azure Active Directory v2 保护的 API 的用户令牌。
    >

## <a name="add-the-code-to-initialize-msal"></a>添加代码以初始化 MSAL
此步骤将创建用于处理与 MSAL 的交互（例如令牌处理）的类。

1. 打开 *App.xaml.cs* 文件，将 MSAL 的引用添加到类：

    ```csharp
    using Microsoft.Identity.Client;
    ```
<!-- Workaround for Docs conversion bug -->

2. 将 App 类更新为：

    ```csharp
    public partial class App : Application
    {
        //Below is the clientId of your app registration. 
        //You have to replace the below with the Application Id for your app registration
        private static string ClientId = "your_client_id_here";

        public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);

    }
    ```

## <a name="create-the-application-ui"></a>创建应用程序 UI

本部分说明应用程序如何查询受保护的后端服务器，例如 Microsoft Graph。 

项目模板中应自动创建了 *MainWindow.xaml* 文件。 打开此文件，然后将应用程序的 *\<Grid>* 节点替换为以下代码：

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

