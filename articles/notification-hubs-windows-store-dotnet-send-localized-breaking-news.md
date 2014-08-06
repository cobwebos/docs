<properties linkid="develop-notificationhubs-tutorials-send-localized-breaking-news-windowsdotnet" urlDisplayName="本地化的突发新闻" pageTitle="通知中心本地化的突发新闻教程" metaKeywords="" description="了解如何使用 Windows Azure Service Bus 通知中心发送本地化的突发新闻通知。" metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="使用通知中心发送本地化的突发新闻" authors="ricksal" solutions="" manager="" editor="" />
# 使用通知中心发送本地化的突发新闻

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/zh-cn/manage/services/notification-hubs/breaking-news-localized-dotnet" title="Windows 应用商店 C#" class="current">Windows 应用商店 C#</a><a href="/zh-cn/manage/services/notification-hubs/breaking-news-localized-ios" title="iOS">iOS</a>
</div>

本主题演示如何使用 Azure 通知中心的**模板**功能广播已按语言和设备本地化的突发新闻通知。在本教程中，您从在[使用通知中心发送突发新闻]中创建的 Windows 应用商店应用程序开始操作。完成时，您将可以注册感兴趣的突发新闻类别，指定要接收通知的语言并仅接收采用该语言的这些类别的推送通知。

本教程将指导您完成启用此方案的以下基本步骤：

1. [模板概念] 
2. [应用程序用户界面]
3. [构建 Windows 应用商店客户端应用程序]
4. [从后端发送通知]


此方案包含两个部分：

- Windows 应用商店应用程序允许客户端设备指定一种语言并订阅不同的突发新闻类别；

- 后端使用 Azure 通知中心的**标签**和**模板**功能广播通知。



##前提条件##

您必须已完成学习[使用通知中心发送突发新闻]教程并具有可用的代码，因为本教程直接围绕该代码展开论述。

您还需要 Visual Studio 2012。


<h2><a name="concepts"></a><span class="short-header">概念</span>模板概念</h2>

在[使用通知中心发送突发新闻]中，您构建了一个使用**标签**订阅不同新闻类别通知的应用程序。
但是，很多应用程序针对多个市场，需要本地化。这意味着通知内容本身必须本地化且传递到正确的设备组。
在本主题中，我们将演示如何使用通知中心的**模板**功能轻松传递本地化的突发新闻通知。

注意：发送本地化的通知的一种方式是创建每个标签的多个版本。例如，要支持英语、法语和汉语，我们需要三种不同的标签用于世界新闻：“world_en”、“world_fr”和“world_ch”。我们然后必须将世界新闻的本地化版本分别发送到这些标签。在本主题中，我们使用模板来避免增生标签和发送多个消息的要求。

在较高级别上，模板是指定特定设备应如何接收通知的一种方法。模板通过引用作为您应用程序后端所发消息的一部分的属性，指定确切的负载格式。在我们的示例中，我们将发送包含所有支持的语言的区域设置未知的消息：

	{
		"News_English": "...",
		"News_French": "...",
		"News_Mandarin": "..."
	}

然后我们将确保设备注册到引用正确属性的模板。例如，要接收简单的 toast 消息的 Windows 应用商店应用程序将注册以下模板：

	<toast>
	  <visual>
	    <binding template=\"ToastText01\">
	      <text id=\"1\">$(News_English)</text>
	    </binding>
	  </visual>
	</toast>



模板是很强大的功能，您可以在[通知中心指南]一文中了解其更多信息。一个模板表达语言的参考是[针对 Windows 应用商店的通知中心操作指南]。


<h2><a name="ui"></a><span class="short-header">应用程序 ui</span>应用程序用户界面</h2>

我们现在将修改您在[使用通知中心发送突发新闻]主题中创建的“突发新闻”应用程序，以使用模板发送本地化的突发新闻。


为了改编您的客户端应用程序以接收本地化的消息，您必须使用模板注册替换*本机*注册（即未指定模板的注册）。


在 Windows 应用商店应用程序中：

更改 MainPage.xaml 以包含区域设置组合框：

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
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="Button_Click" />
    </Grid>

<h2><a name="building-client"></a><span class="building app">App ui</span>Building the Windows Store client app</h2>

