在此部分中，更新代码中你现有的移动应用后端项目以发送推送通知，每次添加新项。 这由提供支持[模板](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)Azure 通知中心，启用跨平台推送功能。 各种客户端注册推送通知使用模板，并且单个通用推送能否访问所有客户端平台。

选择你的后端项目类型相匹配的以下过程之一&mdash;任一[.NET 后端](#dotnet)或[Node.js 后端](#nodejs)。

### <a name="dotnet"></a>.NET 后端项目
1. 在 Visual Studio 中，右键单击服务器项目，然后单击**管理 NuGet 包**。 搜索`Microsoft.Azure.NotificationHubs`，然后单击**安装**。 这将安装为从后端发送通知的通知中心库。
2. 在服务器项目中，打开**控制器** > **TodoItemController.cs**，并添加以下 using 语句：

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;
3. 在**PostTodoItem**方法，将以下代码添加到在调用后**InsertAsync**:  

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Sending the message so that all template registrations that contain "messageParam"
        // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
        Dictionary<string,string> templateParams = new Dictionary<string,string>();
        templateParams["messageParam"] = item.Text + " was added to the list.";

        try
        {
            // Send the push notification and log the results.
            var result = await hub.SendTemplateNotificationAsync(templateParams);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    这将发送模板通知包含项。当插入的新项的文本。
4. 重新发布服务器项目。

### <a name="nodejs"></a>Node.js 后端项目
1. 如果尚未这样做[下载快速入门后端项目](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)，或其他使用[Azure 门户中的联机编辑器](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor)。
2. Todoitem.js 中的现有代码替换为以下：

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');

        var table = azureMobileApps.table();

        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK,
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');

        // Define the template payload.
        var payload = '{"messageParam": "' + context.item.text + '" }';  

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a template notification.
                    context.push.send(null, payload, function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.info('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
        });

        module.exports = table;  

    这将发送模板通知时插入的新项包含 item.text。
3. 在编辑本地计算机上的文件，重新发布服务器项目。
