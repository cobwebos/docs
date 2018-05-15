---
title: 向特定设备（通用 Windows 平台）发送通知 | Microsoft Docs
description: 将 Azure 通知中心与注册中的标记配合使用，将突发新闻发送到通用 Windows 平台应用。
services: notification-hubs
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 9b9e3b910162653c14c398e2c3392709abcd5fd8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-push-notifications-to-specific-windows-devices-running-universal-windows-platform-applications"></a>教程：向运行通用 Windows 平台应用程序的特定 Windows 设备推送通知
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>概述
本教程演示如何使用 Azure 通知中心将突发新闻通知广播到 Windows 应用商店或 Windows Phone 8.1（非 Silverlight）应用程序。 如果要以 Windows Phone 8.1 Silverlight 为目标，请参阅 [Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) 版本。 

本教程介绍如何使用 Azure 通知中心将通知推送到运行通用 Windows 平台 (UWP) 应用程序的特定 Windows 设备。 完成本教程后，可注册感兴趣的突发新闻类别，之后你仅会收到这些类别的推送通知。 

在通知中心创建注册时，通过加入一个或多个*标记*来启用广播方案。 将通知发送到标记时，已注册该标记的所有设备将接收通知。 有关标记的详细信息，请参阅[注册中的标记](notification-hubs-tags-segment-push-message.md)。

> [!NOTE]
> Visual Studio 2017 中不支持 Windows 应用商店和 Windows Phone 项目 8.1 及更早的版本。 有关详细信息，请参阅 [Visual Studio 2017 平台目标以及兼容性](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs)。 

在本教程中，请执行以下步骤：

> [!div class="checklist"]
> * 向移动应用添加类别选择
> * 注册通知
> * 发送带标记的通知
> * 运行应用并生成通知

## <a name="prerequisites"></a>先决条件
在开始本教程之前完成[教程：使用 Azure 通知中心向通用 Windows 平台应用发送通知][get-started]。  

## <a name="add-category-selection-to-the-app"></a>向应用程序中添加类别选择
第一步是向现有主页添加 UI 元素，以便用户选择要注册的类别。 所选类别会存储在设备上。 应用启动时，系统会将所选类别当做标记在通知中心创建设备注册。

1. 打开 MainPage.xaml 项目文件，并复制 Grid 元素中的以下代码：
   
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition/>
                <ColumnDefinition/>
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
        </Grid>

2. 在“解决方案资源管理器”中，右键单击项目，然后添加新类：**Notifications**。 向类定义添加 **public** 修饰符，然后将以下 **using** 语句添加到新的代码文件：

    ```csharp   
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

3. 将以下代码复制到新的 Notifications 类中：
   
    ```csharp
    private NotificationHub hub;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        return await SubscribeToCategories(categories);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
        return categories != null ? categories.Split(','): new string[0];
    }

    public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                categories);
    }
    ```
   
    此类使用本地存储区存储此设备必须接收的新闻类别。 使用模板注册来注册类别时，应调用 RegisterTemplateAsync，而不能调用 RegisterNativeAsync。 
   
    如果需要注册多个模板（例如一个模板针对 toast 通知，一个模板针对磁贴），请提供模板名称（例如“simpleWNSTemplateExample”）。 为模板命名以便更新或删除模板。
   
    >[!NOTE]
    >如果一个设备使用同一标记注册多个模板，针对该标记的传入消息会导致系统向该设备发送多个通知（每个通知对应一个模板）。 当同一逻辑消息必定导致多个可视化通知时（例如，在 Windows 应用商店应用程序中同时显示徽章和 toast），此行为很有用。
   
    有关详细信息，请参阅[模板](notification-hubs-templates-cross-platform-push-messages.md)。

4. 在 App.xaml.cs 项目文件中，将以下属性添加到 **App** 类：

    ```csharp   
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```
   
    使用此属性创建和访问 Notifications 实例。
   
    在代码中，将 `<hub name>` 和 `<connection string with listen access>` 占位符替换为通知中心的名称和之前获取的 *DefaultListenSharedAccessSignature* 的连接字符串。
   
   > [!NOTE]
   > 使用客户端应用分发的凭据通常不安全，因此请使用客户端应用仅分发具有侦听访问权限的密钥。 拥有侦听访问权限后，应用可注册通知，但是无法修改现有注册，也无法发送通知。 在受保护的后端服务中使用完全访问权限密钥，以便发送通知和更改现有注册。
   > 
   > 
5. 在 MainPage.xaml.cs 项目文件中添加以下行：
   
    ```csharp
    using Windows.UI.Popups;
    ```

6. 在 MainPage.xaml.cs 项目文件中，添加以下方法：
   
    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```

    此方法创建一个类别列表并使用 Notifications 类将该列表存储在本地存储中。 它还会向通知中心注册相应的标记。 类别有更改时，会使用新类别重新创建注册。

现在，应用可在设备的本地存储中存储一组类别。 每当用户更改类别选择时，应用都会向通知中心注册。

## <a name="register-for-notifications"></a>注册通知
本部分中将使用存储在本地存储中的类别，在启动时向通知中心注册。

> [!NOTE]
> 由于 Windows 通知服务 (WNS) 分配的通道 URI 随时可能更改，因此应该经常注册通知以避免通知失败。 此示例在每次应用程序启动时注册通知。 对于经常运行（一天一次以上）的应用，如果距上次注册时间不到一天，可以跳过注册，以节省带宽。
> 
> 

1. 若要使用 `notifications` 类基于类别订阅，请打开 App.xaml.cs 文件，然后更新 InitNotificationsAsync 方法。
   
    ```csharp
    // *** Remove or comment out these lines *** 
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
   ```
    此过程可确保应用启动时会从本地存储区检索类别并请求注册这些类别。 可在[通知中心入门][get-started]教程中创建 InitNotificationsAsync 方法。
2. 在 MainPage.xaml.cs 项目文件的 *OnNavigatedTo* 方法中添加以下代码：
   
    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;
    }
    ```

    此代码基于以前保存的类别状态更新主页。

应用现已完成。 它可以在设备的本地存储中存储一组类别，当用户更改类别选择时，使用它向通知中心注册。 下一部分将定义一个后端，该后端可将类别通知发送到此应用。

## <a name="send-tagged-notifications"></a>发送带标记的通知
[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-app-and-generate-notifications"></a>运行应用并生成通知
1. 在 Visual Studio 中，选择 F5 编译并启动应用。 应用 UI 提供了一组开关，可以使用它们选择要订阅的类别。 
   
    ![突发新闻应用][1]

2. 启用一个或多个类别切换，然后单击“订阅”。
   
    应用程序将所选类别转换为标签并针对所选标签从通知中心请求注册新设备。 返回注册的类别并显示在对话框中。
   
    ![类别切换和订阅按钮][19]

3. 使用以下方式之一从后端发送新通知：

   * 控制台应用：启动控制台应用。
   * Java/PHP：运行应用或脚本。
     
     所选类别的通知作为 toast 通知显示。
     
     ![Toast 通知][14]

## <a name="next-steps"></a>后续步骤
我们已通过本文了解了如何按类别广播突发新闻。 后端应用程序将带标记的通知推送到特定设备，这些设备已注册接收该标记的通知。 若要了解如何向特定用户推送通知而不管这些用户使用什么设备，请转到以下教程：

> [!div class="nextstepaction"]
> [推送本地化通知](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification
[Use Notification Hubs to broadcast localized breaking news]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
