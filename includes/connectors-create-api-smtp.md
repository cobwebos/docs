### <a name="prerequisites"></a>先决条件
* 一个 [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) 帐户  

可以在逻辑应用中使用 SMTP 帐户之前，必须先授权该逻辑应用可以连接到 SMTP 帐户。好在，可以从 Azure 门户中的逻辑应用内轻松地执行此操作。  

下面介绍授权逻辑应用连接到 SMTP 帐户的步骤：  

1. 要创建与 SMTP 的连接，请在逻辑应用设计器的下拉列表中选择“显示 Microsoft 托管的 API”，并在搜索框中输入“SMTP”。 选择要使用的触发器或操作：  
   ![](./media/connectors-create-api-smtp/smtp-1.png)  
2. 如果以前尚未创建任何与 SMTP 的连接，系统会提示提供 SMTP 凭据。 这些凭据用于授权逻辑应用连接到 SMTP 帐户，然后访问该帐户中的数据：  
   ![](./media/connectors-create-api-smtp/smtp-2.png)  
3. 请注意，连接已创建，现在可以随时在逻辑应用中继续执行其他步骤：  
   ![](./media/connectors-create-api-smtp/smtp-3.png)  

