---
title: 如何使用 Speech SDK 实现客户端的命令
titleSuffix: Azure Cognitive Services
description: 本文介绍如何使用语音 SDK 处理客户端上的自定义命令活动。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: donkim
ms.openlocfilehash: e109955774722da7f55defe1417de35ff202cce8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "79367732"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>使用 Speech SDK （预览版）从客户端中完成命令

若要使用自定义命令应用程序完成任务，你可以将自定义负载发送到已连接的客户端设备。

本文介绍如何执行以下操作：

- 从自定义命令应用程序定义并发送自定义 JSON 有效负载
- 接收和可视化 c # UWP Speech SDK 客户端应用程序中的自定义 JSON 负载内容

## <a name="prerequisites"></a>先决条件

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- 用于语音服务的 Azure 订阅密钥
  - [免费获取一个](get-started.md)或创建一个[Azure 门户](https://portal.azure.com)
- 以前创建的自定义命令应用
  - [快速入门：使用参数创建自定义命令（预览）](./quickstart-custom-speech-commands-create-parameters.md)
- 启用了语音 SDK 的客户端应用程序
  - [快速入门：使用 Speech SDK （预览版）连接到自定义命令应用程序](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>可选：快速入门

本文逐步介绍如何使客户端应用程序与自定义命令应用程序通信。 如果你想要深入了解，则可在[语音 SDK 示例](https://aka.ms/csspeech/samples)中找到本文中使用的完整、可随时编译的源代码。

## <a name="fulfill-with-json-payload"></a>用 JSON 有效负载完成

1. 从[Speech Studio](https://speech.microsoft.com/)打开之前创建的自定义命令应用程序
1. 检查 "**完成规则**" 部分，确保你有以前创建的用于响应用户的规则
1. 若要将负载直接发送到客户端，请创建具有 Send 活动操作的新规则

   > [!div class="mx-imgBorder"]
   > ![发送活动完成规则](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | 设置 | 建议的值 | 说明 |
   | ------- | --------------- | ----------- |
   | 规则名称 | UpdateDeviceState | 描述规则用途的名称 |
   | Conditions | 必需的参数`OnOff` -和`SubjectDevice` | 确定何时可以运行规则的条件 |
   | 操作 | `SendActivity`（请参阅下文） | 规则条件为 true 时要执行的操作 |

   > [!div class="mx-imgBorder"]
   > ![发送活动有效负载](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>为设备开启或关闭状态创建视觉对象

在[快速入门：使用 SPEECH sdk （预览版）连接到自定义命令应用](./quickstart-custom-speech-commands-speech-sdk.md)程序创建了一个语音 sdk 客户端应用程序，该`turn on the tv`应用`turn off the fan`程序将处理诸如、等命令。 现在，添加一些视觉对象，以便可以看到这些命令的结果。

使用添加到的以下 XML 添加标记为 **"开**" 或 "**关**" 的文本`MainPage.xaml.cs`

```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
```

## <a name="handle-customizable-payload"></a>处理可自定义的负载

创建 JSON 有效负载后，可以添加对[JSON.NET](https://www.newtonsoft.com/json)库的引用来处理反序列化。

> [!div class="mx-imgBorder"]
> ![发送活动有效负载](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

在`InitializeDialogServiceConnector`中，将以下项`ActivityReceived`添加到事件处理程序中。 其他代码将从活动中提取有效负载，并相应地更改电视或风扇的视觉状态。

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);

    if(activity?.name == "SetDeviceState")
    {
        var state = activity?.state;
        var device = activity?.device;
        switch(device)
        {
            case "tv":
                State_TV.Text = state;
                break;
            case "fan":
                State_Fan.Text = state;
                break;
            default:
                NotifyUser($"Received request to set unsupported device {device} to {state}");
                break;
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>试试看

1. 启动应用程序
1. 选择启用麦克风
1. 选择 "对话" 按钮
1. 轻松`turn on the tv`
1. 电视的视觉状态应更改为 "开启"

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何：向自定义命令参数添加验证（预览）](./how-to-custom-speech-commands-validations.md)
