### <a name="prerequisites"></a>必备条件
你必须具有[Service Bus](https://azure.microsoft.com/services/service-bus/)帐户。  

你可以在逻辑应用程序中使用 Azure Service Bus 帐户之前，你必须向授权逻辑应用，使连接到服务总线帐户。 幸运的是，你可以轻松地从 Azure 门户上的应用程序逻辑中。  

下面是授权逻辑应用程序，以连接到服务总线帐户的步骤：  

1. 若要创建与服务总线的连接逻辑应用程序设计器中，选择**显示 Microsoft 托管 Api**下拉列表中。 然后输入**服务总线**的搜索框中。 选择你想要使用的操作的触发器。  
    ![Service Bus 连接图像 1](./media/connectors-create-api-servicebus/servicebus-1.png)  
2. 如果你尚未创建任何连接到服务总线之前，系统将提示提供你的服务总线凭据。 这些凭据用于授权你逻辑应用程序连接到和访问服务总线帐户的数据。 Service Bus 连接器需要的服务总线命名空间的连接字符串。 它还要求**管理**权限。 知道你的连接字符串是否为命名空间，或特定实体是如果它包含一种好方法`EntityPath`参数。 如果是这样，它不逻辑应用正确的连接字符串。  
    ![服务总线连接字符串](./media/connectors-create-api-servicebus/connectionstring.png)
3. 你收到了命名空间的连接字符串后，你可以使用它 Logic Apps 中的 API 连接。  
    ![Service Bus 连接图像 2](./media/connectors-create-api-servicebus/servicebus-2.png)  
4. 请注意已创建的连接，并且你现在可以自由地继续执行应用程序逻辑中的其他步骤。  
    ![Service Bus 连接图像 3](./media/connectors-create-api-servicebus/servicebus-3.png)   

