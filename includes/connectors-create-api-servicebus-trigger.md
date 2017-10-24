下面介绍如何使用“服务总线 - 在队列中收到消息时”触发器，在将新项发送到服务总线队列后启动逻辑应用工作流。  

> [!NOTE]
> 如果尚未与服务总线建立连接，系统会提示使用服务总线连接字符串登录。  
> 
> 

1. 在逻辑应用设计器的搜索框中输入“服务总线”。 然后选择“服务总线 - 当队列中收到消息”触发器。  
   ![服务总线触发器图像 1](./media/connectors-create-api-servicebus/trigger-1.png)   
2. 此时会显示“在队列中收到消息时”对话框。  
   ![服务总线触发器图像 2](./media/connectors-create-api-servicebus/trigger-2.png)   
3. 输入触发器要监视的服务总线队列名称。   
   ![服务总线触发器图像 3](./media/connectors-create-api-servicebus/trigger-3.png)   

此时，逻辑应用中已配置一个触发器。 当选定的队列中收到新项时，该触发器将开始运行工作流中的其他触发器和操作。    

