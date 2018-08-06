---
title: 快速入门：使用认知服务语音 SDK 在 Windows 桌面上的 C++ 中识别语音
titleSuffix: Microsoft Cognitive Services
description: 了解如何使用认知服务语音 SDK 在 Windows 桌面上的 C++ 中识别语音
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 7c93f9f450cc2d244a0d716401171cde02abf70b
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324565"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-desktop-using-the-speech-sdk"></a>快速入门：使用语音 SDK 在 Windows 桌面上的 C++ 中识别语音

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

介绍了如何为 Windows 桌面创建使用语音 SDK 且基于 C++-的控制台应用程序。
该应用程序基于 [Microsoft 认知服务语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)和 Microsoft Visual Studio 2017。

## <a name="prerequisites"></a>先决条件

* 语音服务的订阅密钥。 请参阅[免费试用语音服务](get-started.md)。
* 配有工作麦克风的 Windows 电脑。
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/)、Community Edition 或更高版本。
* Visual Studio 中的“使用 C++ 的桌面开发”工作负载，以及 Visual Studio 中的“NuGet包管理器”组件。
  可分别在“工作负载”和“个别组件”选项卡下的“工具”\>“获取工具和功能”中同时启用它们：

  ![启用“使用 C++ 的桌面开发”工作负载](media/sdk/vs-enable-cpp-workload.png)

  ![在 Visual Studio 中启用 NuGet 包管理器 ](media/sdk/vs-enable-nuget-package-manager.png)

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

在 Visual Studio 2017 中，新建一个 Visual C++ Windows 桌面 Windows 控制台应用程序。 在“新建项目”对话框的左窗格中，展开“已安装”\>“Visual C++”\>“Windows 桌面”，然后选择“Windows控制台应用程序”。 在项目名称处，输入 helloworld。

![创建 Visual C++ Windows 桌面 Windows 控制台应用程序](media/sdk/qs-cpp-windows-01-new-console-app.png)

如果是在 64 位 Windows 安装上运行，可以选择将生成平台切换到 `x64`：

![将生成平台切换到 x64](media/sdk/qs-cpp-windows-02-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>安装并引用语音 SDK NuGet 包

在解决方案资源管理器中，右键单击该解决方案，并单击“为解决方案管理 NuGet 包”。

![右键单击“为解决方案管理 NuGet 包”](media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

在右上角的“包源”字段中，选择“Nuget.org”。
从“浏览”选项卡中，搜索“Microsoft.CognitiveServices.Speech”包，将其选中，再勾选右侧的“项目”和“helloworld”框，然后选择“安装”，将其安装到 helloworld 项目中。

> [!NOTE]
> 认知服务语音 SDK 的当前版本是 `0.5.0`。

![安装 Microsoft.CognitiveServices.Speech NuGet 包](media/sdk/qs-cpp-windows-04-nuget-install-0.5.0.png)

在弹出的许可证屏幕中，接受许可证：

![接受许可证](media/sdk/qs-cpp-windows-05-nuget-license.png)

## <a name="add-the-sample-code"></a>添加示例代码

1. 将默认的起始代码替换为以下代码：

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. 将字符串 `YourSubscriptionKey` 替换为你的订阅密钥。

1. 将字符串 `YourServiceRegion` 替换为与订阅关联的[区域](regions.md)（例如，免费试用版订阅的 `westus`）。

1. 保存对项目的更改。

## <a name="build-and-run-the-sample"></a>生成并运行示例

1. 构建应用程序。 从菜单栏中，选择“构建” > “构建解决方案”。 现在，编译代码时应该不会提示错误：

   ![成功的生成](media/sdk/qs-cpp-windows-06-build.png)

1. 启动应用程序。 在菜单栏中，选择“调试” > “开始调试”，或按 F5。

   ![启动应用进行调试](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. 将弹出控制台窗口，提示你说出任意内容（英语）。
   识别结果将显示在屏幕上。

   ![成功识别后的控制台输出](media/sdk/qs-cpp-windows-08-console-output-release.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 `quickstart/cpp-windows` 文件夹中查找此示例。

## <a name="next-steps"></a>后续步骤

* [获取我们的示例](speech-sdk.md#get-the-samples)
