---
title: "移动应用入门（使用 Xamarin.Forms）"
description: "按本教程操作，开始使用移动应用进行 Xamarin.Forms 开发"
services: app-service\mobile
documentationcenter: xamarin
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: ee12caaad4095cff6dae3282f747ae804f93db81
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="create-a-xamarinforms-app"></a>创建 Xamarin.Forms 应用
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概述
本教程说明如何使用作为后端的 Azure 应用服务的移动应用功能，向 Xamarin.Forms 移动应用添加基于云的后端服务。 请创建一个新的移动应用后端和一个简单的待办事项列表 Xamarin.Forms 应用，此应用将应用数据存储在 Azure 中。

只有在完成本教程后，才可以学习有关 Xamarin.Forms 的所有其他移动应用教程。

## <a name="prerequisites"></a>先决条件
要完成本教程，需要以下各项：

* 有效的 Azure 帐户。 如果没有帐户，可以注册 Azure 试用版并获取多达 10 个免费的移动应用，即使在试用期结束之后仍可继续使用这些应用。 有关详细信息，请参阅 [Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* Visual Studio with Xamarin。 有关信息，请参阅[设置和安装 Visual Studio 和 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) 页。

* 安装了 Xcode v7.0 版或更高版本以及 Xamarin Studio Community 的 Mac。 有关信息，请参阅[设置和安装 Visual Studio 和 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) 以及 [Mac 用户的设置、安装和验证](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN)。

## <a name="create-a-new-mobile-apps-back-end"></a>创建新的移动应用后端

若要创建新的移动应用后端，请执行以下操作：

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

现在已设置可供移动客户端应用程序使用的移动应用后端。 接下来，为简单的待办事项列表后端下载服务器项目，然后将其发布到 Azure。

## <a name="configure-the-server-project"></a>配置服务器项目

若要将服务器项目配置为使用 Node.js 或 .NET 后端，请执行以下操作：

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>下载并运行 Xamarin.Forms 解决方案

可以通过两种方式中的任一方式下载该解决方案。 将解决方案下载到 Mac 并在 Xamarin Studio 中打开它，或者将解决方案下载到 Windows 计算机并使用联网的 Mac 在 Visual Studio 中打开它以生成 iOS 应用。 有关详细信息，请参阅[设置和安装 Visual Studio 和 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx)。

在 Mac 或 Windows 计算机上执行以下操作：

1. 转到 [Azure 门户]。

2. 在移动应用的“设置”边栏选项卡的“移动”下，选择“开始使用” > “Xamarin.Forms”。 在“步骤 3”下选择“创建新应用”，然后选择“下载”。

   此操作下载一个项目，其中包含连接到移动应用的客户端应用程序。 将压缩的项目文件保存到本地计算机，并记下保存位置。

3. 解压缩下载的项目，然后在 Xamarin Studio (Mac) 或 Visual Studio (Windows) 中打开它。

   ![在 Xamarin Studio 中解压缩的项目][9]

   ![在 Visual Studio 中解压缩的项目][8]

## <a name="optional-run-the-ios-project"></a>（可选）运行 iOS 项目
在本部分，请运行适用于 iOS 设备的 Xamarin iOS 项目。 如果不使用 iOS 设备，可以跳过本部分。

#### <a name="in-xamarin-studio"></a>在 Xamarin Studio 中
1. 右键单击 iOS 项目，然后选择“设为启动项目”。

2. 在“运行”菜单上，选择“开始调试”以生成项目，并在 iPhone 模拟器中启动应用。

#### <a name="in-visual-studio"></a>在 Visual Studio 中
1. 右键单击 iOS 项目，然后选择“设为启动项目”。

2. 在“生成”菜单上，选择“配置管理器”。

3. 在“配置管理器”对话框中，选中 iOS 项目旁边的“生成”和“部署”复选框。

4. 若要生成项目并在 iPhone 模拟器中启动应用，请选择 F5 键。

   > [!NOTE]
   > 如果在生成项目时遇到生成问题，请运行 NuGet 包管理器并更新到 Xamarin 支持包的最新版本。 快速入门项目在更新到最新版本时可能较慢。    
   >
   >

5. 在应用中键入有意义的文本（例如“Learn Xamarin”），然后选择加号 (+)。

    ![][10]

    此操作向在 Azure 中托管的新移动应用后端发送 post 请求。 来自请求的数据被插入到 TodoItem 表。 存储在表中的项由移动应用后端返回，数据显示在列表中。

    > [!NOTE]
    > 可在解决方案的可移植类库项目的 TodoItemManager.cs C# 文件中找到用于访问移动应用后端的代码。
    >
    >

## <a name="optional-run-the-android-project"></a>（可选）运行 Android 项目
在本部分，请运行适用于 Android 的 Xamarin droid 项目。 如果不使用 Android 设备，可以跳过本部分。

#### <a name="in-xamarin-studio"></a>在 Xamarin Studio 中

1. 右键单击 Android 项目，然后选择“设为启动项目”。

2. 若要生成项目，并在 Android 模拟器中启动应用，请在“运行”菜单上选择“开始调试”。

#### <a name="in-visual-studio"></a>在 Visual Studio 中

1. 右键单击 Android (Droid) 项目，然后选择“设为启动项目”。

2. 在“生成”菜单上，选择“配置管理器”。

3. 在“配置管理器”对话框中，选中 Android 项目旁边的“生成”和“部署”复选框。

4. 若要生成项目并在 Android 模拟器中启动应用，请选择 F5 键。

   > [!NOTE]
   > 如果在生成项目时遇到生成问题，请运行 NuGet 包管理器并更新到 Xamarin 支持包的最新版本。 快速入门项目在更新到最新版本时可能较慢。    
   >
   >

5. 在应用中键入有意义的文本（例如“Learn Xamarin”），然后选择加号 (+)。

    ![][11]
    
    此操作向在 Azure 中托管的新移动应用后端发送 post 请求。 来自请求的数据被插入到 TodoItem 表。 存储在表中的项由移动应用后端返回，数据显示在列表中。
    
    > [!NOTE]
    > 可在解决方案的可移植类库项目的 TodoItemManager.cs C# 文件中找到用于访问移动应用后端的代码。
    >
    >

## <a name="optional-run-the-windows-project"></a>（可选）运行 Windows 项目

在本部分，请运行适用于 Windows 设备的 Xamarin WinApp 项目。 如果不使用 Windows 设备，可以跳过本部分。

#### <a name="in-visual-studio"></a>在 Visual Studio 中

1. 右键单击任一 Windows 项目，然后选择“设为启动项目”。

2. 在“生成”菜单上，选择“配置管理器”。

3. 在“配置管理器”对话框中，选中所选 Windows 项目旁边的“生成”和“部署”复选框。

4. 若要生成项目并在 Windows 模拟器中启动应用，请选择 F5 键。

   > [!NOTE]
   > 如果在生成项目时遇到生成问题，请运行 NuGet 包管理器并更新到 Xamarin 支持包的最新版本。 快速入门项目在更新到最新版本时可能较慢。    
   >
   >

5. 在应用中键入有意义的文本（例如“Learn Xamarin”），然后选择加号 (+)。

    此操作向在 Azure 中托管的新移动应用后端发送 post 请求。 来自请求的数据被插入到 TodoItem 表。 存储在表中的项由移动应用后端返回，数据显示在列表中。
    
    ![][12]
    
    > [!NOTE]
    > 可在解决方案的可移植类库项目的 TodoItemManager.cs C# 文件中找到用于访问移动应用后端的代码。
    >
    >

## <a name="next-steps"></a>后续步骤

* [向应用添加身份验证](app-service-mobile-xamarin-forms-get-started-users.md)  
  了解如何使用标识提供者对应用的用户进行身份验证。

* [向应用添加推送通知](app-service-mobile-xamarin-forms-get-started-push.md)  
  了解如何为应用添加推送通知支持，以及如何将移动应用后端配置为使用 Azure 通知中心发送推送通知。

* [为应用启用脱机同步](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  了解如何使用移动应用后端向应用添加脱机支持。 使用脱机同步时，即使没有网络连接，也可以查看、添加或修改移动应用的数据。

* [使用移动应用的托管客户端](app-service-mobile-dotnet-how-to-use-client-library.md)  
  了解如何在 Xamarin 应用中使用托管客户端 SDK。

<!-- Anchors. -->
[Get started with Mobile Apps back ends]:#getting-started
[Create a new Mobile Apps back end]:#create-new-service
[Next steps]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure 门户]: https://portal.azure.com/
