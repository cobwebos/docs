---
title: "如何设置计算机以使用 .NET 进行媒体服务开发"
description: "了解使用适用于 .NET 的媒体服务 SDK 进行媒体服务开发所要满足的先决条件。 此外，了解如何创建 Visual Studio 应用程序。"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: 532306427ba13aca70c50d47a33bb7edeac71720
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="media-services-development-with-net"></a>使用 .NET 进行媒体服务开发
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

本文讨论如何使用 .NET 开始开发媒体服务应用程序。

通过 **Azure 媒体服务 .NET SDK** 库，可基于使用 .NET 的媒体服务编程。 为了进一步方便使用 .NET 进行开发，提供了 **Azure 媒体服务 .NET SDK 扩展**库。 此库包含一组扩展方法和帮助器函数，可简化 .NET 代码。 这两个库都通过 **NuGet** 和 **GitHub** 提供。

## <a name="prerequisites"></a>先决条件
* 在新的或现有的 Azure 订阅中拥有一个媒体服务帐户。 请参阅[如何创建媒体服务帐户](media-services-portal-create-account.md)一文。
* 操作系统：Windows 10、Windows 7、Windows 2008 R2 或 Windows 8。
* .NET Framework 4.5 或更高版本。
* Visual Studio。

## <a name="create-and-configure-a-visual-studio-project"></a>创建和配置 Visual Studio 项目
本部分演示如何在 Visual Studio 中创建项目，以及如何将该项目设置为进行媒体服务开发。  在本示例中，该项目为 C# Windows 控制台应用程序，但此处所示的设置步骤同样适用于针对媒体服务应用程序（例如，Windows 窗体应用程序或 ASP.NET Web 应用程序）创建的其他类型的项目。

本部分说明如何使用 **NuGet** 添加媒体服务 .NET SDK 扩展和其他依赖库。

此外，可以从 GitHub（[github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) 或 [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)）获取最新的媒体服务 .NET SDK 资料、生成解决方案并添加对客户端项目的引用。 将自动下载并提取所有必需的依赖项。

1. 在 Visual Studio 中创建新的 C# 控制台应用程序。 输入“名称”、“位置”和“解决方案名称”，并单击“确定”。
2. 生成解决方案。
3. 使用 **NuGet** 安装和添加 **Azure 媒体服务 .NET SDK 扩展** (**windowsazure.mediaservices.extensions**)。 安装此包也会安装 **媒体服务 .NET SDK** 并添加所有其他必需的依赖项。
   
    确保已安装最新版本的 NuGet。 有关详细信息和安装说明，请参阅 [NuGet](http://nuget.codeplex.com/)。

    1. 在“解决方案资源管理器”中，右键单击项目名称，并选择“管理 NuGet 包”。

    2. 此时会显示“管理 NuGet 包”对话框。

    3. 在联机库中，搜索 Azure 媒体服务扩展，选择“Azure 媒体服务.NET SDK 扩展”(windowsazure.mediaservices.extensions)，然后单击“安装”按钮。
   
    4. 此时会修改项目并添加对媒体服务 .NET SDK 扩展、媒体服务 .NET SDK 和其他依赖程序集的引用。
4. 若要升级更干净的开发环境，请考虑启用 NuGet 包还原。 有关详细信息，请参阅 [NuGet 包还原](http://docs.nuget.org/consume/package-restore)。
5. 添加对 **System.Configuration** 程序集的引用。 此程序集包含用于访问配置文件（例如，App.config）的 System.Configuration.**ConfigurationManager** 类。
   
    1. 若要使用“管理引用”对话框添加引用，请在“解决方案资源管理器”中右键单击项目名称。 然后单击“添加”，并单击“引用...”。
   
    2. 此时会显示“管理引用”对话框。
    3. 在 .NET Framework 程序集下，找到并选择 System.Configuration 程序集，并按“确定”。
6. 打开 App.config 文件并将 **appSettings** 节添加到文件。 设置连接到 Media Services API 所需的值。 有关详细信息，请参阅[通过 Azure AD 身份验证访问 Azure 媒体服务 API](media-services-use-aad-auth-to-access-ams-api.md)。 

    使用“服务主体”身份验证方法设置需要连接的值。

        ```csharp
                <configuration>
                ...
                    <appSettings>
                        <add key="AMSAADTenantDomain" value="tenant"/>
                        <add key="AMSRESTAPIEndpoint" value="endpoint"/>
                        <add key="AMSClientId" value="id"/>
                        <add key="AMSClientSecret" value="secret"/>
                    </appSettings>
                </configuration>
        ```

7. 将“System.Configuration”引用添加到项目中。
8. 使用以下代码覆盖位于 Program.cs 文件开头的现有“using”语句：

    ```csharp      
            using System;
            using System.Configuration;
            using System.IO;
            using Microsoft.WindowsAzure.MediaServices.Client;
            using System.Threading;
            using System.Collections.Generic;
            using System.Linq;
    ```

    现在，可以开始开发媒体服务应用程序了。    

## <a name="example"></a>示例

下面是一个小型示例，连接到 AMS API 并列出所有可用的媒体处理器。

```csharp
        class Program
        {
            // Read values from the App.config file.

            private static readonly string _AADTenantDomain =
                ConfigurationManager.AppSettings["AMSAADTenantDomain"];
            private static readonly string _RESTAPIEndpoint =
                ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
            private static readonly string _AMSClientId =
                ConfigurationManager.AppSettings["AMSClientId"];
            private static readonly string _AMSClientSecret =
                ConfigurationManager.AppSettings["AMSClientSecret"];
        
            private static CloudMediaContext _context = null;
            static void Main(string[] args)
            {
                AzureAdTokenCredentials tokenCredentials = 
                    new AzureAdTokenCredentials(_AADTenantDomain,
                        new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                        AzureEnvironments.AzureCloudEnvironment);

                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
        
                // List all available Media Processors
                foreach (var mp in _context.MediaProcessors)
                {
                    Console.WriteLine(mp.Name);
                }
        
            }
 ```

##<a name="next-steps"></a>后续步骤

现在[可以连接到 AMS API](media-services-use-aad-auth-to-access-ams-api.md) 并开始[开发](media-services-dotnet-get-started.md)。


## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

