



当你发送模板通知，只需提供一组属性时，在本例中我们将发送一组包含当前新闻的本地化的版本的实例的属性：

    {
        "News_English": "World News in English!",
        "News_French": "World News in French!",
        "News_Mandarin": "World News in Mandarin!"
    }


本部分演示如何使用控制台应用程序发送通知

包含的代码广播到 Windows 应用商店和 iOS 设备，因为该后端可广播到任何受支持的设备。

### <a name="to-send-notifications-using-a-c-console-app"></a>若要使用 C# 控制台应用程序发送通知
修改`SendTemplateNotificationAsync`替换为以下代码以前创建的控制台应用中的方法。 请注意如何在此情况下没有无需发送不同的区域设置和平台的多个通知。

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


请注意，此简单调用将提供到新闻的本地化的部分**所有**你的设备，不管平台如何，因为你的通知中心将生成并提供了正确的本机负载对所有设备已都订阅特定标记。

### <a name="sending-the-notification-with-mobile-services"></a>发送与移动服务通知
在您的移动服务计划，你可以使用以下脚本：

    var azure = require('azure');
    var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
    var notification = {
            "News_English": "World News in English!",
            "News_French": "World News in French!",
            "News_Mandarin", "World News in Mandarin!"
    }
    notificationHubService.send('World', notification, function(error) {
        if (!error) {
            console.warn("Notification successful");
        }
    });


