
1. 在你的 Mac，请访问[Azure 门户]。 单击**浏览所有** > **Mobile Apps** > 刚创建的后端。 在移动应用程序设置中，单击**快速入门** > **iOS (OBJECTIVE-C)**。 如果您愿意 Swift，单击**快速入门** > **iOS (Swift)**相反。 下**下载并运行您的 iOS 项目**，单击**下载**。 这会将下载应用程序预配置为连接到后端完成 Xcode 项目。 打开使用 Xcode 的项目。
2. 按**运行**按钮以生成项目并在 iOS 模拟器中启动应用程序。
3. 在应用中，键入有意义的文本，例如*完成本教程*，然后单击加号 (**+**) 图标。 这将 POST 请求发送到 Azure 后端前面部署。 来自请求的后端插入数据到 TodoItem SQL 表中，是，并将返回到移动应用程序的新存储的项的信息。 移动应用程序列表中显示此数据。 

   ![在 iOS 上运行的快速入门应用程序](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure 门户]: https://portal.azure.com/
