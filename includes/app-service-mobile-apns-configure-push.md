

1. 在你 Mac 上启动**Keychain Access**。 在左侧的导航栏上，在**类别**，打开**我的证书**。 查找在上一节中下载 SSL 证书，并公开其内容。 选择的证书 （不选择私钥），和[将其导出](https://support.apple.com/kb/PH20122?locale=en_US)。
2. 在[Azure 门户](https://portal.azure.com/)，单击**浏览所有** > **应用程序服务**，然后单击你的移动应用后端。 下**设置**，单击**应用服务推送**，然后单击你的通知中心名称。 转到**Apple 推送通知服务** > **上载证书**。 上载.p12 文件，选择正确**模式**（具体取决于你的客户端 SSL 证书从前面的是否为生产或沙盒）。 保存任何更改。

你的服务现在已配置为使用在 iOS 上的推送通知。

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
