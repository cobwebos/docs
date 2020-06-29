---
title: 设置 web 终结点（预览）
titleSuffix: Azure Cognitive Services
description: 为自定义命令设置 web 终结点
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: eb2a7d4f83b3d8bda0d06e14b4dab9bb4872885e
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85414277"
---
# <a name="set-up-web-endpoints"></a>设置 Web 终结点

本文介绍如何在自定义命令应用程序中设置 web 终结点，以允许从客户端应用程序发出 HTTP 请求。 你将完成以下任务：

- 在自定义命令应用程序中设置 web 终结点
- 在自定义命令应用程序中调用 web 终结点
- 接收 web 终结点响应 
- 通过 c # UWP 语音 SDK 客户端应用程序将 web 终结点响应集成到自定义 JSON 有效负载，并对其进行可视化

## <a name="prerequisites"></a>先决条件
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * 用于语音服务的 Azure 订阅密钥：[免费获取一个](get-started.md)，或在[Azure 门户](https://portal.azure.com)上创建它
> * 以前[创建的自定义命令应用](quickstart-custom-commands-application.md)
> * 启用了语音 SDK 的客户端应用：操作[说明：向客户端应用程序结束活动](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-web-endpoints"></a>设置 web 终结点

1. 打开之前创建的自定义命令应用程序。 
1. 请依次单击 "Web 终结点"、"新建 Web 终结点"。

   > [!div class="mx-imgBorder"]
   > ![新 web 终结点](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | 设置 | 建议的值 | 说明 |
   | ------- | --------------- | ----------- |
   | 名称 | UpdateDeviceState | Web 终结点的名称。 |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | 希望自定义命令应用与之通信的终结点的 URL。 |
   | 方法 | POST | 与终结点之间允许的交互（如 GET、POST）。|
   | 头文件 | 注册表项：应用程序的值：应用程序的唯一名称 | 要包含在请求标头中的标头参数。|

    > [!NOTE]
    > - 使用[Azure Function](https://docs.microsoft.com/azure/azure-functions/)创建的示例 web 终结点，该终结点与保存电视和风扇的设备状态的数据库挂钩
    > - 建议的标头仅对示例终结点是必需的
    > - 在现实世界中，web 终结点可以是用于管理设备的[IOT 中心](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)的终结点

1. 单击“保存” ****。

## <a name="call-web-endpoints"></a>调用 web 终结点

1. 中转到**TurnOnOff**命令，在 "完成规则" 下选择 " **ConfirmationResponse** "，然后选择 "**添加操作**"。
1. 在 "**新建操作-类型**" 下，选择 "**调用 web 终结点**"
1. 在 "**编辑操作-终结点**" 中，选择**UpdateDeviceState**，这是我们创建的 web 终结点。  
1. 在 "**配置**" 中，放置以下值： 
   > [!div class="mx-imgBorder"]
   > ![调用 web 终结点操作参数](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | 设置 | 建议的值 | 说明 |
   | ------- | --------------- | ----------- |
   | 终结点 | UpdateDeviceState | 要在此操作中调用的 web 终结点。 |
   | 查询参数 | item = {SubjectDevice} &&值 = {麦克风} | 要追加到 web 终结点 URL 的查询参数。  |
   | 正文内容 | 空值 | 请求的正文内容。 |

    > [!NOTE]
    > - 建议的查询参数仅对示例终结点是必需的

1. 在 **"成功时-要执行的操作**" 中，选择 "**发送语音响应**"。
   
   > [!div class="mx-imgBorder"]
   > ![成功时调用 web 终结点操作](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | 设置 | 建议的值 | 说明 |
   | ------- | --------------- | ----------- |
   | 要执行的操作 | 发送语音响应 | 对 web 终结点的请求成功时要执行的操作 |
   
   > [!NOTE]
   > - 还可以通过使用直接访问 http 响应中的字段 `{YourWebEndpointName.FieldName}` 。 例如： `{UpdateDeviceState.TV}`

1. 在 **"失败时-要执行的操作**" 中，选择 "**发送语音响应**"
   > [!div class="mx-imgBorder"]
   > ![失败时调用 web 终结点操作](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | 设置 | 建议的值 | 说明 |
   | ------- | --------------- | ----------- |
   | 要执行的操作 | 发送语音响应 | 对 web 终结点的请求失败时要执行的操作 |

   > [!NOTE]
   > - `{WebEndpointErrorMessage}` 是可选项。 如果你不希望公开任何错误消息，则可以随意将其删除。
   > - 在我们的示例终结点中，向后发送 http 响应，其中包含常见错误（如缺少标头参数）的详细错误消息。 

### <a name="try-it-out-in-test-portal"></a>在测试门户中试用
- 成功响应时 \
保存、定型和测试
   > [!div class="mx-imgBorder"]
   > ![成功时调用 web 终结点操作](media/custom-commands/setup-web-endpoint-on-success-response.png)
- 失败响应时 \
删除查询参数之一，保存、重新训练和测试
   > [!div class="mx-imgBorder"]
   > ![成功时调用 web 终结点操作](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>与客户端应用程序集成

在操作[方法：将活动发送到客户端应用程序（预览版）](./how-to-custom-commands-send-activity-to-client.md)中，向**客户端**操作添加了 Send 活动。 无论**调用 web 终结点**操作是否成功，都将活动发送到客户端应用程序。
但是，在大多数情况下，只需在调用 web 终结点时将活动发送到客户端应用程序。 在此示例中，这是成功更新设备的状态。

1. 删除之前添加的**客户端**操作的 "发送" 活动。
1. 编辑调用 web 终结点： 
    1. 在 "**配置**" 中，确保**查询参数**为`item={SubjectDevice}&&value={OnOff}`
    1. 如果**成功**，请更改要**执行的操作**以向**客户端发送活动**
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
    > ![成功发送活动](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-activity.png)
   
现在，仅当对 web 终结点的请求成功时才将活动发送到客户端。

### <a name="create-visuals-for-syncing-device-state"></a>创建用于同步设备状态的视觉对象
将以下 XML 添加到 `MainPage.xaml` 块上方 `"EnableMicrophoneButton"` 。

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>同步设备状态 

在中 `MainPage.xaml.cs` ，添加引用 `using Windows.Web.Http;` 。 将以下代码添加到 `MainPage` 类。 此方法会将 GET 请求发送到示例终结点，并提取应用程序的当前设备状态。 请确保更改 `<your_app_name>` 为自定义命令 Web 终结点的**标头**中使用的内容

```C#
private async void SyncDeviceState_ButtonClicked(object sender, RoutedEventArgs e)
{
    //Create an HTTP client object
    var httpClient = new HttpClient();

    //Add a user-agent header to the GET request. 
    var your_app_name = "<your-app-name>";

    Uri endpoint = new Uri("https://webendpointexample.azurewebsites.net/api/DeviceState");
    var requestMessage = new HttpRequestMessage(HttpMethod.Get, endpoint);
    requestMessage.Headers.Add("app", $"{your_app_name}");

    try
    {
        //Send the GET request
        var httpResponse = await httpClient.SendRequestAsync(requestMessage);
        httpResponse.EnsureSuccessStatusCode();
        var httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
        dynamic deviceState = JsonConvert.DeserializeObject(httpResponseBody);
        var TVState = deviceState.TV.ToString();
        var FanState = deviceState.Fan.ToString();
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
            CoreDispatcherPriority.Normal,
            () =>
            {
                State_TV.Text = TVState;
                State_Fan.Text = FanState;
            });
    }
    catch (Exception ex)
    {
        NotifyUser(
            $"Unable to sync device status: {ex.Message}");
    }
}
```

## <a name="try-it-out"></a>试用

1. 启动应用程序
1. 单击 "同步设备状态"。
如果 `turn on tv` 在上一节中对应用进行了测试，则会看到电视显示为 "打开"。
    > [!div class="mx-imgBorder"]
    > ![同步设备状态](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. 选择启用麦克风
1. 选择 "对话" 按钮
1. 轻松`turn on the fan`
1. 风扇的视觉状态应更改为 "开启"
    > [!div class="mx-imgBorder"]
    > ![打开风扇](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [为自定义命令应用程序启用 CI/CD 进程](./how-to-custom-commands-deploy-cicd.md)