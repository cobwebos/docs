---
title: 设置 Web 终结点（预览）
titleSuffix: Azure Cognitive Services
description: 为自定义命令设置 Web 终结点
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 1b7b5e209329bd5dc4c95f81f61fecf48fb74e40
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362104"
---
# <a name="set-up-web-endpoints"></a>设置 Web 终结点

本文介绍如何在自定义命令应用程序中设置 Web 终结点，这些终结点使你可从客户端应用程序发出 HTTP 请求。 你将完成以下任务：

- 在自定义命令应用程序中设置 Web 终结点
- 在自定义命令应用程序中调用 Web 终结点
- 接收 Web 终结点响应 
- 将 Web 终结点响应集成到自定义 JSON 有效负载，从 C# UWP SPEECH SDK 客户端应用程序中发送和直观呈现该响应

## <a name="prerequisites"></a>先决条件
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * 语音服务的 Azure 订阅密钥：[免费获取一个](overview.md#try-the-speech-service-for-free)或在 [Azure 门户](https://portal.azure.com)上创建它
> * 之前[创建的自定义命令应用](quickstart-custom-commands-application.md)
> * 已启用语音 SDK 的客户端应用：[操作说明：向客户端应用程序发送活动](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-web-endpoints"></a>设置 Web 终结点

1. 打开之前创建的自定义命令应用程序。 
1. 转到“Web 终结点”，单击“新建 Web 终结点”。

   > [!div class="mx-imgBorder"]
   > ![新建 Web 终结点](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | 设置 | 建议的值 | 说明 |
   | ------- | --------------- | ----------- |
   | 名称 | UpdateDeviceState | Web 终结点的名称。 |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | 你希望自定义命令应用与之通信的终结点的 URL。 |
   | 方法 | POST | 允许的与终结点之间的交互（如 GET、POST）。|
   | 标头 | 键：app，Value：取 applicationId 的前 8 位 | 要包含在请求头中的标头参数。|

    > [!NOTE]
    > - 使用 [Azure Function](https://docs.microsoft.com/azure/azure-functions/) 创建的示例 Web 终结点，该终结点与保存电视和风扇设备状态的数据库挂钩
    > - 建议的标头只是示例终结点需要而已
    > - 若要确保标头值在示例终结点中具有唯一性，请使用 applicationId 的前 8 位
    > - 在实际中，Web 终结点可以是管理设备的 [IoT 中心](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) 的终结点

1. 单击“ **保存**”。

## <a name="call-web-endpoints"></a>调用 Web 终结点

1. 转到“TurnOnOff”命令，选择完成规则下的“ConfirmationResponse”，然后选择“添加操作”  。
1. 在“新建操作类型”下，选择“调用 Web 终结点” 
1. 在“编辑操作 - 终结点”中，选择“UpdateDeviceState”，这是我们创建的 Web 终结点 。  
1. 在“配置”中，使用以下值： 
   > [!div class="mx-imgBorder"]
   > ![调用 Web 终结点操作参数](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | 设置 | 建议的值 | 说明 |
   | ------- | --------------- | ----------- |
   | 终结点 | UpdateDeviceState | 要在此操作中调用的 Web 终结点。 |
   | 查询参数 | item={SubjectDevice}&&value={OnOff} | 要追加到 Web 终结点 URL 的查询参数。  |
   | 正文内容 | 不可用 | 请求的正文内容。 |

    > [!NOTE]
    > - 建议的查询参数只是示例终结点需要而已

1. 在“成功时 - 要执行的操作”中，选择“发送语音响应” 。
    
    在“简单编辑器”中，输入 `{SubjectDevice} is {OnOff}`。
   
   > [!div class="mx-imgBorder"]
   > ![成功时调用 Web 终结点操作](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | 设置 | 建议的值 | 说明 |
   | ------- | --------------- | ----------- |
   | 要执行的操作 | 发送语音响应 | 对 Web 终结点的请求成功时要执行的操作 |
   
   > [!NOTE]
   > - 还可以使用 `{YourWebEndpointName.FieldName}` 直接访问 http 响应中的字段。 例如：`{UpdateDeviceState.TV}`

1. 在“失败时 - 要执行的操作”中，选择“发送语音响应” 

    在“简单编辑器”中，输入 `Sorry, {WebEndpointErrorMessage}`。

   > [!div class="mx-imgBorder"]
   > ![失败时调用 Web 终结点操作](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | 设置 | 建议的值 | 说明 |
   | ------- | --------------- | ----------- |
   | 要执行的操作 | 发送语音响应 | 对 Web 终结点的请求失败时要执行的操作 |

   > [!NOTE]
   > - `{WebEndpointErrorMessage}` 是可选项。 如果不希望公开任何错误消息，则可以随意将其删除。
   > - 在示例终结点中，我们发送了带有详细错误消息的 http 响应，以返回常见错误（例如缺少标头参数）的错误消息。 

### <a name="try-it-out-in-test-portal"></a>在测试门户中试用
- 成功时响应\
保存、训练并测试
   > [!div class="mx-imgBorder"]
   > ![成功时调用 Web 终结点操作](media/custom-commands/setup-web-endpoint-on-success-response.png)
- 失败时响应\
删除其中一个查询参数，保存，重新训练并测试
   > [!div class="mx-imgBorder"]
   > ![成功时调用 Web 终结点操作](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>与客户端应用程序集成

在[操作说明：向客户端应用程序发送活动（预览版）](./how-to-custom-commands-send-activity-to-client.md)中，你添加了“向客户端发送活动”操作。 无论“调用 Web 终结点”操作是否成功，都会向客户端应用程序发送活动。
但是，在大多数情况下，只有当调用 Web 终结点成功时才希望将活动发送到客户端应用程序。 本例中，这即为设备状态成功更新的时候。

1. 删除之前添加的“向客户端发送活动”操作。
1. 编辑调用 Web 终结点： 
    1. 在“配置”中，确保“查询参数”是 `item={SubjectDevice}&&value={OnOff}` 
    1. 在“成功时”中，更改“要执行的操作”，以向客户端发送活动  
    1. 将下面的 JSON 复制到“活动内容”
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
    > [!div class="mx-imgBorder"]
    > ![成功时发送活动](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-activity.png)
   
现在，只有当对 Web 终结点的请求成功时才向客户端发送活动。

### <a name="create-visuals-for-syncing-device-state"></a>创建同步设备状态的视觉对象
将以下 XML 添加到 `"EnableMicrophoneButton"` 块上方的 `MainPage.xaml` 中。

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>同步设备状态 

在 `MainPage.xaml.cs` 中，添加引用 `using Windows.Web.Http;`。 将以下代码添加到 `MainPage` 类。 此方法会向示例终结点发送 GET 请求，并提取应用的当前设备状态。 请务必将 `<your_app_name>` 更改为自定义命令 Web 终结点的标头中所使用的内容

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

## <a name="try-it-out"></a>试试看

1. 启动应用程序
1. 单击“同步设备状态”。\
如果你在上一部分中使用 `turn on tv` 测试了应用，便会发现电视显示为“开”。
    > [!div class="mx-imgBorder"]
    > ![同步设备状态](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. 选择“启用麦克风”
1. 选择“对话”按钮
1. 说出 `turn on the fan`
1. 风扇的可视状态应更改为“开”
    > [!div class="mx-imgBorder"]
    > ![打开风扇](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [为自定义命令应用程序启用 CI/CD 过程](./how-to-custom-commands-deploy-cicd.md)