---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: 23c4e18ae3e4111c179c1b98320af8c5659fa10c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673095"
---
若要创建 Visual Studio 项目以用于通用 Windows 平台 (UWP) 开发，需要设置 Visual Studio 开发选项，创建项目，选择目标体系结构，设置音频捕获，然后安装语音 SDK。

### <a name="set-up-visual-studio-development-options"></a>设置 Visual Studio 开发选项

若要开始，请确保已在 Visual Studio 中正确设置以用于 UWP 开发：

1. 打开 Visual Studio 2019 以显示“开始”  窗口。

   ![启动窗口 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. 选择“继续但无需代码”  ，转到 Visual Studio IDE。

1. 在 Visual Studio 菜单栏中，选择“工具”   > “获取工具和功能”  以打开 Visual Studio 安装程序并查看“修改”  对话框。

   ![“工作负载”选项卡，“修改”对话框，Visual Studio 安装程序](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. 在“工作负载”  选项卡的“Windows”  下，找到“通用 Windows 平台开发”  工作负载。 如果已选中该工作负载旁的复选框，请关闭“修改”  对话框，然后转到步骤 6。

1. 选中“通用 Windows 平台开发”  复选框，选择“修改”  ，然后在“在开始之前”  对话框中，选择“继续”  以安装 UWP 开发工作负载。 安装新功能可能花费一些时间。

1. 关闭 Visual Studio 安装程序。

### <a name="create-the-project-and-select-the-target-architecture"></a>创建项目并选择目标体系结构

接下来，创建项目：

1. 在 Visual Studio 菜单栏中，选择“文件”   > “新建”   > “项目”  以显示“创建新项目”  窗口。

   ![创建新项目 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. 查找并选择“空白应用(通用 Windows)”。  确保选择此项目类型的 C# 版本（而不是 Visual Basic）。

1. 选择“下一步”  以显示“配置新项目”  屏幕。

   ![配置新项目 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. 在“项目名称”  中输入 `helloworld`。

1. 在“位置”  中，导航到并选择或创建用于保存项目的文件夹。

1. 选择“创建”  以转到“新建通用 Windows 平台项目”  窗口。

   ![“新建通用 Windows 平台项目”对话框 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. 在“最低版本”  （第二个下拉框）中，选择“Windows 10 Fall Creators Update (10.0；内部版本 16299)”  ，这是语音 SDK 的最低要求。

1. 在“目标版本”  （第一个下拉框）中，选择与“最低版本”  中的值相等或更高的值。

1. 选择“确定”  。 返回到 Visual Studio IDE，其中新项目已创建并显示在“解决方案资源管理器  ”窗格中。

   ![helloworld 项目 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

现在选择目标平台体系结构。 在 Visual Studio 工具栏中，找到“解决方案平台”  下拉框。 （如果找不到，请选择“查看”   > “工具栏”   > “标准”  以显示包含“解决方案平台”的工具栏  。）如果运行的是 64 位 Windows，请在下拉框中选择“x64”  。 64 位 Windows 也可以运行 32 位应用程序，因此可以根据自己的偏好选择“x86”  。

> [!NOTE]
> 语音 SDK 仅支持 Intel 兼容的处理器。 目前不支持 ARM 处理器。

### <a name="set-up-audio-capture"></a>设置音频捕获

然后允许项目捕获音频输入：

1. 在“解决方案资源管理器”  中，双击“Package.appxmanifest”  ，以打开包应用程序清单。

1. 选择“功能”  选项卡。

   ![“功能”选项卡，包应用程序清单 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. 选中“麦克风”  功能对应的框。

1. 在菜单栏中，选择“文件”   > “保存 Package.appxmanifest”  以保存所做的更改。

### <a name="install-the-speech-sdk"></a>安装语音 SDK

最后，安装[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)，并在项目中引用语音 SDK：

1. 在“解决方案资源管理器”  中，右键单击你的解决方案，然后选择“管理解决方案的 NuGet 包”  以转到“NuGet - 解决方案”  窗口。

1. 选择“浏览”  。

   ![“管理解决方案包”对话框的屏幕截图](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. 在“包源”  中，选择“nuget.org”  。

1. 在“搜索”  框中，输入 `Microsoft.CognitiveServices.Speech`，然后在该包显示在搜索结果中之后选择该包。

   ![“管理解决方案包”对话框的屏幕截图](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. 在搜索结果旁的“包状态”窗格中，选择“helloworld”  项目。

1. 选择“安装”  。

1. 在“预览更改”  对话框中，选择“确定”  。

1. 在“接受许可证”  对话框中，查看许可证，然后选择“我接受”  。 包安装开始，安装完成后，“输出”  窗格将显示类似于以下文本的消息：`Successfully installed 'Microsoft.CognitiveServices.Speech 1.12.0' to helloworld`。
