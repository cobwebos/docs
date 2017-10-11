现在，你已添加触发器，其时间来完成更有趣的所生成的触发器的数据。 请按照这些步骤添加**SFTP-提取文件夹**操作。 如果满足定义的条件，此操作将提取文件的内容。 

若要配置此操作，你将需要提供以下信息。 你将注意到它是作为输入新的文件的属性的某些生成触发器的易于使用数据：

| SFTP-提取文件夹属性 | 描述 |
| --- | --- |
| 源存档文件路径 |这是在提取文件的路径。 你可以从较早的操作选择一个令牌，或浏览 SFTP 服务器，以找到的文件路径。 |
| 目标文件夹路径 |这是将放置所提取的文件的路径。 您可以选择其中一个令牌从早期的操作为目标路径或浏览 SFTP 服务器和选择的路径。 |
| 要覆盖吗？ |指示是否应覆盖现有文件，或不是否目标文件夹路径中找到与提取的文件同名的文件。 |

让我们开始吧添加操作以提取文件，如果前面定义的条件的计算结果为*True*。 

1. 选择**添加操作**。        
   ![SFTP 操作条件图像 6](./media/connectors-create-api-sftp/condition-6.png)   
2. 选择**SFTP-提取文件夹**操作      
   ![SFTP 操作条件图像 7](./media/connectors-create-api-sftp/condition-7.png)   
3. 选择**源存档文件路径**              
   ![SFTP 操作条件图像 9](./media/connectors-create-api-sftp/condition-9.png)   
4. 选择**文件路径**令牌。 这表示，你将使用该触发器找到的文件的文件路径，作为源存档文件路径。           
   ![SFTP 操作条件图像 10](./media/connectors-create-api-sftp/condition-10.png)   
5. 选择**目标文件夹路径**           
   ![SFTP 操作条件图像 11](./media/connectors-create-api-sftp/condition-11.png)   
6. 选择**文件路径**令牌。 这表示，你将用作触发器找到的文件的文件路径的目标路径的提取的文件。   
7. 输入*\ExtractedFile*中**目标文件夹路径**控件。 执行此操作之后目标文件夹路径控件中的文件路径令牌。         
   ![SFTP 操作条件图像 12](./media/connectors-create-api-sftp/condition-12.png)   
8. 输入*True*中 **覆盖？*控件以指明是否它们具有相同名称的提取的文件应覆盖现有文件。      
   ![SFTP 操作条件图像 13](./media/connectors-create-api-sftp/condition-13.png)   
9. 将所做的更改保存到你的工作流  

