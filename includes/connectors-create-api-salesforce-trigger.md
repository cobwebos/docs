在本演练中，您将学习如何使用**Salesforce-创建对象时**触发器，以在您的 Salesforce 中创建一个新的主管时启动逻辑应用工作流。

> [!NOTE]
> 你将让系统提示你登录到你的 Salesforce 帐户，如果尚未创建*连接*到 Salesforce。  
> 
> 

1. 输入*salesforce*逻辑应用设计器上的搜索框中然后选择**Salesforce-创建对象时**触发器。  
   ![Salesforce 触发器图像 1](./media/connectors-create-api-salesforce/trigger-1.png)   
2. **创建对象时**显示控件。  
   ![Salesforce 触发器图像 2](./media/connectors-create-api-salesforce/trigger-2.png)   
3. 选择**对象类型**然后选择*导致*从对象列表。 在此步骤中则表示您要创建一个触发器，它将通知你逻辑的应用程序，每当在 Salesforce 中创建一个新的主管。   
   ![Salesforce 触发器图像 3](./media/connectors-create-api-salesforce/trigger-3.png)   
4. 就是这样。 你已创建触发器。 但是，你需要创建至少一个操作，以使这有效逻辑应用程序。    
   ![Salesforce 触发器图像 4](./media/connectors-create-api-salesforce/trigger-4.png)   

此时，在您的 Salesforce 中创建新项时，将开始运行的其他触发器和工作流中的操作的触发器与已配置你逻辑应用。  

