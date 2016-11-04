
### 使用 API 密钥创建 Google Cloud Messaging 项目
> [!NOTE]
> 若要完成此过程，你必须拥有一个包含已验证电子邮件地址的 Google 帐户。若要新建一个 Google 帐户，请转到 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>。
> 
> 

1. 导航到 [Google Cloud Console](https://console.developers.google.com/project)，然后使用 Google 帐户凭据登录。
2. 转到“所有项目”，然后单击“创建项目”。
3. 输入**项目名称**，然后单击“创建”
4. 创建项目后，请确保记下由一长串数值组成的**项目编号**。可以在项目的“设置”中的“IAM 和管理员部分”下找到此编号，稍后会用到。
   
    ![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)
5. 现在，我们将创建一个适用于 Google Cloud Messaging 平台的密钥，我们的平台将使用该密钥向 Android 设备发送通知。转到“API 管理器”部分，然后单击“移动 API”下面的“Google Cloud Messaging”。
   
    ![](./media/mobile-engagement-enable-google-cloud-messaging/gcm.png)
6. 在下一页上，单击“启用”按钮。此时仪表板将提示你创建凭据。因此请单击“转到凭据”按钮。
   
    ![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)
7. 从第一个下拉列表框中选择“Google Cloud Messaging”，从第二个下拉列表框中选择“Web 服务器”，然后单击“我需要哪些凭据？”
   
       ![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)
8. 在“将凭据添加到项目”页面上，单击“创建 API 密钥”。
   
       ![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)
9. 记下“API 密钥”值。稍后将使用此 API 密钥值在“原生推送”部分中进行配置。现在单击“完成”。

<!---HONumber=AcomDC_0921_2016-->