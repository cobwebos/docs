

1. 登录到 [Firebase 控制台](https://firebase.google.com/console/)。 如果还没有 Firebase 项目，创建一个新项目。
2. 创建项目后，选择“向 Android 应用添加 Firebase”。 然后按提供的说明操作。

    ![将 Firebase 添加到 Android 应用](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. 在 Firebase 控制台中，选择与项目相对应的齿轮图标。 然后，选择“项目设置”。

    ![选择“项目设置”](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. 在项目设置中选择“常规”选项卡。 然后下载包含服务器 API 密钥和客户端 ID 的 **google-services.json** 文件。
5. 选择项目设置中的“Cloud Messaging”选项卡，然后复制“旧服务器密钥”的值。 请使用此值配置通知中心访问策略。
