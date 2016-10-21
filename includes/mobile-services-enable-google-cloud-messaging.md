
1. 导航到“Google Cloud Console”，然后使用你的 Google 帐户凭据登录。[](https://console.developers.google.com/project)
 
2. 单击“创建项目”，键入项目名称，然后单击“创建”。如果系统请求，请执行 SMS 验证，然后再次单击“创建”。

   	![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)

	 键入新的**项目名称**，然后单击“创建项目”。

3. 单击“实用工具及其他”按钮，然后单击“项目信息”。记下“项目编号”。需要将此值设置为客户端应用中的 `SenderId` 变量。

   	![](./media/mobile-services-enable-google-cloud-messaging/notification-hubs-utilities-and-more.png)


4. 在项目仪表板的“移动 API”下，单击“Google Cloud Messaging”，然后在下一页上，单击“启用 API”并接受服务条款。

	![启用 GCM](./media/mobile-services-enable-google-cloud-messaging/enable-GCM.png)

	![启用 GCM](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-2.png)

5. 在项目仪表板中，依次单击“凭据”>“创建凭据”>“API 密钥”。

   	![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)

6. 在“创建新密钥”中，单击“服务器密钥”、键入密钥的名称，然后单击“创建”。

7. 记下“API 密钥”值。

	接下来，你将使用此 API 密钥值，让 Azure 对 GCM 进行身份验证并代表你的应用程序发送推送通知。

<!---HONumber=AcomDC_0921_2016-->