1. 在 Notifications 类中，将一个区域设置参数添加到 *StoreCategoriesAndSubscribe*  和 *SubscribeToCateories* 方法。

		public async Task StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            await SubscribeToCategories(locale, categories);
        }

        public async Task SubscribeToCategories(string locale, IEnumerable<string> categories)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            var template = String.Format(@"<toast><visual><binding template=""ToastText01""><text id=""1"">$(News_{0})</text></binding></visual></toast>", locale);

            await hub.RegisterTemplateAsync(channel.Uri, template, "newsTemplate", categories);
        }

	请注意，不是调用 *RegisterNativeAsync* 方法，我们调用的是 *RegisterTemplateAsync*：我们将注册特定的通知格式，在其中模板依赖于区域设置。我们还提供模板的名称（“newsTemplate”），因为我们可能要注册多个模板（例如一个用于 toast 通知，一个用于磁贴），需要命名它们以便可以更新或删除它们。

	请注意，如果一个设备使用同一标签注册多个模板，针对该标签的传入消息将导致多个通知发送到设备（每个通知对应一个模板）。当同一逻辑消息必须导致多个可视通知时，此行为很有用，例如在 Windows 应用商店应用程序显示徽章和 toast。

2. 添加以下方法来检索存储的区域设置：

		public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }

3. 在您的 MainPage.xaml.cs 中，通过检索“区域设置”组合框的当前值并将它提供给对 Notifications 类的调用，更新按钮单击处理程序，如下所示：

		 var locale = (string)Locale.SelectedItem;
            
         var categories = new HashSet<string>();
         if (WorldToggle.IsOn) categories.Add("World");
         if (PoliticsToggle.IsOn) categories.Add("Politics");
         if (BusinessToggle.IsOn) categories.Add("Business");
         if (TechnologyToggle.IsOn) categories.Add("Technology");
         if (ScienceToggle.IsOn) categories.Add("Science");
         if (SportsToggle.IsOn) categories.Add("Sports");

         await ((App)Application.Current).Notifications.StoreCategoriesAndSubscribe(locale, categories);

         var dialog = new MessageDialog(String .Format("Locale: {0}; Subscribed to: {1}", locale, string.Join(",", categories)));
         dialog.Commands.Add(new UICommand("OK"));
         await dialog.ShowAsync();

4. 最后，在 App.xaml.cs 文件中，确保在 *OnLaunched* 方法中更新对 
Notifications 单一实例的调用：

		Notifications.SubscribeToCategories(Notifications.RetrieveLocale(), Notifications.RetrieveCategories());


<h2><a name="send"></a><span class="short-header">发送本地化的通知</span>从后端发送本地化的通知</h2>

[WACOM.INCLUDE [notification-hubs-localized-back-end](../includes/notification-hubs-localized-back-end.md)]





## 后续步骤

有关使用模板的更多信息，请参见[使用通知中心通知用户：ASP.NET]、[使用通知中心通知用户：移动服务]以及[通知中心指南]。一个模板表达语言的参考是[针对 Windows 应用商店的通知中心操作指南]。

<!-- Anchors. -->
[模板概念]: #concepts
[应用程序用户界面]: #ui
[构建 Windows 应用商店客户端应用程序]: #building-client
[从后端发送通知]: #send
[后续步骤]:#next-steps

<!-- Images. -->





















<!-- URLs. -->
[移动服务]: /zh-cn/develop/mobile/tutorials/get-started
[使用通知中心通知用户：ASP.NET]: /zh-cn/manage/services/notification-hubs/notify-users-aspnet
[使用通知中心通知用户：移动服务]: /zh-cn/manage/services/notification-hubs/notify-users
[使用通知中心发送突发新闻]: /zh-cn/manage/services/notification-hubs/breaking-news-dotnet 

[“提交应用程序”页]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的应用程序]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[移动服务入门]: /zh-cn/develop/mobile/tutorials/get-started/#create-new-service
[开始使用数据]: /zh-cn/develop/mobile/tutorials/get-started-with-data-dotnet
[移动服务中身份验证入门]: /zh-cn/develop/mobile/tutorials/get-started-with-users-dotnet
[推送通知入门]: /zh-cn/develop/mobile/tutorials/get-started-with-push-dotnet
[向应用用户推送通知]: /zh-cn/develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[使用脚本为用户授权]: /zh-cn/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript 和 HTML]: /zh-cn/develop/mobile/tutorials/get-started-with-push-js

[Azure 管理门户]: https://manage.windowsazure.com/
[wns 对象]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[通知中心指南]: http://msdn.microsoft.com/zh-cn/library/jj927170.aspx
[针对 iOS 的通知中心操作指南]: http://msdn.microsoft.com/zh-cn/library/jj927168.aspx
[针对 Windows 应用商店的通知中心操作指南]: http://msdn.microsoft.com/zh-cn/library/jj927172.aspx

