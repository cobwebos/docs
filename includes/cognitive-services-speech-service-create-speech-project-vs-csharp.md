---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: 8ff8e8341b6f39f66c2cc8014d41d3d3a2918d2b
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454516"
---
1. 启动 Visual Studio 2017。
 
1. 请确保 **.NET 桌面环境**工作负荷可用。 在 Visual Studio 菜单栏中选择“工具”\>“获取工具和功能”，以打开 Visual Studio 安装程序。 如果此工作负荷已启用，请关闭对话框。 

    否则，请选中“.NET 桌面开发”旁边的复选框，然后单击对话框右下角的“修改”按钮。 安装新功能将花费一些时间。

    ![启用 .NET 桌面开发](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. 创建新的 Visual C# 控制台应用。 在“新建项目”对话框的左窗格中，展开“已安装”\>“Visual C#”\>“Windows 桌面”，然后选择“控制台应用(.NET Framework)”。 在项目名称处，输入 helloworld。

    ![创建 Visual C# 控制台应用 (.NET Framework)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Create Visual C# Console App (.NET Framework)")

1. 安装并引用[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)。 在解决方案资源管理器中，右键单击该解决方案，并选择“为解决方案管理 NuGet 包”。

    ![右键单击“为解决方案管理 NuGet 包”](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "为解决方案管理 NuGet 包")

1. 在右上角的“包源”字段中，选择“nuget.org”。搜索 `Microsoft.CognitiveServices.Speech` 包，并将其安装到“helloworld”项目中。

    ![安装 Microsoft.CognitiveServices.Speech NuGet 包](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "安装 NuGet 包")

1. 接受显示的许可证即可开始安装 NuGet 包。

    ![接受许可证](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Accept the license")

    安装此包后，在包管理器控制台中会显示一条确认消息。

1. 通过配置管理器创建与你的电脑体系结构匹配的平台配置。 选择“生成” > “配置管理器”。

    ![启动配置管理器](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Launch the configuration manager")

1. 在“配置管理器”对话框中，添加新平台。 从“活动解决方案平台”下拉列表中，选择“新建”。

    ![在配置管理器窗口下添加新平台](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Add a new platform under the configuration manager window")

1. 如果运行的是 64 位 Windows，请创建名为 `x64` 的新平台配置。 如果运行的是 32 位 Windows，请创建名为 `x86` 的新平台配置。

    ![在 64 位 Windows 上，添加名为“x64”的新平台](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "添加 x64 平台")


