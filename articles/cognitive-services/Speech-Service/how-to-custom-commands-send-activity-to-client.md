---
title: 将自定义命令活动发送到客户端应用程序
titleSuffix: Azure Cognitive Services
description: 本文介绍如何将活动从自定义命令应用程序发送到运行语音 SDK 的客户端应用程序。
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 0a3e3455615006c0e93cf32eebcdaedac9960a79
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307450"
---
# <a name="send-custom-commands-activity-to-client-application"></a>将自定义命令活动发送到客户端应用程序

本文介绍如何将活动从自定义命令应用程序发送到运行语音 SDK 的客户端应用程序。

完成以下任务：

- 从自定义命令应用程序定义并发送自定义 JSON 有效负载
- 接收和可视化 c # UWP Speech SDK 客户端应用程序中的自定义 JSON 负载内容

## <a name="prerequisites"></a>先决条件
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * 用于语音服务的 Azure 订阅密钥：[免费获取一个](get-started.md)，或在[Azure 门户](https://portal.azure.com)上创建它
> * 以前[创建的自定义命令应用](quickstart-custom-commands-application.md)
> * 支持语音 SDK 的客户端应用：[如何：使用语音 sdk 与客户端应用程序集成](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>将活动设置发送到客户端 
1. 打开之前创建的自定义命令应用程序
1. 选择**TurnOnOff**命令，在 "完成规则" 下选择 " **ConfirmationResponse** "，然后选择 "**添加操作**"
1. 在 "**新建操作-类型**" 下，选择 "**向客户端发送活动**"
1. 将下面的 JSON 复制到**活动内容**
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
1. 单击 "**保存**" 以创建具有 Send 活动操作的新规则

   > [!div class="mx-imgBorder"]
   > ![发送活动完成规则](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>与客户端应用程序集成

在[操作方法：使用 SPEECH sdk （预览版）设置客户端应用程序](./how-to-custom-commands-setup-speech-sdk.md)中，你创建了一个使用 speech SDK 的 UWP 客户端应用程序，该应用程序已处理诸如、之类的命令 `turn on the tv` `turn off the fan` 。 添加一些视觉对象后，可以看到这些命令的结果。

使用添加到的以下 XML 添加标记为 **"开**" 或 "**关**" 的文本`MainPage.xaml`

```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
```

### <a name="add-reference-libraries"></a>添加引用库

由于已创建 JSON 有效负载，因此需要添加对[JSON.NET](https://www.newtonsoft.com/json)库的引用来处理反序列化。

1. 向右客户端解决方案。
1. 选择 "**管理解决方案的 NuGet 包**"，选择 "**安装**" 
1. 在更新列表中搜索 " **Newtonsoft.js** "，将**NETCore**更新为最新版本

> [!div class="mx-imgBorder"]
> ![发送活动有效负载](media/custom-commands/send-activity-to-client-json-nuget.png)

在 "MainPage" 中，添加
- `using Newtonsoft.Json;` 
- `using Windows.ApplicationModel.Core;`

### <a name="handle-the-received-payload"></a>处理收到的有效负载

在中 `InitializeDialogServiceConnector` ，将 `ActivityReceived` 事件处理程序替换为下面的代码。 修改后的 `ActivityReceived` 事件处理程序将从活动中提取有效负载，并分别更改电视或风扇的视觉状态。

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

## <a name="try-it-out"></a>试用

1. 启动应用程序
1. 选择启用麦克风
1. 选择 "对话" 按钮
1. 轻松`turn on the tv`
1. 电视的视觉状态应更改为 "开启"
   > [!div class="mx-imgBorder"]
   > ![发送活动有效负载](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何：设置 web 终结点（预览）](./how-to-custom-commands-setup-web-endpoints.md)