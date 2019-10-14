---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: erhopf
ms.openlocfilehash: 0e4e67710c98b80dce2b0d55a86869625f3942d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71837486"
---
若要创建 Visual Studio 项目，以便使用 Xamarin 进行跨平台移动应用 .NET 开发，需要设置 Visual Studio 开发选项，创建项目，选择目标体系结构，然后安装语音 SDK。

### <a name="set-up-visual-studio-development-options"></a>设置 Visual Studio 开发选项

若要开始，请确保已在 Visual Studio 中正确进行设置，以便使用 .NET 进行跨平台移动开发：

1. 打开 Visual Studio 2019。

1. 在 Visual Studio 菜单栏中，选择“工具”   > “获取工具和功能”  以打开 Visual Studio 安装程序并查看“修改”  对话框。

   ![“工作负载”选项卡，“修改”对话框，Visual Studio 安装程序](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. 在“工作负荷”  选项卡的“Windows”  下，找到“使用 .NET 的移动开发”工作负荷  。 如果已选中该工作负荷旁的复选框，请关闭“修改”  对话框，然后转到步骤 5。

1. 选中“使用 .NET 的移动开发”  复选框，选择“修改”  ，然后在“在开始之前”  对话框中，选择“继续”  以安装使用 .NET 的移动开发工作负荷。 安装新功能可能花费一些时间。

1. 关闭 Visual Studio 安装程序。

### <a name="create-the-project"></a>创建项目

1. 在 Visual Studio 菜单栏中，选择“文件”   > “新建”   > “项目”  以显示“创建新项目”  窗口。

   ![创建新项目 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. 找到并选择“移动应用(Xamarin Forms)”  。

1. 选择“下一步”  以显示“配置新项目”  屏幕。 

   ![配置新项目 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. 在“项目名称”  中输入 `helloworld`。

1. 在“位置”  中，导航到并选择或创建用于保存项目的文件夹。

1. 选择“创建”  以转到“新建移动应用 Xamarin Forms 项目”  窗口。

   ![“新建通用 Windows 平台项目”对话框 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. 选择“空白”模板 

1. 在“平台”中，  勾选 **Android**、**iOS** 和 **Windows (UWP)** 对应的框。

1. 选择“确定”  。 返回到 Visual Studio IDE，其中新项目已创建并显示在“解决方案资源管理器  ”窗格中。

   ![helloworld 项目 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

现在选择目标平台体系结构和启动项目。 在 Visual Studio 工具栏中，找到“解决方案平台”  下拉框。 （如果找不到，请选择“查看”   > “工具栏”   > “标准”  以显示包含“解决方案平台”的工具栏  。）如果运行的是 64 位 Windows，请在下拉框中选择“x64”  。 64 位 Windows 也可以运行 32 位应用程序，因此可以根据自己的偏好选择“x86”  。 对于“启动项目”下拉框，  请设置 helloworld.UWP（通用 Windows）。

### <a name="install-the-speech-sdk"></a>安装语音 SDK

安装[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)，并在项目中引用语音 SDK：

1. 在“解决方案资源管理器”  中，右键单击你的解决方案，然后选择“管理解决方案的 NuGet 包”  以转到“NuGet - 解决方案”  窗口。

1. 选择“浏览”  。

   ![“管理解决方案包”对话框的屏幕截图](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. 在“包源”  中，选择“nuget.org”  。

1. 在“搜索”  框中，输入 `Microsoft.CognitiveServices.Speech`，然后在该包显示在搜索结果中之后选择该包。

   ![“管理解决方案包”对话框的屏幕截图](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)
   > 注意：Microsoft.CognitiveServices.Speech nuget 中的 iOS 库未启用 Bitcode。 如果需要适合应用程序并启用了 Bitcode 的库，请使用专用于 iOS 项目的 Microsoft.CognitiveServices.Speech.Xamarin.iOS nuget。

1. 在搜索结果旁的“包状态”窗格中，选择所有项目。 **helloworld**、**helloworld.Android**、**helloworld.iOS** 和 **helloworld.UWP**。

1. 选择“安装”  。

1. 在“预览更改”  对话框中，选择“确定”  。

1. 在“接受许可证”  对话框中查看许可证，然后选择“我接受”，安装针对所有项目的语音 SDK 包引用  。 安装成功完成后，可能会看到下述针对 helloworld.iOS 的警告。 这是已知问题，不会影响应用功能。

> 不能解析引用“C:\Users\Default\.nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a”。 如果此引用是代码所需的，则可能会出现编译错误。
