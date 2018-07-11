---
title: 使用 Azure 通知中心向特定用户发送通知 | Microsoft Docs
description: 了解如何使用通用 Windows 平台 (UWP) 应用程序向特定用户发送通知。
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
ms.assetid: 012529f2-fdbc-43c4-8634-2698164b5880
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/04/2018
ms.author: dimazaid
ms.openlocfilehash: ee0e78402515a733731e6faf21b09886674cda51
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778236"
---
# <a name="tutorial-send-notifications-to-specific-users-by-using-azure-notification-hubs"></a>教程：使用 Azure 通知中心向特定用户发送通知
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>概述
本教程说明如何使用 Azure 通知中心将推送通知发送到特定设备上的特定应用程序用户。 ASP.NET WebAPI 后端用于对客户端进行身份验证。 后端在对客户端应用程序用户进行身份验证时，会自动将标记添加到通知注册中。 后端使用此标记将通知发送到特定用户。 

> [!NOTE]
> 可以在 [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers) 上找到本教程的已完成代码。 

在本教程中，请执行以下步骤：

> [!div class="checklist"]
> * 创建 WebAPI 项目
> * 在 WebAPI 后端对客户端进行身份验证
> * 使用 WebAPI 后端注册通知
> * 从 WebAPI 后端发送通知
> * 发布新的 WebAPI 后端
> * 更新客户端项目的代码
> * 测试应用程序


## <a name="prerequisites"></a>先决条件
本教程基于[教程：使用 Azure 通知中心向通用 Windows 平台应用发送通知](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)教程中创建的通知中心和 Visual Studio 项目编写。 因此，请在开始本教程之前完成该教程。 

> [!NOTE]
> 如果使用 Azure 应用服务中的移动应用作为后端服务，请参阅本教程的[移动应用版本](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md)。


&nbsp;

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="update-the-code-for-the-client-project"></a>更新客户端项目的代码
在此部分，请更新为[教程：使用 Azure 通知中心向通用 Windows 平台应用发送通知](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)教程完成的项目中的代码。 该项目应该已经与 Windows 应用商店相关联， 并且也应该已经配置为使用通知中心。 在本部分，请添加相关代码，以便调用新的 WebAPI 后端并使用该后端来注册和发送通知。

1. 在 Visual Studio 中，请打开为[教程：使用 Azure 通知中心向通用 Windows 平台应用发送通知](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)创建的解决方案。
2. 在“解决方案资源管理器”中，右键单击“WindowsApp”项目，然后单击“管理 NuGet 包”。
3. 在左侧单击“**联机**”。
4. 在“**搜索**”框中键入 **Http 客户端**。
5. 在结果列表中单击“System.Net.Http”，然后单击“安装”。 完成安装。
6. 返回到 NuGet“**搜索**”框，键入 **Json.net**。 安装 **Newtonsoft.json** 包，然后关闭“NuGet 包管理器”窗口。
8. 在解决方案资源管理器的 **WindowsApp** 项目中双击“MainPage.xaml”，在 Visual Studio 编辑器中打开它。
9. 在 **MainPage.xaml** XML 代码中，将 `<Grid>` 节替换为以下代码：此代码添加供用户进行身份验证的用户名和密码文本框。 它还会添加通知消息的文本框，以及应接收通知的用户名标记：

    ```xml   
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>

        <TextBlock Grid.Row="0" Text="Notify Users" HorizontalAlignment="Center" FontSize="48"/>

        <StackPanel Grid.Row="1" VerticalAlignment="Center">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition></ColumnDefinition>
                    <ColumnDefinition></ColumnDefinition>
                    <ColumnDefinition></ColumnDefinition>
                </Grid.ColumnDefinitions>
                <TextBlock Grid.Row="0" Grid.ColumnSpan="3" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="UsernameTextBox" Grid.Row="1" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
                <TextBlock Grid.Row="2" Grid.ColumnSpan="3" Text="Password" FontSize="24" Margin="20,0,20,0" />
                <PasswordBox Name="PasswordTextBox" Grid.Row="3" Grid.ColumnSpan="3" Margin="20,0,20,0"/>

                <Button Grid.Row="4" Grid.ColumnSpan="3" HorizontalAlignment="Center" VerticalAlignment="Center"
                            Content="1. Login and register" Click="LoginAndRegisterClick" Margin="0,0,0,20"/>

                <ToggleButton Name="toggleWNS" Grid.Row="5" Grid.Column="0" HorizontalAlignment="Right" Content="WNS" IsChecked="True" />
                <ToggleButton Name="toggleGCM" Grid.Row="5" Grid.Column="1" HorizontalAlignment="Center" Content="GCM" />
                <ToggleButton Name="toggleAPNS" Grid.Row="5" Grid.Column="2" HorizontalAlignment="Left" Content="APNS" />

                <TextBlock Grid.Row="6" Grid.ColumnSpan="3" Text="Username Tag To Send To" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="ToUserTagTextBox" Grid.Row="7" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                <TextBlock Grid.Row="8" Grid.ColumnSpan="3" Text="Enter Notification Message" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="NotificationMessageTextBox" Grid.Row="9" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                <Button Grid.Row="10" Grid.ColumnSpan="3" HorizontalAlignment="Center" Content="2. Send push" Click="PushClick" Name="SendPushButton" />
            </Grid>
        </StackPanel>
    </Grid>
    ```
