---
title: 使用 Azure 通知中心向特定 Windows Phone 推送通知 | Microsoft Docs
description: 本教程介绍如何使用 Azure 通知中心将通知推送到注册到应用程序后端的特定（并非所有）Windows Phone 8 或 Windows Phone 8.1 设备。
services: notification-hubs
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 42726bf5-cc82-438d-9eaa-238da3322d80
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: c61a6efaa4a56636400acfe5a212cddad47f4f0c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38652739"
---
# <a name="tutorial-push-notifications-to-specific-windows-phone-devices-by-using-azure-notification-hubs"></a>教程：使用 Azure 通知中心向特定 Windows Phone 设备推送通知
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

本教程演示如何使用 Azure 通知中心将推送通知发送到特定 Windows Phone 8 或 Windows Phone 8.1 设备。 如果要以 Windows Phone 8.1（非 Silverlight）为目标，请参阅本教程的 [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) 版本。

在通知中心创建注册时，请通过加入一个或多个标记来启用此方案。 将通知发送到标记时，已注册该标记的所有设备将接收通知。 有关标记的详细信息，请参阅[注册中的标记](notification-hubs-tags-segment-push-message.md)。

> [!NOTE]
> 通知中心 Windows Phone SDK 不支持将 Windows 推送通知服务 (WNS) 与 Windows Phone 8.1 Silverlight 应用配合使用。 若要将 WNS（而不是 MPNS）与 Windows Phone 8.1 Silverlight 应用配合使用，请遵循使用 REST API 的[通知中心 - Windows Phone Silverlight 教程]。

本教程介绍如何执行以下操作： 

> [!div class="checklist"]
> * 向移动应用添加类别选择
> * 注册带标记的通知
> * 发送带标记的通知
> * 测试应用程序

## <a name="prerequisites"></a>先决条件
完成[教程：使用 Azure 通知中心向 Windows Phone 应用推送通知](notification-hubs-windows-mobile-push-notifications-mpns.md)。 在本教程中，请更新移动应用程序，以便注册感兴趣的突发新闻类别，只接收这些类别的推送通知。 

## <a name="add-category-selection-to-the-mobile-app"></a>向移动应用添加类别选择
第一步是向现有主页添加 UI 元素，这些元素允许用户选择要注册的类别。 用户选择的类别存储在设备上。 应用程序启动时，使用所选类别作为标签在通知中心创建设备注册。

1. 打开 MainPage.xaml 项目文件，并使用以下代码替换名为 `TitlePanel` 和 `ContentPanel` 的 **Grid** 元素：
   
        <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
            <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
            <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
        </StackPanel>
   
        <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="auto"/>
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition />
                <ColumnDefinition />
            </Grid.ColumnDefinitions>
            <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
            <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
            <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
            <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
            <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
            <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
        </Grid>
2. 向项目添加名为 **Notifications** 的类。 向类定义添加 **public** 修饰符。 然后，向新的代码文件添加以下 **using** 语句：
   
    ```csharp
    using Microsoft.Phone.Notification;
    using Microsoft.WindowsAzure.Messaging;
    using System.IO.IsolatedStorage;
    using System.Windows;
    ```
1. 将以下代码添加到新的 **Notifications** 类：
   
    ```csharp
    private NotificationHub hub;

    // Registration task to complete registration in the ChannelUriUpdated event handler
    private TaskCompletionSource<Registration> registrationTask;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
        return categories != null ? categories.Split(',') : new string[0];
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        var categoriesAsString = string.Join(",", categories);
        var settings = IsolatedStorageSettings.ApplicationSettings;
        if (!settings.Contains("categories"))
        {
            settings.Add("categories", categoriesAsString);
        }
        else
        {
            settings["categories"] = categoriesAsString;
        }
        settings.Save();

        return await SubscribeToCategories();
    }

    public async Task<Registration> SubscribeToCategories()
    {
        registrationTask = new TaskCompletionSource<Registration>();

        var channel = HttpNotificationChannel.Find("MyPushChannel");

        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
            channel.ChannelUriUpdated += channel_ChannelUriUpdated;

            // This is optional, used to receive notifications while the app is running.
            channel.ShellToastNotificationReceived += channel_ShellToastNotificationReceived;
        }

        // If channel.ChannelUri is not null, complete the registrationTask here.  
        // If it is null, the registrationTask will be completed in the ChannelUriUpdated event handler.
        if (channel.ChannelUri != null)
        {
            await RegisterTemplate(channel.ChannelUri);
        }

        return await registrationTask.Task;
    }

    async void channel_ChannelUriUpdated(object sender, NotificationChannelUriEventArgs e)
    {
        await RegisterTemplate(e.ChannelUri);
    }

    async Task<Registration> RegisterTemplate(Uri channelUri)
    {
        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyMPNS = "<wp:Notification xmlns:wp=\"WPNotification\">" +
                                            "<wp:Toast>" +
                                                "<wp:Text1>$(messageParam)</wp:Text1>" +
                                            "</wp:Toast>" +
                                        "</wp:Notification>";

        // The stored categories tags are passed with the template registration.

        registrationTask.SetResult(await hub.RegisterTemplateAsync(channelUri.ToString(), 
            templateBodyMPNS, "simpleMPNSTemplateExample", this.RetrieveCategories()));

        return await registrationTask.Task;
    }

    // This is optional. It is used to receive notifications while the app is running.
    void channel_ShellToastNotificationReceived(object sender, NotificationEventArgs e)
    {
        StringBuilder message = new StringBuilder();
        string relativeUri = string.Empty;

        message.AppendFormat("Received Toast {0}:\n", DateTime.Now.ToShortTimeString());

        // Parse out the information that was part of the message.
        foreach (string key in e.Collection.Keys)
        {
            message.AppendFormat("{0}: {1}\n", key, e.Collection[key]);

            if (string.Compare(
                key,
                "wp:Param",
                System.Globalization.CultureInfo.InvariantCulture,
                System.Globalization.CompareOptions.IgnoreCase) == 0)
            {
                relativeUri = e.Collection[key];
            }
        }

        // Display a dialog of all the fields in the toast.
        System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() => 
        { 
            MessageBox.Show(message.ToString()); 
        });
    }
    ```
    
    此类使用隔离存储区存储此设备要接收的新闻类别。 它还包含用于通过[模板](notification-hubs-templates-cross-platform-push-messages.md)通知注册来注册这些类别的方法。
