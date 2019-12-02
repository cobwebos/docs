---
title: Xamarin 入门
description: 按本教程操作，开始使用移动应用进行 Xamarin.Forms 开发
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 69a9b3294bf080f7ae1516b0acbfc4be077518f4
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668653"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>使用 Azure 创建 Xamarin.Forms 应用

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center 支持以移动应用开发为中心的端到端集成服务。 开发人员可以使用“生成”、“测试”和“分发”服务来设置“持续集成和交付”管道。 部署应用后，开发人员可以使用“分析”和“诊断”服务监视其应用的状态和使用情况，并使用“推送”服务吸引用户。 开发人员还可以利用“身份验证”对其用户进行身份验证，并使用“数据”服务在云中保留和同步应用数据。
>
> 如果希望将云服务集成到移动应用程序中，请立即注册到 [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 中。

## <a name="overview"></a>概述
本教程说明如何使用作为后端的 Azure 应用服务的移动应用功能，向 Xamarin.Forms 移动应用添加基于云的后端服务。 请创建一个新的移动应用后端和一个简单的待办事项列表 Xamarin.Forms 应用，此应用将应用数据存储在 Azure 中。

只有在完成本教程后，才可以学习有关 Xamarin.Forms 的所有其他移动应用教程。

## <a name="prerequisites"></a>必备组件

若要完成本教程，需要以下各项：

* 有效的 Azure 帐户。 如果没有帐户，可以注册 Azure 试用版并获取多达 10 个免费的移动应用，即使在试用期结束之后仍可继续使用这些应用。 有关详细信息，请参阅 [Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* 适用于 Xamarin、Visual Studio 2017 或更高版本中的 Visual Studio Tools 或 Visual Studio for Mac。 有关说明，请参阅[Xamarin 安装页][Install Xamarin]。

* （可选）若要生成 iOS 应用，必须使用装有 Xcode 9.0 或更高版本的 Mac。 Visual Studio for Mac 可用于开发 iOS 应用，或者可以使用 Visual Studio 2017 或更高版本（只要 Mac 在网络上可用）。

## <a name="create-a-new-mobile-apps-back-end"></a>创建新的移动应用后端
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>创建数据库连接并配置客户端和服务器项目
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinforms-solution"></a>运行 Xamarin Forms 解决方案

若要打开解决方案，需要 Visual Studio Tools Xamarin，请参阅[xamarin 安装说明][Install Xamarin]。 如果该工具已安装，请按以下步骤来下载并打开解决方案：

### <a name="visual-studio-windows-and-mac"></a>Visual Studio （Windows 和 Mac）

1. 转到[Azure 门户](https://portal.azure.com/)，导航到所创建的移动应用。 在 "`Overview`" 边栏选项卡上，查找作为你的移动应用程序的公共终结点的 URL。 示例-我的应用名称 "test123" 的 sitename 将 https://test123.azurewebsites.net 。

2. 打开此文件夹中 `Constants.cs` 文件-xamarin. forms/ZUMOAPPNAME。 应用程序名称为 `ZUMOAPPNAME`。

3. 在 `Constants.cs` 类中，将 `ZUMOAPPURL` 变量替换为上面的公共终结点。

    `public static string ApplicationURL = @"ZUMOAPPURL";`

    会

    `public static string ApplicationURL = @"https://test123.azurewebsites.net";`
    
4. 按照下面的说明运行 Android 或 Windows 项目；如果有联网的 Mac 计算机可用，则运行 iOS 项目。

## <a name="optional-run-the-android-project"></a>（可选）运行 Android 项目

在本部分，请运行 Xamarin.Android 项目。 如果不使用 Android 设备，可以跳过本部分。

### <a name="visual-studio"></a>Visual Studio

1. 右键单击 Android (Droid) 项目，然后选择“设为启动项目”。

2. 在“生成”菜单上，选择“配置管理器”。

3. 在“配置管理器”对话框中，选中 Android 项目旁边的“生成”和“部署”复选框，并确保共享的代码项目已将“生成”框选中。

4. 若要生成项目并在 Android 模拟器中启动应用，请按 **F5** 键或单击“启动”按钮。

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. 右键单击 Android 项目，然后选择“设为启动项目”。

2. 若要生成项目，并在 Android 模拟器中启动应用，请选择“运行”菜单，然后选择“开始调试”。

在应用中键入有意义的文本（例如“Learn Xamarin”），然后选择加号 (+)。

![Android 待办事项应用][11]

此操作向在 Azure 中托管的新移动应用后端发送 post 请求。 请求的数据将插入 TodoItem 表。 存储在表中的项由移动应用后端返回，数据显示在列表中。

> [!NOTE]
> 用于访问移动应用后端的代码位于解决方案的共享代码项目的 **TodoItemManager.cs** C# 文件中。
>

## <a name="optional-run-the-ios-project"></a>（可选）运行 iOS 项目

在本部分，请运行适用于 iOS 设备的 Xamarin iOS 项目。 如果不使用 iOS 设备，可以跳过本部分。

### <a name="visual-studio"></a>Visual Studio

1. 右键单击 iOS 项目，然后选择“设为启动项目”。

2. 在“生成”菜单上，选择“配置管理器”。

3. 在“配置管理器”对话框中，选中 iOS 项目旁边的“生成”和“部署”复选框，并确保共享的代码项目已将“生成”框选中。

4. 若要生成项目并在 iPhone 模拟器中启动应用，请选择 F5 键。

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. 右键单击 iOS 项目，然后选择“设为启动项目”。

2. 在“运行”菜单上，选择“开始调试”以生成项目，并在 iPhone 模拟器中启动应用。

在应用中键入有意义的文本（例如“Learn Xamarin”），然后选择加号 (+)。

![iOS 待办事项应用][10]

此操作向在 Azure 中托管的新移动应用后端发送 post 请求。 请求的数据将插入 TodoItem 表。 存储在表中的项由移动应用后端返回，数据显示在列表中。

> [!NOTE]
> 你会发现，用于访问移动应用后端的代码位于解决方案的共享代码项目的 **TodoItemManager.cs** C# 文件中。
>

## <a name="optional-run-the-windows-project"></a>（可选）运行 Windows 项目

在本部分，请运行适用于 Windows 设备的 Xamarin.Forms 通用 Windows 平台 (UWP) 项目。 如果不使用 Windows 设备，可以跳过本部分。

### <a name="visual-studio"></a>Visual Studio

1. 右键单击任意 UWP 项目，然后选择“设为启动项目”。

2. 在“生成”菜单上，选择“配置管理器”。

3. 在“配置管理器”对话框中，选中所选 Windows 项目旁边的“生成”和“部署”复选框，并确保共享的代码项目已将“生成”框选中。

4. 若要生成项目并在 Windows 模拟器中启动应用，请按 **F5** 键或单击“启动”按钮（应该显示为“本地计算机”）。

> [!NOTE]
> Windows 项目不能在 macOS 上运行。

在应用中键入有意义的文本（例如“Learn Xamarin”），然后选择加号 (+)。

此操作向在 Azure 中托管的新移动应用后端发送 post 请求。 请求的数据将插入 TodoItem 表。 存储在表中的项由移动应用后端返回，数据显示在列表中。

![UWP 待办事项应用][12]

> [!NOTE]
> 可在解决方案的可移植类库项目的 **TodoItemManager.cs** C# 文件中找到用于访问移动应用后端的代码。
>

## <a name="troubleshooting"></a>故障排除

如果在生成解决方案时遇到问题，请运行 NuGet 包管理器并更新到 `Xamarin.Forms` 的最新版本，然后在 Android 项目中更新 `Xamarin.Android` 支持包。 快速入门项目并非始终包含最新版本。

请注意，在 Android 项目中引用的所有支持包必须都具有相同的版本。 对于 Android 平台，[Azure 移动应用 NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)具有 `Xamarin.Android.Support.CustomTabs` 依赖项，因此，如果你的项目使用较新的支持包，则你需要直接安装具有所需版本的此包以避免冲突。

<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png

<!-- URLs. -->
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/
