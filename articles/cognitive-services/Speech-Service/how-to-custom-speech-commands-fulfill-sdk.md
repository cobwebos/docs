---
title: 如何使用语音 SDK 实现来自客户端的命令
titleSuffix: Azure Cognitive Services
description: 在本文中，我们将解释如何使用语音 SDK 处理客户端上的自定义命令活动。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: donkim
ms.openlocfilehash: e109955774722da7f55defe1417de35ff202cce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367732"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>使用语音 SDK 实现来自客户端的命令（预览）

要使用自定义命令应用程序完成任务，可以将自定义负载发送到已连接的客户端设备。

在本文中，您将：

- 定义自定义 JSON 负载，并从自定义命令应用程序
- 从 C# UWP 语音 SDK 客户端应用程序接收和可视化自定义 JSON 有效负载内容

## <a name="prerequisites"></a>先决条件

- [视觉工作室 2019](https://visualstudio.microsoft.com/downloads/)
- 语音服务的 Azure 订阅密钥
  - [免费获取一个](get-started.md)或在[Azure 门户](https://portal.azure.com)上创建
- 以前创建的自定义命令应用
  - [快速入门：使用参数创建自定义命令（预览）](./quickstart-custom-speech-commands-create-parameters.md)
- 启用语音 SDK 的客户端应用程序
  - [快速入门：使用语音 SDK 连接到自定义命令应用程序（预览版）](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>可选：快速入门

本文将逐步介绍如何使客户端应用程序与自定义命令应用程序进行对话。 如果您喜欢直接潜入，本文中使用的完整、即用即用的源代码可在[语音 SDK 示例](https://aka.ms/csspeech/samples)中。

## <a name="fulfill-with-json-payload"></a>使用 JSON 有效负载实现

1. 从[语音工作室](https://speech.microsoft.com/)打开以前创建的自定义命令应用程序
1. 检查 **"完成规则"** 部分，以确保您拥有以前创建的规则，该规则已响应用户
1. 要将有效负载直接发送到客户端，请使用"发送活动"操作创建新规则

   > [!div class="mx-imgBorder"]
   > ![发送活动完成规则](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | 设置 | 建议的值 | 描述 |
   | ------- | --------------- | ----------- |
   | 规则名称 | 更新设备状态 | 描述规则目的的名称 |
   | 条件 | 所需参数`OnOff`- 和`SubjectDevice` | 确定规则何时可以运行的条件 |
   | 操作 | `SendActivity`（见下文） | 规则条件为 true 时要执行的操作 |

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

## <a name="create-visuals-for-device-on-or-off-state"></a>为设备打开或关闭状态创建视觉对象

在[快速入门中：使用语音 SDK（预览）连接到自定义命令应用程序，](./quickstart-custom-speech-commands-speech-sdk.md)您创建了一个语音 SDK 客户端应用程序，该应用程序`turn on the tv`处理`turn off the fan`命令，如 。 现在添加一些视觉对象，以便可以看到这些命令的结果。

使用以下 XML 添加带有文本指示 **"开****"或"关"** 的标签框`MainPage.xaml.cs`

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

## <a name="handle-customizable-payload"></a>处理可自定义的有效负载

现在，您已经创建了 JSON 有效负载，您可以添加对[JSON.NET](https://www.newtonsoft.com/json)库的引用来处理反序列化。

> [!div class="mx-imgBorder"]
> ![发送活动有效负载](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

在`InitializeDialogServiceConnector`将以下内容添加到事件`ActivityReceived`处理程序中。 附加代码将从活动中提取有效负载，并相应地更改电视或风扇的可视状态。

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

## <a name="try-it-out"></a>试用

1. 启动应用程序
1. 选择启用麦克风
1. 选择"通话"按钮
1. 说`turn on the tv`
1. 电视的视觉状态应更改为"打开"

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何：将验证添加到自定义命令参数（预览）](./how-to-custom-speech-commands-validations.md)
