添加触发器后，可以使用触发器生成的数据来执行一些有趣的操作。 遵循以下步骤添加“SharePoint Online - 创建文件”操作。 每次激发“新建项”触发器时，此操作将在 SharePoint Online 中创建一个文件。 

若要配置此操作，需要提供以下信息。 你会发现，可以方便地使用触发器生成的数据作为某些新文件属性的输入：

| 创建文件属性 | 说明 |
| --- | --- |
| 站点 URL |这是要在其中创建新文件的 SharePoint Online 站点的 URL。 请从显示的列表中选择站点。 |
| 文件夹路径 |这是要在其中放置新文件的文件夹（位于“站点 URL”中）。 请浏览并选择文件夹。 |
| 文件名 |这是要创建的文件的名称。 |
| 文件内容 |将写入文件的内容。 |

1. 选择“+ 新建步骤”来添加操作。  
   ![](./media/connectors-create-api-sharepointonline/action-1.png)  
2. 选择“添加操作”链接。 这将打开可搜索要采取的任何操作的搜索框。 本示例关注的操作是 SharePoint 操作。    
   ![](./media/connectors-create-api-sharepointonline/action-2.png)    
3. 输入 *sharepoint* 搜索与 SharePoint 相关的操作。
4. 选择“SharePoint Online - 创建文件”作为要执行的操作。   **注意：**系统将提示授权逻辑应用访问 SharePoint 帐户（如果之前尚未这样做）。    
   ![](./media/connectors-create-api-sharepointonline/action-3.png)    
5. 此时将打开“创建文件”控件。   
   ![](./media/connectors-create-api-sharepointonline/action-4.png)     
6. 选择“站点 URL”，然后通过浏览找到想要在其中创建文件的站点。     
   ![](./media/connectors-create-api-sharepointonline/action-5.png)  
7. 选择“文件夹路径”，然后通过浏览找到要在其中放置新文件的文件夹。  
   ![](./media/connectors-create-api-sharepointonline/action-6.png)  
8. 选择“文件名”控件，然后输入要创建的文件的名称。 请注意，对于文件名，可以使用以前创建的触发器中的任何属性，从显示的列表中选择即可。     
   ![](./media/connectors-create-api-sharepointonline/action-7.png)  
9. 选择“文件内容”控件，然后输入将要写入到所创建文件的内容。 对于文件内容，请注意，可以使用以前创建的触发器中的任何属性。 只需从显示的列表中选择属性即可。 或者，也可以在控件中直接输入“文件内容”文本。 本示例选择了一些属性，并在每个属性之间添加了空格和连字符。        
   ![](./media/connectors-create-api-sharepointonline/action-8.png)  
10. 保存对工作流所做的更改  



<!--HONumber=Jan17_HO3-->


