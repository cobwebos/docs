---
title: 如何使用 Speech SDK 实现客户端的命令
titleSuffix: Azure Cognitive Services
description: 本文介绍如何使用语音 SDK 处理客户端上的自定义命令活动。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: trbye
ms.openlocfilehash: f11f5f3c2ad4c9f0241d34edeb664f739f88d15c
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871742"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>使用 Speech SDK （预览版）从客户端中完成命令

若要使用自定义命令应用程序完成任务，你可以将自定义负载发送到已连接的客户端设备。

本文介绍如何执行以下操作：

- 从自定义命令应用程序定义并发送自定义 JSON 有效负载
- 接收和可视化 c # UWP Speech SDK 客户端应用程序中的自定义 JSON 负载内容

## <a name="prerequisites"></a>必备条件
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * 用于语音服务的 Azure 订阅密钥：[免费获取一个](get-started.md)，或在[Azure 门户](https://portal.azure.com)上创建它
> * 以前创建的自定义命令应用：[快速入门：使用参数创建自定义命令（预览）](./quickstart-custom-speech-commands-create-parameters.md)
> * 启用了语音 SDK 的客户端应用程序：[快速入门：使用 SPEECH SDK （预览版）连接到自定义命令应用程序](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>可选：快速入门

本文逐步介绍如何使客户端应用程序与自定义命令应用程序通信。 如果你想要深入了解，则可在[语音 SDK 示例](https://aka.ms/csspeech/samples)中找到本文中使用的完整、可随时编译的源代码。

## <a name="fulfill-with-json-payload"></a>用 JSON 有效负载完成

1. 打开之前通过快速入门创建的自定义命令应用程序[：使用参数创建自定义命令](./quickstart-custom-speech-commands-create-parameters.md)
1. 检查 "**完成规则**" 部分，确保你有以前创建的用于响应用户的规则
1. 若要将负载直接发送到客户端，请创建具有 Send 活动操作的新规则

   > [!div class="mx-imgBorder"]
   > ![发送活动完成规则](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | 设置 | 建议的值 | 说明 |
   | ------- | --------------- | ----------- |
   | 规则名称 | UpdateDeviceState | 描述规则用途的名称 |
   | 条件 | 必需的参数`OnOff` -和`SubjectDevice` | 确定何时可以运行规则的条件 |
   | 操作 | `SendActivity`（请参阅下文） | 规则条件为 true 时要执行的操作 |

1. 将下面的 JSON 复制到**活动内容**
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
   > [!div class="mx-imgBorder"]
   > ![发送活动有效负载](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

## <a name="create-visuals-for-device-on-or-off-state"></a>为设备开启或关闭状态创建视觉对象

在[快速入门：使用语音 Sdk 连接到自定义命令应用程序](./quickstart-custom-speech-commands-speech-sdk.md)，你创建了一个语音 sdk 客户端应用程序， `turn on the tv`该`turn off the fan`应用程序将处理诸如、等命令。 添加一些视觉对象后，可以看到这些命令的结果。

使用添加到的以下 XML 添加标记为 **"开**" 或 "**关**" 的文本`MainPage.xaml`

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
### <a name="add-reference-libraries"></a>添加引用库

由于已创建 JSON 有效负载，因此需要添加对[JSON.NET](https://www.newtonsoft.com/json)库的引用来处理反序列化。
- 向右客户端解决方案。
- 选择 "**管理解决方案的 NuGet 包**"，选择 "**安装**" 
- 在更新列表中搜索**newtonsoft.json** ，并将**microsoft.netcore.universalwindowsplatform**更新到最新版本

> [!div class="mx-imgBorder"]
> ![发送活动有效负载](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

在 "MainPage" 中，添加
- `using Newtonsoft.Json;` 
- `using Windows.ApplicationModel.Core;`

### <a name="handle-received-payload"></a>处理收到的有效负载

在`InitializeDialogServiceConnector`中，将`ActivityReceived`事件处理程序替换为下面的代码。 修改后`ActivityReceived`的事件处理程序将从活动中提取有效负载，并相应地更改电视或风扇的视觉状态。

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.device != null ? activity.state.ToString() : string.Empty;
        var device = activity?.device != null ? activity.device.ToString() : string.Empty;

        if (state.Equals("on") || state.Equals("off"))
        {
            switch (device)
            {
                case "tv":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_TV.Text = state; });
                    break;
                case "fan":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_Fan.Text = state; });
                    break;
                default:
                    NotifyUser($"Received request to set unsupported device {device} to {state}");
                    break;
            }
        }
        else { 
            NotifyUser($"Received request to set unsupported state {state}");
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
   > [!div class="mx-imgBorder"]
   > ![发送活动有效负载](media/custom-speech-commands/fulfill-sdk-turn-on-tv.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何：向自定义命令参数添加验证（预览）](./how-to-custom-speech-commands-validations.md)
