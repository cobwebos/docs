---
title: 快速入门：用于.NET Framework (Windows) 的语音 SDK 平台设置 - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用本指南，通过语音服务 SDK 在用于 Windows 的 .NET Framework 下设置 C# 平台。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: 2caa5124144146e2286b102bf6ded9c5d120f904
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504425"
---
本指南介绍如何安装用于 .NET Framework (Windows) 的[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>先决条件

本快速入门需要：

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>创建 Visual Studio 项目并安装语音 SDK

需安装[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)，以便在代码中引用它。 为此，可能首先需要创建 **helloworld** 项目。 如果已经有一个可以使用“.NET 桌面开发”  工作负荷的项目，则可使用该项目并跳到[使用 NuGet 包管理器安装语音 SDK](#use-nuget-package-manager-to-install-the-speech-sdk)。

### <a name="create-helloworld-project"></a>创建 helloworld 项目

1. 打开 Visual Studio 2019。

1. 在“开始”窗口中，选择“创建新项目”  。 

1. 在“创建新项目”窗口中，选择“控制台应用(.NET Framework)”，然后选择“下一步”。   

1. 在“配置新项目”窗口中的“项目名称”内输入 *helloworld*，在“位置”中选择或创建目录路径，然后选择“创建”。    

1. 在 Visual Studio 菜单栏中，选择“工具” > “获取工具和功能”打开 Visual Studio 安装程序并显示“修改”对话框。   

1. 检查“.NET 桌面开发”工作负荷是否可用。  如果尚未安装该工作负荷，请选中它旁边的复选框，然后选择“修改”以启动安装。  下载和安装过程可能需要几分钟。

   如果已选中“.NET 桌面开发”旁边的复选框，请选择“关闭”退出对话框。  

   ![启用 .NET 桌面开发](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. 关闭 Visual Studio 安装程序。

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>使用 NuGet 包管理器安装语音 SDK

1. 在解决方案资源管理器中右键单击“helloworld”项目，然后选择“管理 NuGet 包”显示 NuGet 包管理器。  

   ![NuGet 包管理器](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. 在右上角找到“包源”  下拉框，确保选择了 **`nuget.org`** 。

1. 在左上角，选择“浏览”  。

1. 在搜索框中，键入 *Microsoft.CognitiveServices.Speech* 并按 **Enter**。

1. 在搜索结果中选择“Microsoft.CognitiveServices.Speech”包，然后选择“安装”以安装最新稳定版本。  

   ![安装 Microsoft.CognitiveServices.Speech NuGet 包](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. 接受所有协议和许可证，开始安装。

   安装此包后，“包管理器控制台”  窗口中将显示一条确认消息。

现在可以继续完成下面的[后续步骤](#next-steps)。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [windows](../quickstart-list.md)]
