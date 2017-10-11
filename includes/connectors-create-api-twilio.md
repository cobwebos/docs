### <a name="prerequisites"></a>必备条件
* Twilio 帐户
* 已验证的 Twilio 电话号码可以接收 SMS
* 可以发送短信验证的 Twilio 电话号码

> [!NOTE]
> 如果你使用的 Twilio 试用帐户，则可以仅发送到 SMS**验证**电话号码。  
> 
> 

你可以在逻辑应用程序中使用 Twilio 帐户之前，你必须向授权逻辑应用，使连接到你的 Twilio 帐户。 幸运的是，你可以轻松地从在 Azure 门户上的应用程序逻辑。 

下面是授权逻辑应用程序，以连接到你的 Twilio 帐户的步骤：

1. 若要创建连接到 Twilio，逻辑应用程序设计器中，选择**显示 Microsoft 托管 Api**下拉列表然后输入*Twilio*的搜索框中。 选择触发器或你将希望使用的操作：  
   ![](./media/connectors-create-api-twilio/twilio-0.png)
2. 如果你尚未创建任何连接到 Twilio 之前，你将获得提示以提供您的 Twilio 凭据。 这些凭据将用于连接到你逻辑应用程序进行授权和访问你的 Twilio 帐户的数据：  
   ![](./media/connectors-create-api-twilio/twilio-1.png)  
3. 你将需要**Twilio 帐户 id**和**Twilio 访问令牌**中 Twilio 仪表板中，因此中登录到 Twilio 帐户现在获取这些两条信息：  
   ![](./media/connectors-create-api-twilio/twilio-2.png)  
4. Twilio 和逻辑应用使用不同的名称来标识这些两条信息。 下面是如何你必须将它们映射到逻辑应用对话框：![](./media/connectors-create-api-twilio/twilio-3.png)  
5. 选择**创建连接**按钮：  
   ![](./media/connectors-create-api-twilio/twilio-4.png)
6. 请注意已创建连接，并且你现在可以自由地继续其他应用程序逻辑中的步骤：  
   ![](./media/connectors-create-api-twilio/twilio-5.png)

