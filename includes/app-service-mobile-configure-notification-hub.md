Azure App Service Mobile Apps 功能使用[Azure 通知中心]可以发送推送，因此你将为你的移动应用配置通知中心。

1. 在[Azure 门户]，请转到**应用程序服务**，然后单击您的应用后端。 下**设置**，单击**推送**。
2. 单击**连接**将通知中心资源添加到应用程序。 你可以创建一个中心，或连接到一个现有。

    ![](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

现在你已连接到你的移动应用后端项目的通知中心。 更高版本，你将配置此通知中心以连接到平台通知系统 (PNS) 将推送到设备。

[Azure 门户]: https://portal.azure.com/
[Azure 通知中心]: https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-push-notification-overview/
