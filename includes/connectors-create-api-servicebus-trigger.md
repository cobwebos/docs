下面介绍了如何使用**Service Bus 的队列中接收到消息**触发器启动逻辑应用工作流，当新项发送到服务总线队列。  

> [!NOTE]
> 将提示你进行登录你的服务总线连接字符串，如果尚未创建与服务总线的连接。  
> 
> 

1. 在逻辑应用设计器上的搜索框中，输入**服务总线**。 然后选择**Service Bus 的队列中接收到消息**触发器。  
   ![服务总线触发器图像 1](./media/connectors-create-api-servicebus/trigger-1.png)   
2. **队列中接收到消息时**对话框随即显示。  
   ![服务总线触发器图像 2](./media/connectors-create-api-servicebus/trigger-2.png)   
3. 输入你想要监视的触发器服务总线队列的名称。   
   ![服务总线触发器图像 3](./media/connectors-create-api-servicebus/trigger-3.png)   

此时，与某个触发器已配置你的逻辑应用。 你所选的队列中收到新项时，该触发器将开始运行的其他触发器和工作流中的操作。    

