本演练介绍在 Salesforce 中创建新潜在顾客时，如何使用“Salesforce - 创建对象时”触发器启动逻辑应用工作流。

> [!NOTE]
> 如果尚未与 Salesforce 建立*连接*，系统会提示登录到 Salesforce 帐户。  
> 
> 

1. 在逻辑应用设计器的搜索框中输入 *salesforce*，并选择“Salesforce - 创建对象时”触发器。  
   ![Salesforce 触发器图像 1](./media/connectors-create-api-salesforce/trigger-1.png)   
2. 显示“创建对象时”控件。  
   ![Salesforce 触发器图像 2](./media/connectors-create-api-salesforce/trigger-2.png)   
3. 选择“对象类型”，并从对象列表中选择“潜在客户”。 在此步骤中，将指示要创建一个触发器，每当在 Salesforce 中新建潜在客户时，该触发器都将通知逻辑应用。   
   ![Salesforce 触发器图像 3](./media/connectors-create-api-salesforce/trigger-3.png)   
4. 就这么简单。 现已创建触发器。 但是，至少需要创建一个操作，才能使此逻辑应用生效。    
   ![Salesforce 触发器图像 4](./media/connectors-create-api-salesforce/trigger-4.png)   

现在，逻辑应用上已配置了一个触发器。在 Salesforce 中创建新项时，该触发器将开始运行工作流中的其他触发器和操作。  

