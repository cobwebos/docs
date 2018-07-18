添加条件后，可以使用触发器生成的数据来执行一些有趣的操作。 请遵循以下步骤添加“Salesforce - 获取对象”操作。 此操作在每次创建新的潜在顾客时获取数据。 稍后还要添加第二个操作，它使用来自“Salesforce - 获取对象”操作的数据，通过 Office 365 连接器发送电子邮件。  

若要配置此操作，需要提供以下信息。 会发现，可以方便地使用触发器生成的数据作为某些新文件属性的输入：

| 创建文件属性 | 说明 |
| --- | --- |
| 对象类型 |这是我们关注的 Salesforce 对象类型。 示例包括“潜在顾客”、“帐户”等。 |
| 对象 ID |表示对象的标识符。 |

1. 选择“添加操作”链接。 这会打开可搜索要采取的任何操作的搜索框。 本示例关注的操作是 Salesforce 操作。      
   ![Salesforce 操作图像 1](./media/connectors-create-api-salesforce/action-1.png)  
2. 输入 *salesforce* 搜索与 salesforce 相关的操作。
3. 选择“Salesforce - 获取对象”作为要执行的操作。   **注意：** 系统会提示授权逻辑应用访问 Salesforce 帐户（如果之前尚未这样做）。    
   ![Salesforce 操作图像 2](./media/connectors-create-api-salesforce/action-2.png)    
4. 此时会打开“获取对象”控件。  
5. 选择“潜在顾客”作为对象类型。
6. 选择“对象 ID”控件。
7. 选择“...”展开可用作操作输入的标记列表。       
   ![Salesforce 操作图像 3](./media/connectors-create-api-salesforce/action-3.png)    
8. 选择“潜在顾客 ID”控件。   
   ![Salesforce 操作图像 4](./media/connectors-create-api-salesforce/action-4.png)     
9. 请注意，“潜在顾客 ID”标记现在位于“对象ID”控件中，表示“获取对象”操作会搜索 ID 与触发此逻辑应用的潜在顾客的 ID 相同的潜在顾客。  
   ![Salesforce 操作图像 5](./media/connectors-create-api-salesforce/action-5.png)  
10. 保存工作。 大功告成，现已将“获取对象”操作添加到逻辑应用。 “获取对象”控件应如下所示：    
    ![Salesforce 操作图像 6](./media/connectors-create-api-salesforce/action-6.png)  

添加用于获取潜在顾客的操作后，可以针对新建的潜在顾客执行一些有趣的操作。 在企业中，可以发送电子邮件来通知通讯组已创建新的潜在顾客。 让我们使用 Office 365 连接器发送一封电子邮件，其中包含来自 Salesforce 中新潜在顾客对象的一些相关信息。  

1. 选择“添加操作”，并在搜索控件中输入“电子邮件”。 这会筛选出与发送和接收电子邮件相关的操作。  
2. 选择“Office 365 Outlook - 发送电子邮件”列表项。 如果尚未与 Office 365 帐户建立*连接*，系统会提示输入 Office 365 凭据来立即建立连接。 完成后，“发送电子邮件”控件会打开。        
   ![Salesforce 操作图像 7](./media/connectors-create-api-salesforce/action-7.png)  
3. 在“收件人”控件中，输入电子邮件要发送到的电子邮件地址。
4. 在“主题”控件中，输入“已创建新潜在顾客”，并选择“公司”标记。 随后会显示 Salesforce 中创建的新潜在顾客所在的“公司”字段。  
5. 在“正文”控件中，可以选择新潜在顾客对象中的任一标记，还可以输入想要显示在电子邮件正文中的任何文本。 下面是一个示例：  
   ![Salesforce 操作图像 8](./media/connectors-create-api-salesforce/action-8.png)   
6. 保存工作流。  

就这么简单。 逻辑应用现已完成。  

现在，可以测试逻辑应用：在 Salesforce 中，创建一个符合所创建条件的新潜在顾客。  如果完全按照本演练操作，只需创建一个电子邮件地址包含 *amazon.com* 的新潜在顾客即可。 几秒钟后，应会触发该逻辑应用，结果将如下所示：  
![Salesforce 操作图像 9](./media/connectors-create-api-salesforce/action-9.png)  

