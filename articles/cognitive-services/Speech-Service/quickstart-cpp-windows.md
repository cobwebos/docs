---
title: 快速入门：识别语音，C++ (Windows) - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何使用语音服务 SDK 在 Windows 桌面上的 C++ 中识别语音
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: wolfma
ms.openlocfilehash: 454b84f4ba5e492f61daf67c980b204c82a2090d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53075181"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>快速入门：使用语音 SDK 在 Windows 上的 C++ 中识别语音

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

在本文中，请创建适用于 Windows 的 C++ 控制台应用程序。 请使用认知服务[语音 SDK](speech-sdk.md)，通过电脑的麦克风实时将语音转录为文本。 该应用程序是使用[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)和 Microsoft Visual Studio 2017（任何版本）构建的。

## <a name="prerequisites"></a>先决条件

需要具有语音服务订阅密钥才能完成此快速入门。 你可以免费获得一个。 有关详细信息，请参阅[免费试用语音服务](get-started.md)。

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

1. 启动 Visual Studio 2017。

1. 确保“使用 C++ 进行的桌面开发”工作负荷可用。 在 Visual Studio 菜单栏中选择“工具” > “获取工具和功能”，以便打开 Visual Studio 安装程序。 如果此工作负荷已启用，请跳到下一步。

    ![Visual Studio 工作负荷选项卡的屏幕截图](media/sdk/vs-enable-cpp-workload.png)

    否则，请勾选“使用 C++ 进行的桌面开发”旁边的框。

1. 确保 **NuGet 包管理器**组件可用。 切换到 Visual Studio 安装程序对话框的“单个组件”选项卡，选择“NuGet 包管理器”（如果尚未启用）。

      ![Visual Studio 的“单个组件”选项卡的屏幕截图](media/sdk/vs-enable-nuget-package-manager.png)

1. 如需启用 C++ 工作负荷或 NuGet，请选择对话框右下角的“修改”。 安装新功能需要一定的时间。 如果两项功能均已启用，请改为关闭对话框。

1. 创建新的 Visual C++ Windows 桌面 Windows 控制台应用程序。 首先，从菜单中选择“文件” > “新建” > “项目”。 在“新建项目”对话框的左窗格中，展开“已安装” > “Visual C++” > “Windows 桌面”， 然后选择“Windows 控制台应用程序”。 在项目名称处，输入 helloworld。

    ![“新建项目”对话框屏幕截图](media/sdk/qs-cpp-windows-01-new-console-app.png)

1. 如果运行 64 位 Windows，可以使用 Visual Studio 工具栏中的下拉菜单将生成平台切换为 `x64`。 （64 位版本的 Windows 可以运行 32 位应用程序，因此这不是必需的。）

    ![Visual Studio 工具栏的屏幕截图，其中突出显示了 x64 选项](media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. 在解决方案资源管理器中，右键单击该解决方案，然后选择“为解决方案管理 NuGet 包”。

    ![解决方案资源管理器的屏幕截图，其中突出显示了“为解决方案管理 NuGet 包”选项](media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. 在右上角的“包源”字段中，选择“nuget.org”。搜索 `Microsoft.CognitiveServices.Speech` 包，并将其安装到“helloworld”项目中。

    ![“管理解决方案包”对话框的屏幕截图](media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > 认知服务语音 SDK 的当前版本是 `1.1.0`。

1. 接受显示的许可证即可开始安装 NuGet 包。

    ![“接受许可证”对话框的屏幕截图](media/sdk/qs-cpp-windows-05-nuget-license.png)

安装此包后，在包管理器控制台中会显示一条确认消息。

## <a name="add-sample-code"></a>添加示例代码

1. 打开源文件 *helloworld.cpp*。 将初始 include 语句（`#include "stdafx.h"` 或 `#include "pch.h"`）下面的所有代码替换为以下内容：

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. 在同一文件中，将字符串 `YourSubscriptionKey` 替换为你的订阅密钥。

1. 将字符串 `YourServiceRegion` 替换为与订阅关联的[区域](regions.md)（例如，免费试用版订阅的 `westus`）。

1. 保存对项目的更改。

## <a name="build-and-run-the-app"></a>生成并运行应用

1. 构建应用程序。 从菜单栏中，选择“构建” > “构建解决方案”。 编译代码时应不会出错。

   ![Visual Studio 应用程序的屏幕截图，其中突出显示了“生成解决方案”选项](media/sdk/qs-cpp-windows-06-build.png)

1. 启动应用程序。 在菜单栏中，选择“调试” > “开始调试”，或按 F5。

   ![Visual Studio 应用程序的屏幕截图，其中突出显示了“启动调试”选项](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. 此时将显示控制台窗口，提示你说出任意内容。 说一个英语短语或句子。 你的语音将传输到语音服务并转录为文本，该文本将显示在同一窗口中。

   ![成功识别后的控制台输出的屏幕截图](media/sdk/qs-cpp-windows-08-console-output-release.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 `quickstart/cpp-windows` 文件夹中查找此示例。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用适用于 C++ 的语音 SDK 从语音中识别意向](how-to-recognize-intents-from-speech-cpp.md)

## <a name="see-also"></a>另请参阅

- [翻译语音](how-to-translate-speech-csharp.md)
- [自定义声学模型](how-to-customize-acoustic-models.md)
- [自定义语言模型](how-to-customize-language-model.md)