11. 在“解决方案资源管理器”中，打开“**(Windows 8.1)**”和“**(Windows Phone 8.1)**”项目的 **MainPage.xaml.cs** 文件。 在这两个文件顶部添加以下 `using` 语句：

    ```csharp    
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Windows.Networking.PushNotifications;
    using Windows.UI.Popups;
    using System.Threading.Tasks;
    ```
12. 在 **WindowsApp** 项目的 **MainPage.xaml.cs** 中，将以下成员添加到 `MainPage` 类。 确保使用前面获取的实际后端终结点来替换 `<Enter Your Backend Endpoint>`。 例如，`http://mybackend.azurewebsites.net`。
    
    ```csharp
    private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
    ```
13. 将以下代码添加到“**(Windows 8.1)**”和“**(Windows Phone 8.1)**”项目的 **MainPage.xaml.cs** 中的 MainPage 类。
    
    `PushClick` 方法是“**发送推送**”按钮的单击处理程序。 它调用后端以触发向用户名标记与 `to_tag` 参数匹配的所有设备发送通知。 通知消息作为请求正文中的 JSON 内容发送。
    
    `LoginAndRegisterClick` 方法是“登录并注册”按钮的单击处理程序。 它在本地存储中存储基本身份验证令牌（代表身份验证方案使用的任何令牌），然后使用 `RegisterClient` 来通过后端注册通知。

    ```csharp
    private async void PushClick(object sender, RoutedEventArgs e)
    {
        if (toggleWNS.IsChecked.Value)
        {
            await sendPush("wns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
        }
        if (toggleGCM.IsChecked.Value)
        {
            await sendPush("gcm", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
        }
        if (toggleAPNS.IsChecked.Value)
        {
            await sendPush("apns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);

        }
    }

    private async Task sendPush(string pns, string userTag, string message)
    {
        var POST_URL = BACKEND_ENDPOINT + "/api/notifications?pns=" +
            pns + "&to_tag=" + userTag;

        using (var httpClient = new HttpClient())
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                MessageDialog alert = new MessageDialog(ex.Message, "Failed to send " + pns + " message");
                alert.ShowAsync();
            }
        }
    }

    private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
    {
        SetAuthenticationTokenInLocalStorage();

        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // The "username:<user name>" tag gets automatically added by the message handler in the backend.
        // The tag passed here can be whatever other tags you may want to use.
        try
        {
            // The device handle used is different depending on the device and PNS. 
            // Windows devices use the channel uri as the PNS handle.
            await new RegisterClient(BACKEND_ENDPOINT).RegisterAsync(channel.Uri, new string[] { "myTag" });

            var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            SendPushButton.IsEnabled = true;
        }
        catch (Exception ex)
        {
            MessageDialog alert = new MessageDialog(ex.Message, "Failed to register with RegisterClient");
            alert.ShowAsync();
        }
    }

    private void SetAuthenticationTokenInLocalStorage()
    {
        string username = UsernameTextBox.Text;
        string password = PasswordTextBox.Password;

        var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
        ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
    }
    ```
