### <a name="prerequisites"></a>先决条件
必须具有一个[服务总线](https://azure.microsoft.com/services/service-bus/)帐户。  

可以在逻辑应用中使用 Azure 服务总线帐户之前，必须先授权该逻辑应用可以连接到你的服务总线帐户。 好在，可以从 Azure 门户中的逻辑应用内轻松地执行此操作。  

下面介绍授权逻辑应用连接到服务总线帐户的步骤：  

1. 若要创建与服务总线的连接，请在逻辑应用设计器的下拉列表中选择“显示 Microsoft 托管的 API”。 然后在搜索框中输入“服务总线”。 选择要使用的触发器或操作。  
    ![服务总线连接图像 1](./media/connectors-create-api-servicebus/servicebus-1.png)  
2. 如果以前尚未创建任何与服务总线的连接，系统会提示你提供服务总线凭据。 这些凭据将用于授权逻辑应用连接到你的服务总线帐户，然后访问该帐户中的数据。 服务总线连接器需要适用于服务总线命名空间的连接字符串。 还需要**管理**权限。 知道你的连接字符串是适用于该命名空间还是适用于特定实体的好方法是：该连接字符串是否包含 `EntityPath` 参数。 如果包含，则该连接字符串不适用于逻辑应用。  
    ![服务总线连接字符串](./media/connectors-create-api-servicebus/connectionstring.png)
3. 收到适用于命名空间的连接字符串后，可以将它用于逻辑应用中的 API 连接。  
    ![服务总线连接图像 2](./media/connectors-create-api-servicebus/servicebus-2.png)  
4. 请注意，连接已创建，现在可以随时在逻辑应用中继续执行其他步骤。  
    ![服务总线连接图像 3](./media/connectors-create-api-servicebus/servicebus-3.png)   



<!--HONumber=Nov16_HO3-->


