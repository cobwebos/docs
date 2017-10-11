### <a name="prerequisites"></a>必备条件
* A [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol)帐户  

你可以在逻辑应用程序中使用 SMTP 帐户之前，你必须向授权逻辑应用，使连接到 SMTP 帐户。幸运的是，你可以轻松地从在 Azure 门户上的应用程序逻辑。  

下面是授权逻辑应用程序，以连接到 SMTP 帐户的步骤：  

1. 若要创建连接到 SMTP，逻辑应用程序设计器中，选择**显示 Microsoft 托管 Api**下拉列表然后输入*SMTP*的搜索框中。 选择触发器或你将希望使用的操作：  
   ![](./media/connectors-create-api-smtp/smtp-1.png)  
2. 如果你尚未创建任何连接到 SMTP 之前，你将让系统提示你提供 SMTP 凭据。 这些凭据将用于连接到你逻辑应用程序进行授权和访问你的 SMTP 帐户的数据：  
   ![](./media/connectors-create-api-smtp/smtp-2.png)  
3. 请注意已创建连接，并且你现在可以自由地继续其他应用程序逻辑中的步骤：  
   ![](./media/connectors-create-api-smtp/smtp-3.png)  

