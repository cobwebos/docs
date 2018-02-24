添加触发器后，可以使用触发器生成的数据来执行一些有趣的操作。 请遵循以下步骤添加“SFTP - 提取文件夹”操作。 符合定义的条件时，此操作将提取文件的内容。 

若要配置此操作，需要提供以下信息。 会发现，可以方便地使用触发器生成的数据作为某些新文件属性的输入：

| SFTP - 提取文件夹属性 | 说明 |
| --- | --- |
| 源存档文件路径 |这是要提取的文件的路径。 可以从前一个操作选择某个标记，或者在 SFTP 服务器中通过浏览找到文件路径。 |
| 目标文件夹路径 |这是提取的文件的放置路径。 可以从前一个操作选择某个标记作为目标路径，或者在 SFTP 服务器中浏览并选择一个路径。 |
| 覆盖？ |指示在目标文件夹路径中找到与所提取文件同名的文件时是否覆盖现有文件。 |

让我们开始添加要在前面定义的条件求值为 *True* 时提取文件的操作。 

1. 选择“添加操作”。        
   ![SFTP 操作条件图像 6](./media/connectors-create-api-sftp/condition-6.png)   
2. 选择“SFTP - 提取文件夹”操作      
   ![SFTP 操作条件图像 7](./media/connectors-create-api-sftp/condition-7.png)   
3. 选择“源存档文件路径”              
   ![SFTP 操作条件图像 8](./media/connectors-create-api-sftp/condition-9.png)   
4. 选择“文件路径”标记。 这表示要使用触发器找到的文件的文件路径作为源存档文件路径。           
   ![SFTP 操作条件图像 10](./media/connectors-create-api-sftp/condition-10.png)   
5. 选择“目标文件夹路径”           
   ![SFTP 操作条件图像 11](./media/connectors-create-api-sftp/condition-11.png)   
6. 选择“文件路径”标记。 这表示要使用触发器找到文件的文件路径作为所提取文件的目标路径。   
7. 在“目标文件夹路径”控件中输入 *\ExtractedFile*。 请在“目标文件夹路径”控件中紧接在“文件路径”标记后面输入此值。         
   ![SFTP 操作条件图像 12](./media/connectors-create-api-sftp/condition-12.png)   
8. 在“覆盖? \*控件中输入 *True*，指明当现有文件与所提取文件同名时应覆盖现有文件。      
   ![SFTP 操作条件图像 13](./media/connectors-create-api-sftp/condition-13.png)   
9. 保存对工作流所做的更改  

