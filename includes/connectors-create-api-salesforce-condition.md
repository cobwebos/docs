此条件将计算每个新的 Salesforce 潜在客户的电子邮件地址字段。 如果电子邮件地址包含*amazon.com*，条件结果将是*True*。

1. 选择**+ 新步骤**。  
   ![Salesforce 条件图像 1](./media/connectors-create-api-salesforce/condition-1.png)   
2. 选择**添加条件**。    
   ![Salesforce 条件图像 2](./media/connectors-create-api-salesforce/condition-2.png)  
3. 选择**选择一个值**。    
   ![Salesforce 条件图像 3](./media/connectors-create-api-salesforce/condition-3.png)  
4. 选择*电子邮件*令牌从该触发器的潜在客户。    
   ![Salesforce 条件图像 4](./media/connectors-create-api-salesforce/condition-4.png)  
5. 选择*包含*。      
   ![Salesforce 条件图像 5](./media/connectors-create-api-salesforce/condition-5.png)  
6. 选择**选择一个值**底部的控件。     
   ![Salesforce 条件映像 6](./media/connectors-create-api-salesforce/condition-6.png)  
7. 输入*amazon.com*作为你想要评估新的电子邮件地址的值会导致的。 如果电子邮件地址包含*amazon.com*，条件的计算结果将为*True* ，应用程序逻辑中的其他步骤才能继续。    
   ![Salesforce 条件映像 7](./media/connectors-create-api-salesforce/condition-7.png)  
8. 保存你的 logic apps。  

