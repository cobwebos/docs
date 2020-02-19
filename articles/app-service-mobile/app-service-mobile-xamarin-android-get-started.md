---
title: Xamarin Android 应用入门
description: 按照本教程开始使用 Azure 移动应用进行 Xamarin Android 开发。
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: b42205436c88f9075423bfcaf9e5a9fd931ee4f4
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461362"
---
# <a name="create-a-xamarinandroid-app"></a>创建 Xamarin.Android 应用
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概述
本教程说明如何向 Xamarin.Android 应用添加基于云的后端服务。 有关详细信息，请参阅 [什么是移动应用](app-service-mobile-value-prop.md)。

以下是完成的应用程序的屏幕快照：

![][0]

只有在完成本教程后，才可以学习有关 Xamarin Android 应用的所有其他移动应用教程。

## <a name="prerequisites"></a>必备条件
若要完成本教程，需要具备以下先决条件：

* 有效的 Azure 帐户。 如果没有帐户，可以注册 Azure 试用版并获取多达 10 个免费的移动应用。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。
* Visual Studio with Xamarin。 有关说明，请参阅 [设置和安装 Visual Studio 和 Xamarin](/visualstudio/cross-platform/setup-and-install) 。

## <a name="create-an-azure-mobile-app-backend"></a>创建 Azure 移动应用后端
按照这些步骤创建移动应用后端。

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

现已预配可供移动客户端应用程序使用的 Azure 移动应用后端。 接下来，为简单的“待办事项列表”后端下载服务器项目并将其发布到 Azure。

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>创建数据库连接并配置客户端和服务器项目
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinandroid-app"></a>运行 Xamarin Android 应用
1. 打开 Xamarin Android 项目。

2. 转到[Azure 门户](https://portal.azure.com/)，导航到所创建的移动应用。 在 "`Overview`" 边栏选项卡上，查找作为你的移动应用程序的公共终结点的 URL。 示例-我的应用名称 "test123" 的 sitename 将 https://test123.azurewebsites.net。

3. 打开此文件夹中 `ToDoActivity.cs` 文件-xamarin/ZUMOAPPNAME/ToDoActivity。 应用程序名称为 `ZUMOAPPNAME`。

4. 在 `ToDoActivity` 类中，将 `ZUMOAPPURL` 变量替换为上面的公共终结点。

    `const string applicationURL = @"ZUMOAPPURL";`

    变成
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. 按 F5 键部署并运行应用。

6. 在应用中键入有意义的文本（例如“完成教程”），并单击“添加”图标。

    ![][10]

    来自请求的数据插入到 TodoItem 表。 移动应用后端返回存储在表中的项，数据显示在列表中。

   > [!NOTE]
   > 可以查看访问移动应用后端以查询和插入数据的代码，这些代码在 ToDoActivity.cs C# 文件中。
   
## <a name="troubleshooting"></a>故障排除
如果在生成解决方案时遇到问题，请运行 NuGet 包管理器并更新 `Xamarin.Android` 支持包。 快速入门项目并非始终包含最新版本。

请注意，在项目中引用的所有支持包必须都具有相同的版本。 对于 Android 平台，[Azure 移动应用 NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)具有 `Xamarin.Android.Support.CustomTabs` 依赖项，因此，如果你的项目使用较新的支持包，则你需要直接安装具有所需版本的此包以避免冲突。

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png
<!-- URLs. -->
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
