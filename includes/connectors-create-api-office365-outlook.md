#### <a name="prerequisites"></a>先决条件
* Azure 帐户；可以创建[免费帐户](https://azure.microsoft.com/free)
* 一个 [Office 365](https://office365.com) 帐户  

在逻辑应用中使用 Office 365 帐户之前，请先授权该逻辑应用可以连接到 Office 365 帐户。 可以在 Azure 门户中的逻辑应用内轻松地执行此操作。  

按照以下步骤操作，即可授权逻辑应用连接到 Office 365 帐户：

1. 创建逻辑应用。 在逻辑应用设计器的下拉列表中选择“显示 Microsoft 托管的 API”，并在搜索框中输入“office 365”。 选择其中一个触发器或操作：  
    ![Office 365 连接创建步骤](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  
2. 如果以前尚未创建任何与 Office 365 的连接，系统会提示使用 Office 365 凭据进行登录：  
    ![Office 365 连接创建步骤](./media/connectors-create-api-office365-outlook/office365-signin.png)  
3. 选择“登录”，并输入用户名和密码。 选择“登录”：  
    ![Office 365 连接创建步骤](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)
   
    这些凭据用于授权逻辑应用连接到 Office 365 帐户，然后访问该帐户。 
4. 请注意，连接已创建。 现在，继续在逻辑应用中执行其他步骤：   
    ![Office 365 连接创建步骤](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  

