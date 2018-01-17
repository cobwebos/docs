

1. 登录到 [Firebase 控制台](https://firebase.google.com/console/)。 如果还没有 Firebase 项目，创建一个新项目。
2. 项目创建后，单击“将 Firebase 添加到 Android 应用”，并按照提供的说明操作。

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. 在 Firebase 控制台中，单击项目的齿轮图标，并单击“项目设置”。

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. 单击项目设置中的“常规”选项卡，然后下载包含服务器 API 密钥和客户端 ID 的 **google-services.json** 文件。
5. 单击项目设置中的“Cloud Messaging”选项卡，并复制“旧服务器密钥”的值。 此值将用于配置通知中心访问策略。
