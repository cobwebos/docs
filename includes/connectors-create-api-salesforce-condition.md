此条件评估每个新 Salesforce 潜在顾客的电子邮件地址字段。 如果电子邮件地址包含 *amazon.com*，则条件的结果为 *True*。

1. 选择“+新建步骤”。  
   ![Salesforce 条件图像 1](./media/connectors-create-api-salesforce/condition-1.png)   
2. 选择“添加条件”。    
   ![Salesforce 条件图像 2](./media/connectors-create-api-salesforce/condition-2.png)  
3. 选择“选择值”。    
   ![Salesforce 条件图像 3](./media/connectors-create-api-salesforce/condition-3.png)  
4. 从触发器的潜在顾客中选择“电子邮件”标记。    
   ![Salesforce 条件图像 4](./media/connectors-create-api-salesforce/condition-4.png)  
5. 选择“包含”。      
   ![Salesforce 条件图像 5](./media/connectors-create-api-salesforce/condition-5.png)  
6. 在控件底部选择“选择值”。     
   ![Salesforce 条件图像 6](./media/connectors-create-api-salesforce/condition-6.png)  
7. 输入 *amazon.com* 作为要针对新潜在顾客的电子邮件地址评估的值。 如果电子邮件地址包含 *amazon.com*，则条件评估为 *True*，逻辑应用中的其他步骤可继续执行。    
   ![Salesforce 条件图像 7](./media/connectors-create-api-salesforce/condition-7.png)  
8. 保存逻辑应用。  

