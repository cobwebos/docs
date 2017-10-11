现在，你已添加一个条件，其时间来完成更有趣的所生成的触发器的数据。 请按照这些步骤添加**Salesforce-Get 对象**操作。 此操作将在每次创建一个新的主管获取数据。 你还将添加将使用来自 Salesforce-Get 对象操作发送电子邮件使用 Office 365 连接器的数据的第二个操作。  

若要配置此操作，你将需要提供以下信息。 你将注意到它是作为输入新的文件的属性的某些生成触发器的易于使用数据：

| 创建文件属性 | 描述 |
| --- | --- |
| 对象类型 |这是你感兴趣的 Salesforce 对象的类型。 示例包括潜在客户、 帐户等。 |
| 对象 ID |这表示对象的标识符。 |

1. 选择**添加操作**链接。 搜索框中，你可以搜索的任何操作你想要学习这将打开。 对于此示例，Salesforce 操作感兴趣。      
   ![Salesforce 操作图像 1](./media/connectors-create-api-salesforce/action-1.png)  
2. 输入*salesforce*搜索与 salesforce 相关的操作。
3. 选择**Salesforce-Get 对象**作为要执行的操作。   **请注意**： 系统将提示你授权你逻辑应用程序访问你的 Salesforce 帐户，如果你不这样做之前。    
   ![Salesforce 操作图像 2](./media/connectors-create-api-salesforce/action-2.png)    
4. **Get 对象**控件打开。  
5. 选择*导致*作为对象类型。
6. 选择**对象 ID**控件。
7. 选择**...**来展开可以用于操作作为输入的令牌的列表。       
   ![Salesforce 操作图像 3](./media/connectors-create-api-salesforce/action-3.png)    
8. 选择**导致 ID**控件打开。   
   ![Salesforce 操作图像 4](./media/connectors-create-api-salesforce/action-4.png)     
9. 请注意，会导致 ID 令牌是现在在对象 ID 控件中，表明 Get 对象操作将搜索为潜在客户 id，它等于触发此逻辑应用的潜在客户的潜在客户 ID。  
   ![Salesforce 操作图像 5](./media/connectors-create-api-salesforce/action-5.png)  
10. 保存你的工作。 就这么简单，你已将 Get 对象操作添加到逻辑应用。 你控制 Get 对象应如下所示：    
    ![Salesforce 操作图像 6](./media/connectors-create-api-salesforce/action-6.png)  

现在，你已添加一个操作以获取一个主管，你可能想要执行某些与新创建的潜在顾客有意义。 在企业中，你可能想要发送电子邮件通知的通讯组列表已创建一个新的主管。 让我们使用 Office 365 连接器从 Salesforce 中的新主管对象发送包含一些相关信息的电子邮件。  

1. 选择**添加操作**然后输入*电子邮件*搜索控件中。 这将筛选为那些与发送和接收电子邮件相关的操作。  
2. 选择**Office 365 Outlook 的电子邮件发送**列表项。 如果你尚未创建*连接*到你的 Office 365 帐户，你将会提示您输入您的 Office 365 凭据立即创建。 完成后，**发送一封电子邮件**控件打开。        
   ![Salesforce 操作图像 7](./media/connectors-create-api-salesforce/action-7.png)  
3. 输入你想要在发送电子邮件的电子邮件地址**到**控件。
4. 在**主题**控件上，输入*创建新导致*-然后选择*公司*令牌。 这将显示*公司*字段从新的潜在顾客在 Salesforce 中创建。  
5. 在**正文**控件，你可以从新的潜在客户对象中选择任何标记并且也可以输入你想要在电子邮件的正文中显示任何文本。 下面是一个示例：  
   ![Salesforce 操作图像 8](./media/connectors-create-api-salesforce/action-8.png)   
6. 保存工作流。  

就是这样。 逻辑应用现已完成。  

现在，你可以测试逻辑应用： 在 Salesforce，创建一个新的主管符合您创建的条件。  如果您完全遵循本演练，就可以创建一个主管包含的电子邮件地址*amazon.com*中它。 几秒钟后应触发逻辑应用和结果看起来可能类似于此：  
![Salesforce 操作图像 9](./media/connectors-create-api-salesforce/action-9.png)  

