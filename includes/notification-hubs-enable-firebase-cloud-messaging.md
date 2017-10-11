

1. 登录到[Firebase 控制台](https://firebase.google.com/console/)。 如果你尚未拥有，请创建一个新的 Firebase 项目。
2. 创建你的项目后，单击**向 Android 应用程序添加 Firebase**并按照提供的说明。

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. 在 Firebase 控制台中，单击你的项目的小齿轮，然后单击**项目设置**。

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. 单击**Cloud Messaging**选项卡中你的项目设置，并将复制的值**服务器密钥**和**发件人 ID**。 这些值将在稍后用于在应用程序中配置通知中心访问策略和你的通知处理程序。
