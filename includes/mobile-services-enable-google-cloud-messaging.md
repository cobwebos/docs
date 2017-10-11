
1. 导航到[Google Cloud Console](https://console.developers.google.com/project)，使用你的 Google 帐户凭据登录。 
2. 单击**创建项目**，键入项目名称，然后单击**创建**。 如果请求，执行 SMS 验证，然后单击**创建**试。
   
    ![新建项目](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
   
     在您的新类型**项目名称**单击**创建项目**。
3. 单击**实用程序以及更多**按钮，然后单击**项目信息**。 记下**项目编号**。 你将需要设置此值为`SenderId`变量中客户端应用程序。
   
    ![应用工具和的详细信息](./media/mobile-services-enable-google-cloud-messaging/notification-hubs-utilities-and-more.png)
4. 在项目面板中，在**移动 Api**，单击**Google Cloud Messaging**，然后在下一页上单击**启用 API**并接受服务条款。 
   
    ![启用 GCM](./media/mobile-services-enable-google-cloud-messaging/enable-GCM.png)
   
    ![启用 GCM](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-2.png) 
5. 在项目面板中，单击**凭据** > **Create Credential** > **API 密钥**。 
   
    ![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)
6. 在**创建新密钥**，单击**服务器密钥**，键入你的密钥的名称，然后单击**创建**。
7. 记下**API 密钥**值。
   
    此 API 密钥值将用于启用 Azure 以使用 GCM 进行身份验证并代表你的应用程序发送推送通知。

