

1. 在 Mac 上启动“Keychain Access”。 在左侧导航栏中的“类别”下，打开“我的证书”。 找到已在前一部分下载的 SSL 证书，并公开其内容。 仅选择证书（不选择私钥）。 然后[将其导出](https://support.apple.com/kb/PH20122?locale=en_US)。
2. 在 [Azure 门户](https://portal.azure.com/)中，选择“浏览全部” > “应用程序服务”。 然后选择“移动应用”后端。 
3. 在“设置”下，选择“应用服务推送”。 然后选择通知中心名称。 
3. 转到 **Apple Push Notification 服务** > **上传证书**。 上传 .p12 文件，选择正确的**模式**（具体取决于此前的客户端 SSL 证书是生产证书还是沙盒证书）。 保存任何更改。

现在，服务已配置为在 iOS 上使用通知中心。

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
