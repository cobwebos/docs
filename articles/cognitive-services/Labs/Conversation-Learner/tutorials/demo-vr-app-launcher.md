---
title: 演示对话学习器模型，虚拟现实应用启动器 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何创建演示对话学习器模型。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 896ec007c03e30e5c20a5344430be040271bc00b
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171136"
---
# <a name="demo-virtual-reality-app-launcher"></a>演示：虚拟现实应用启动器

该演示阐释了支持“启动 Skype 并置于留言板内”等命令的虚拟现实模型启动器。 用户需要说出应用名称和位置才能启动该应用。 模型启动通过 API 调用处理。 当用户识别出应用名称时，entityDetectionCallback 会检查所请求的应用是否与已安装应用列表中的一个或多个应用匹配。 它处理未安装所请求应用的情况，以及应用名称不明确的情况（匹配多个已安装的应用）。

## <a name="video"></a>视频

[![演示 VR 应用预览](http://aka.ms/cl-demo-vrapp-preview)](http://aka.ms/blis-demo-vrapp)

## <a name="requirements"></a>要求

本教程要求运行 VRAppLauncher 机器人：

    npm run demo-vrapp
    
### <a name="open-the-demo"></a>打开演示

在 Web UI 的“模型”列表中，单击 VRAppLauncher。 

## <a name="entities"></a>实体

我们已创建了四个实体：

- AppName：如 Skype
- PlacementLocation：如留言板
- UnknownAppName：系统在无法识别用户所说的实体名称时设置的编程实体，例如因为该实体尚未安装。
- DisAmbigAppNames：两个或多个与用户所说相匹配的已安装应用名称的数组。 

![](../media/tutorial_vrapplauncher_entities.PNG)

### <a name="actions"></a>操作

我们创建了一组操作，其中包含一个名为 LaunchApp 的 API，它将启动函数调用以启动应用。

![](../media/tutorial_vrapplauncher_actions.PNG)

### <a name="training-dialogs"></a>定型对话
我们已定义大量定型对话。

![](../media/tutorial_vrapplauncher_dialogs.PNG)

例如，让我们尝试一个教学会话。

1. 依次单击“定型”对话框和“新建定型”对话框。
1. 输入“hi”。
2. 单击“对操作打分”。
3. 单击选择“你想启动哪个应用?”
4. 输入“outlook”。
    - LUIS 将其识别为实体。
5. 单击“对操作打分”。
3. 单击以选择“想要把它置于哪个位置?”
4. 输入“置于留言板上”。
    - LUIS 将其识别为 PlacementLocation。
2. 输入“对操作打分”
6. 选择“LaunchApp”
7. 系统：“启动留言板上的 outlook”。
    - 这会触发 API 调用。 此调用的代码位于 C:\<\installedpath>\src\demos\demoVRAppLauncher.ts。 然而，该调用实际上并没有为该演示启动 Outlook 的代码。
    - 它将清除 AppName 和 PlacementLocation 实体。 返回以上字符串作为响应。
4. 单击“完成教学”。

![](../media/tutorial_vrapplauncher_callbackcode.PNG)

让我们开始另一个定型会话来处理未知和不明确的实体。

1. 单击“新建定型对话”。
1. 输入“启用 OneNote”。 
    - 该模型将 OneNote 识别为应用名。 代码中定义的 `EntityDetectionCallback` 函数将用户输入的名称与代码中定义的应用列表进行匹配，从而将该名称解析为应用名。 然后，它返回一组所有匹配的应用。 
    - 如果匹配列表为零，则表示未安装该应用程序。 系统将其放入 unknownAppName。
    - 如果它找到多个应用，则会将它们复制到 `DisambigAppNames` 并清除 AppName 实体。
2. 单击“对操作打分”。
3. 单击以选择“抱歉，我不了解应用 $UknownAppName”。
4. 输入“start Amazon”。 我们将尝试其他路径。
5. 单击“对操作打分”。
    - Amazon 视频和 Amazon 音乐现位于 `DisambigAppNames` 内存中，系统已清除 OneNote。
3. 单击选择“有几个听起来类似...的应用”
    - 因为到目前为止我们只有一些定型对话，所以得分不是很高。 定义更多的定型对话会使模型更明确。
2. 输入“对操作打分”
4. 单击“完成教学”。

你现在已经了解如何执行实体解析。 该演示还阐释了 API 回调，并显示了一个模板，用于收集信息，检查存在性和多义性，并据此采取正确的操作。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [部署会话学习器机器人](../deploy-to-bf.md)
