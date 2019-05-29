---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: afe6f1493c7fa8272c67f23d6708ad6e4eea9381
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145505"
---
1. 启动 Visual Studio 2017。

1. 在 Visual Studio 的菜单栏中，选择“工具”>“获取工具”，确保“.NET 桌面开发”工作负荷可用。   如果工作负荷尚未安装，请标记该复选框，然后单击“修改”以启动安装。  下载和安装过程可能需要几分钟。

   如果“.NET 桌面开发”旁边的复选框已选中，现在就可以关闭对话框。 

   ![启用 .NET 桌面开发](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. 接下来，让我们创建一个项目。 从菜单栏中选择“文件”>“新建”>“项目”  。 当对话框显示以后，请在左面板中展开以下部分：“已安装”>“Visual C#”>“Windows 桌面”，然后选择“控制台应用(.NET Framework)”   。 将此项目命名为 *helloworld*。

    ![创建 Visual C# 控制台应用 (.NET Framework)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Create Visual C# Console App (.NET Framework)")

1. 设置项目以后，需安装[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)并在代码中引用它。 找到解决方案资源管理器，右键单击“helloworld”。 在菜单中选择“管理 NuGet 包...”。 

   ![右键单击“为解决方案管理 NuGet 包”](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "为解决方案管理 NuGet 包")

1. 在 NuGet 包管理器  的右上角找到“包源”下拉列表，确保“nuget.org”已选中  。 然后选择“浏览”，搜索  `Microsoft.CognitiveServices.Speech` 包并安装最新的稳定版本。

   ![安装 Microsoft.CognitiveServices.Speech NuGet 包](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "安装 NuGet 包")

1. 接受所有协议和许可证，开始安装。

   ![接受许可证](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Accept the license")

    安装此包后，在包管理器控制台中会显示一条确认消息。

1. 下一步是创建平台配置，使之与用来生成并运行控制台应用程序的计算机的体系结构相匹配。 在菜单栏中，选择“生成” > “配置管理器...”   。

    ![启动配置管理器](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Launch the configuration manager")

1. 在“配置管理器”对话框中找到“活动解决方案平台”下拉列表，选择“新建”    。

    ![在配置管理器窗口下添加新平台](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Add a new platform under the configuration manager window")

1. 如果运行的是 64 位 Windows，则当系统提示“键入或选择新平台”时，键入 `x64`。  如果运行的是 32 位 Windows，则选择 `x86`。 完成后，单击“确定”  。

    ![在 64 位 Windows 上，添加名为“x64”的新平台](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "添加 x64 平台")
