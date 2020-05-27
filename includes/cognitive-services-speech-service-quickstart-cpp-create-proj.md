---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: e3ee175e521fe37c99fcb3650ce7480f3f503a08
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673265"
---
若要创建 Visual Studio 项目以用于 C++ 桌面开发，需要设置 Visual Studio 开发选项，创建项目，选择目标体系结构，然后安装语音 SDK。

### <a name="set-up-visual-studio-development-options"></a>设置 Visual Studio 开发选项

若要开始，请确保已在 Visual Studio 中正确设置以用于 C++ 桌面开发：

1. 打开 Visual Studio 2019 以显示“开始”  窗口。

   ![启动窗口 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-start-window.png)

1. 选择“继续但无需代码”  ，转到 Visual Studio IDE。

1. 在 Visual Studio 菜单栏中，选择“工具”   > “获取工具和功能”  以打开 Visual Studio 安装程序并查看“修改”  对话框。

   ![“工作负载”选项卡，“修改”对话框，Visual Studio 安装程序](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

1. 在“工作负载”  选项卡的“Windows”  下，找到“使用 C++ 的桌面开发”工作负载  。 如果尚未选择该工作负载旁边的复选框，请选中。

1. 在“单个组件”  选项卡中，找到“Nuget 包管理器”  复选框。 如果尚未选中此复选框，请选中。

1. 选择角上的标签为“关闭”  或“修改”  的按钮。 （按钮名称会有所不同，具体取决于你是否选择了任何功能以进行安装。）如果选择“修改”  ，将开始安装，这可能需要一段时间。

1. 关闭 Visual Studio 安装程序。

### <a name="create-the-project-and-select-the-target-architecture"></a>创建项目并选择目标体系结构

接下来，创建项目：

1. 在 Visual Studio 菜单栏中，选择“文件”   > “新建”   > “项目”  以显示“创建新项目”  窗口。

   ![创建新项目 C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. 找到“控制台应用”并将其选中  。 确保选择此项目类型的 C++ 版本（而不是 C# 或 Visual Basic）。

1. 选择“下一步”  以显示“配置新项目”  屏幕。

   ![配置 Visual Studio 项目 C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-configure-your-new-project.png)

1. 在“项目名称”  中输入 `helloworld`。

1. 在“位置”  中，导航到并选择或创建用于保存项目的文件夹。

现在选择目标平台体系结构。 在 Visual Studio 工具栏中，找到“解决方案平台”  下拉框。 （如果找不到，请选择“查看”   > “工具栏”   > “标准”  以显示包含“解决方案平台”的工具栏  。）如果运行的是 64 位 Windows，请在下拉框中选择“x64”  。 64 位 Windows 也可以运行 32 位应用程序，因此可以根据自己的偏好选择“x86”  。

### <a name="install-the-speech-sdk"></a>安装语音 SDK

最后，安装[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)，并在项目中引用语音 SDK：

1. 在“解决方案资源管理器”  中，右键单击你的解决方案，然后选择“管理解决方案的 NuGet 包”  以转到“Nuget - 解决方案”  窗口。

1. 选择“浏览”  。

   ![NuGet - “解决方案”选项卡，Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. 在“包源”  中，选择“nuget.org”  。

1. 在“搜索”  框中，输入 `Microsoft.CognitiveServices.Speech`，然后在该包显示在搜索结果中之后选择该包。

   ![安装 Microsoft.CognitiveServices.Speech C++ 包 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

1. 在搜索结果旁的“包状态”窗格中，选择“helloworld”  项目。

1. 选择“安装”  。

1. 在“预览更改”  对话框中，选择“确定”  。

1. 在“接受许可证”  对话框中，查看许可证，然后选择“我接受”  。 包安装开始，安装完成后，“输出”  窗格将显示类似于以下文本的消息：`Successfully installed 'Microsoft.CognitiveServices.Speech 1.12.0' to helloworld`。