1. 在 App.xaml.cs 项目文件中，将以下属性添加到 **App** 类： 将 `<hub name>` 和 `<connection string with listen access>` 占位符替换为通知中心名称和前面获取的 *DefaultListenSharedAccessSignature* 的连接字符串。
   
    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

   > [!NOTE]
   > 由于使用客户端应用程序分发的凭据通常是不安全的，只应使用客户端应用程序分发具有侦听访问权限的密钥。 侦听访问权限允许应用程序注册通知，但是无法修改现有注册，也无法发送通知。 在受保护的后端服务中使用完全访问权限密钥，以便发送通知和更改现有注册。
   > 
   > 
2. 在 MainPage.xaml.cs 中，添加以下行：
   
    ```csharp
    using Windows.UI.Popups;
    ```
1. 在 MainPage.xaml.cs 项目文件中，添加以下方法：
   
    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldCheckBox.IsChecked == true) categories.Add("World");
        if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
        if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
        if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
        if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
        if (SportsCheckBox.IsChecked == true) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        MessageBox.Show("Subscribed to: " + string.Join(",", categories) + " on registration id : " +
            result.RegistrationId);
    }
    ```
   
    此方法创建一个类别列表并使用 **Notifications** 类将该列表存储在本地存储区中，将相应的标签注册到通知中心。 更改类别时，使用新类别重新创建注册。

应用程序现在可以将一组类别存储在设备的本地存储区中了，每当用户更改所选类别时，会将这些类别注册到通知中心。

## <a name="register-for-notifications"></a>注册通知
这些步骤用于在启动时会在本地存储区中存储的类别注册到通知中心。

> [!NOTE]
> 由于 Microsoft 推送通知服务 (MPNS) 分配的通道 URI 随时可能更改，因此，应该经常注册通知以避免通知失败。 此示例在每次应用程序启动时注册通知。 对于经常运行（一天一次以上）的应用程序，如果每次注册间隔时间不到一天，可以跳过注册来节省带宽。

1. 打开 App.xaml.cs 文件，将 **async** 修饰符添加到 **Application_Launching** 方法，并会在[通知中心入门]中添加的通知中心注册代码替换为以下代码：
   
    ```csharp
    private async void Application_Launching(object sender, LaunchingEventArgs e)
    {
        var result = await notifications.SubscribeToCategories();
    
        if (result != null)
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration Id :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
    }
    ```
   
    此代码确保每次应用程序启动时，它从本地存储检索类别并请求注册这些类别。
2. 在 MainPage.xaml.cs 项目文件中，添加实现 **OnNavigatedTo** 方法的以下代码：
   
    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();
    
        if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
        if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
        if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
        if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
        if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
        if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
    }
    ```
   
    此代码基于以前保存的类别状态更新主页。

应用程序现在已完成，可以在设备的本地存储区中存储一组类别了，每当用户更改所选类别时会使用这些类别注册到通知中心。 接下来，请定义一个后端，以便将类别通知发送到此应用。

## <a name="send-tagged-notifications"></a>发送带标记的通知
[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>测试应用程序
1. 在 Visual Studio 中，按 F5 编译并启动应用程序。
   
    ![包含类别的移动应用][1]
   
    应用 UI 提供了一组开关，可以使用它们选择要订阅的类别。
2. 启用一个或多个类别开关，并单击“**订阅**”。
   
    应用程序将所选类别转换为标签并针对所选标签从通知中心请求注册新设备。 返回注册的类别并显示在对话框中。
   
    ![订阅的消息][2]
3. 在接收类别已完成订阅的确认后，运行控制台应用以发送每个类别的通知。 验证是否仅接收已订阅类别的通知。
   
    ![通知消息][3]

## <a name="next-steps"></a>后续步骤
本教程介绍了如何向其标记与注册相关联的特定设备推送通知。 若要了解如何向可能会使用多个设备的特定用户推送通知，请转到以下教程： 

> [!div class="nextstepaction"]
>[向特定用户推送通知](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)


<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png



<!-- URLs.-->
[通知中心入门]: /manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Use Notification Hubs to broadcast localized breaking news]: ../breakingnews-localized-wp8.md
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users/
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Phone]: ??