1. 打开 **App.xaml.cs** 文件。 在 `OnLaunched()` 事件处理程序中，查找对 `InitNotificationsAsync()` 的调用。 注释掉或删除对 `InitNotificationsAsync()` 的调用。 按钮处理程序会初始化通知注册。

    ```csharp
    protected override void OnLaunched(LaunchActivatedEventArgs e)
    {
        //InitNotificationsAsync();
    ```
1. 右键单击“WindowsApp”项目，单击“添加”，然后单击“类”。 将类命名为 **RegisterClient.cs**，然后单击“**确定**”以生成该类。
   
   此类会包装联系应用后端所需的 REST 调用，以便注册推送通知。 它还会在本地存储通知中心创建的 *registrationIds*，如[从应用后端注册](http://msdn.microsoft.com/library/dn743807.aspx)中所述。 它使用单击“登录并注册”按钮时存储在本地存储中的授权令牌。
2. 在 RegisterClient.cs 文件的顶部添加以下 `using` 语句：

    ```csharp   
    using Windows.Storage;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using System.Threading.Tasks;
    using System.Linq;
    ```
3. 在 `RegisterClient` 类定义中添加以下代码：
   
    ```csharp
    private string POST_URL;

    private class DeviceRegistration
    {
        public string Platform { get; set; }
        public string Handle { get; set; }
        public string[] Tags { get; set; }
    }

    public RegisterClient(string backendEndpoint)
    {
        POST_URL = backendEndpoint + "/api/register";
    }

    public async Task RegisterAsync(string handle, IEnumerable<string> tags)
    {
        var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();

        var deviceRegistration = new DeviceRegistration
        {
            Platform = "wns",
            Handle = handle,
            Tags = tags.ToArray<string>()
        };

        var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);

        if (statusCode == HttpStatusCode.Gone)
        {
            // regId is expired, deleting from local storage & recreating
            var settings = ApplicationData.Current.LocalSettings.Values;
            settings.Remove("__NHRegistrationId");
            regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
            statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
        }

        if (statusCode != HttpStatusCode.Accepted && statusCode != HttpStatusCode.OK)
        {
            // log or throw
            throw new System.Net.WebException(statusCode.ToString());
        }
    }

    private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
    {
        using (var httpClient = new HttpClient())
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

            var putUri = POST_URL + "/" + regId;

            string json = JsonConvert.SerializeObject(deviceRegistration);
                            var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
    {
        var settings = ApplicationData.Current.LocalSettings.Values;
        if (!settings.ContainsKey("__NHRegistrationId"))
        {
            using (var httpClient = new HttpClient())
            {
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                if (response.IsSuccessStatusCode)
                {
                    string regId = await response.Content.ReadAsStringAsync();
                    regId = regId.Substring(1, regId.Length - 2);
                    settings.Add("__NHRegistrationId", regId);
                }
                else
                {
                    throw new System.Net.WebException(response.StatusCode.ToString());
                }
            }
        }
        return (string)settings["__NHRegistrationId"];

    }
    ```
4. 保存所有更改。

## <a name="test-the-application"></a>测试应用程序
1. 在两种版本的 Windows 上启动应用程序。
2. 输入“用户名”和“密码”，如以下屏幕所示。 这应该与在 Windows Phone 上输入的用户名和密码不同。
3. 单击“**登录并注册**”，然后验证是否会确认显示已登录的对话框。 此代码也会启用“发送推送”按钮。
   
    ![][14]
5. 然后在“收件人用户名标记”字段中输入注册的用户名。 输入通知消息，并单击“**发送推送**”。
6. 只有已使用匹配用户名标记进行注册的设备才会收到通知消息。
   
    ![][15]

## <a name="next-steps"></a>后续步骤
本教程介绍了如何向其标记与注册相关联的特定用户推送通知。 若要了解如何推送基于位置的通知，请转到以下教程： 

> [!div class="nextstepaction"]
>[推送基于位置的通知](notification-hubs-push-bing-spartial-data-geofencing-notification.md)

[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png



<!-- URLs. -->
[Get started with Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Secure Push]: notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
