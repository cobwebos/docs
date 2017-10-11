
1. 请访问[Azure 门户]。 单击**浏览所有** > **Mobile Apps** > 刚创建的后端。 在移动应用程序设置中，单击**快速入门** > **Android)**。 下**配置客户端应用程序**，单击**下载**。 这会将下载应用程序预配置为连接到后端完成 Android 项目。 
2. 打开项目使用**Android Studio**，使用**导入项目 （Eclipse ADT、 Gradle 等）**. 请确保你进行此导入所选内容以避免任何 JDK 错误。
3. 按**运行 app**按钮以生成项目并在 Android 模拟器中启动应用程序。
4. 在应用中，键入有意义的文本，例如*完成本教程*，然后单击添加按钮。 这将 POST 请求发送到 Azure 后端前面部署。 来自请求的后端插入数据到 TodoItem SQL 表中，是，并将返回到移动应用程序的新存储的项的信息。 移动应用程序列表中显示此数据。 
   
    ![](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)

[Azure 门户]: https://portal.azure.com/
