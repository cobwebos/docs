### <a name="prerequisites"></a>先决条件
* 一个 Twilio 帐户
* 已验证可以接收短信的 Twilio 手机号码
* 已验证可以发送短信的 Twilio 手机号码

> [!NOTE]
> 如果使用的是 Twilio 试用帐户，则只能发送短信到**已验证**的手机号码。  
> 
> 

可以在逻辑应用中使用 Twilio 帐户之前，必须先授权该逻辑应用可以连接到 Twilio 帐户。 好在，可以从 Azure 门户中的逻辑应用内轻松地执行此操作。 

下面介绍授权逻辑应用连接到 Twilio 帐户的步骤：

1. 要创建与 Twilio 的连接，请在逻辑应用设计器的下拉列表中选择“显示 Microsoft 托管的 API”，并在搜索框中输入“Twilio”。 选择要使用的触发器或操作：  
   ![](./media/connectors-create-api-twilio/twilio-0.png)
2. 如果以前尚未创建任何与 Twilio 的连接，系统会提示提供 Twilio 凭据。 这些凭据用于授权逻辑应用连接到 Twilio 帐户，然后访问该帐户中的数据：  
   ![](./media/connectors-create-api-twilio/twilio-1.png)  
3. 需要从 Twilio 的仪表板中获取 Twilio 帐户 ID 和 Twilio 访问令牌，因此现在登录到 Twilio 帐户即可获得这两条信息：  
   ![](./media/connectors-create-api-twilio/twilio-2.png)  
4. Twilio 和逻辑应用使用不同的名称来标识这两条信息。 下面介绍如何将它们映射到逻辑应用对话框：![](./media/connectors-create-api-twilio/twilio-3.png)  
5. 选择“创建连接”按钮：  
   ![](./media/connectors-create-api-twilio/twilio-4.png)
6. 请注意，连接已创建，现在可以随时在逻辑应用中继续执行其他步骤：  
   ![](./media/connectors-create-api-twilio/twilio-5.png)

