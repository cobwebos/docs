---
title: 使用 Azure 通知中心向 Windows 应用发送本地化通知 | Microsoft Docs
description: 了解如何使用 Azure 通知中心发送本地化的突发新闻通知。
services: notification-hubs
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: c454f5a3-a06b-45ac-91c7-f91210889b25
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 517e7ae3871a1ed816ea407ad47c9033a1bb5a0e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776883"
---
# <a name="tutorial-push-localized-notifications-to-windows-apps-by-using-azure-notification-hubs"></a>教程：使用 Azure 通知中心向 Windows 应用推送本地化通知
> [!div class="op_single_selector"]
> * [Windows 应用商店 C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

## <a name="overview"></a>概述
本教程介绍如何将本地化通知推送到已注册到通知中心服务的移动设备。 在本教程中，请更新在[教程：向特定设备（通用 Windows 平台）发送通知](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)中创建的应用程序，使之支持以下方案： 

- Windows 应用商店应用允许客户端设备指定一种语言并订阅不同的突发新闻类别。
- 后端应用使用 Azure 通知中心的**标记**和**模板**功能来广播通知。

完成本教程后，即可通过移动应用程序注册感兴趣的类别，并指定接收通知的语言。 后端应用程序可发送按语言和设备本地化的通知。 

本教程介绍如何执行下列操作： 

> [!div class="checklist"]
> * 更新 Windows 应用，使之支持区域设置信息
> * 更新后端应用，以便发送本地化通知
> * 测试应用程序

## <a name="prerequisites"></a>先决条件
完成[教程：向特定设备（通用 Windows 平台）发送通知](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)。 

在[教程：向特定设备（通用 Windows 平台）发送通知](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)中，已开发一个使用**标记**订阅不同新闻**类别**通知的应用。 在本教程中，请使用通知中心的**模板**功能轻松传递**本地化**突发新闻通知。

在高级别，模板是指定特定设备接收通知的格式的一种方法。 模板通过引用作为应用程序后端所发消息的一部分的属性，指定确切的负载格式。 在本教程中，后端应用程序会发送一条与区域设置无关的消息，其中包含所有支持的语言：

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

设备注册到引用正确属性的模板。 例如，要接收英文 toast 消息的 Windows 应用商店应用会注册以下包含任何相应标记的模板：

```xml
<toast>
    <visual>
    <binding template=\"ToastText01\">
        <text id=\"1\">$(News_English)</text>
    </binding>
    </visual>
</toast>
```

若要详细了解模板，请参阅[模板](notification-hubs-templates-cross-platform-push-messages.md)一文。 

## <a name="update-windows-app-to-support-locale-information"></a>更新 Windows 应用，使之支持区域设置信息

1. 打开为[教程：向特定设备（通用 Windows 平台）发送通知](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)创建的 Visual Studio 解决方案。 
2. 更新 **MainPage.xaml**，使之包含区域设置组合框：

    ```xml
    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>
    ```
2. 在 **Notifications** 类中，将一个区域设置参数添加到 **StoreCategoriesAndSubscribe** 和 **SubscribeToCateories** 方法。

    ```csharp   
    public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        ApplicationData.Current.LocalSettings.Values["locale"] = locale;
        return await SubscribeToCategories(categories);
    }

    public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration. This makes supporting notifications across other platforms much easier.
        // Using the localized tags based on locale selected.
        string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
    }
    ```

    请调用 *RegisterTemplateAsync* 而非 *RegisterNativeAsync* 方法。 请注册一个具体的通知格式，采用此通知格式的模板依赖于区域设置。 另请提供模板名称（“localizedWNSTemplateExample”），因为可能需要注册多个模板（例如，一个模板用于 toast 通知，另一个模板用于磁贴）。 另外，为了便于更新或删除，也需要为其命名。
   
    如果一个设备使用同一标记注册多个模板，针对该标记的传入消息会导致多个通知发送到设备（一个通知对应一个模板）。 当同一逻辑消息必须导致多个可视通知时，此行为很有用，例如在 Windows 应用商店应用程序显示徽章和 toast。
3. 添加以下方法来检索存储的区域设置：
   
    ```csharp
    public string RetrieveLocale()
    {
        var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
        return locale != null ? locale : "English";
    }
    ```

4. 在 **MainPage.xaml.cs** 中，通过检索“区域设置”组合框的当前值并将它提供给对 Notifications 类的调用，更新按钮单击处理程序，如下所示：
   
    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var locale = (string)Locale.SelectedItem;

        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                categories);

        var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
            string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```
4. 最后，在 App.xaml.cs 文件中，确保更新 `InitNotificationsAsync` 方法以检索区域设置，并在订阅时使用该区域设置：

    ```csharp   
    private async void InitNotificationsAsync()
    {
        var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

## <a name="send-localized-notifications-from-your-back-end"></a>从后端发送本地化的通知
发送模板通知时，只需提供一组属性。在本教程中，后端应用程序发送一组包含当前新闻的本地化版本的属性，例如：

```json
{
    "News_English": "World News in English!",
    "News_French": "World News in French!",
    "News_Mandarin": "World News in Mandarin!"
}
```

在本部分，请在解决方案中更新控制台应用程序项目。 使用以下代码修改前面创建的控制台应用中的 `SendTemplateNotificationAsync` 方法： 

> [!IMPORTANT]
> 在代码中指定通知中心的名称和具有完全访问权限的连接字符串。 


```csharp
private static async void SendTemplateNotificationAsync()
{
    // Define the notification hub.
    NotificationHubClient hub = 
        NotificationHubClient.CreateClientFromConnectionString(
            "<connection string with full access>", "<hub name>");

    // Sending the notification as a template notification. All template registrations that contain 
    // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
    // This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string, string> templateParams = new Dictionary<string, string>();

    // Create an array of breaking news categories.
    var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
    var locales = new string[] { "English", "French", "Mandarin" };

    foreach (var category in categories)
    {
        templateParams["messageParam"] = "Breaking " + category + " News!";

        // Sending localized News for each tag too...
        foreach( var locale in locales)
        {
            string key = "News_" + locale;

            // Your real localized news content would go here.
            templateParams[key] = "Breaking " + category + " News in " + locale + "!";
        }

        await hub.SendTemplateNotificationAsync(templateParams, category);
    }
}
```

不管平台如何，此简单调用都会将本地化的新闻片段传送到**所有**设备，因为通知中心会生成正确的本机有效负载并将其传送到已订阅特定标记的所有设备。

## <a name="test-the-app"></a>测试应用程序
1. 运行通用 Windows 应用商店应用程序。 等待“注册成功”消息出现。

    ![移动应用程序和注册](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/registration-successful.png)
1. 选择**类别**和**区域设置**，然后单击“订阅”。 应用程序将所选类别转换为标签并针对所选标签从通知中心请求注册新设备。

    ![移动应用程序](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/mobile-app.png)
2.  此时会看到有关**订阅**的**确认**消息。 

    ![订阅消息](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/subscription-message.png)
1. 在收到确认后，请运行**控制台应用**，以发送每个类别和每种支持语言的通知。 验证是否仅接收已订阅类别的通知，以及消息是否对应于所选区域设置。 

    ![通知消息](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/notifications.png)
 

## <a name="next-steps"></a>后续步骤
本教程介绍了如何向其标记与注册相关联的特定设备推送本地化通知。 若要了解如何向可能会使用多个设备的特定用户推送通知，请转到以下教程： 

> [!div class="nextstepaction"]
>[向特定用户推送通知](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)


<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[Use Notification Hubs to send breaking news]: /notification-hubs/notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
