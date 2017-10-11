## <a name="connect-to-outlookcom"></a>连接到 Outlook.com
### <a name="prerequisites"></a>先决条件
* 一个 Outlook.com 帐户

可以在逻辑应用中使用 Outlook.com 帐户之前，必须先授权该逻辑应用可以连接到 Outlook.com 帐户。 好在，可以从 Azure 门户中的逻辑应用内轻松地执行此操作。 

下面介绍授权逻辑应用连接到 Outlook.com 帐户的步骤：

1. 所有逻辑应用都需要由触发器启动，因此在完成创建逻辑应用后，设计器会打开并显示可用于启动逻辑应用的触发器列表：
   
   ![](./media/connectors-create-api-outlook/office365-outlook-0.png)
2. 在搜索框中输入“outlook”。 请注意对列表进行筛选以列出名称中使用"Outlook"的所有触发器：![](./media/connectors-create-api-outlook/office365-outlook-0-5.png)
3. 选择“Office 365 Outlook - 新电子邮件到达时”。   
   如果以前尚未创建任何与 Outlook 的连接，系统会提示提供 Outlook.com 凭据。 这些凭据用于授权逻辑应用连接到 Outlook.com 帐户，然后访问该帐户中的数据：![](./media/connectors-create-api-outlook/office365-outlook-1.png)
4. 提供 Outlook 的凭据并登录：![](./media/connectors-create-api-outlook/office365-outlook-2.png)  
   就这么简单。 现在已完成创建与 Outlook 的连接。 可在创建的任何其他逻辑应用中使用此连接。

