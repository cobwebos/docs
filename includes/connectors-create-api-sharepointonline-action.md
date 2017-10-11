现在，你已添加触发器，其时间来完成更有趣的所生成的触发器的数据。 请按照这些步骤添加**SharePoint Online 的创建文件**操作。 此操作将一个文件在 SharePoint Online 每的次创建新的项触发器将激发。 

若要配置此操作，你将需要提供以下信息。 提供此信息，你将注意到它是作为输入新的文件的属性的某些生成触发器的易于使用数据：

| 创建文件属性 | 描述 |
| --- | --- |
| 站点 URL |这是你想要创建新的文件的 SharePoint Online 站点的 URL。 从显示的列表中选择的站点。 |
| 文件夹路径 |这是 （在上一步中选择了站点 URL) 的文件夹将放置新的文件。 浏览和选择的文件夹。 |
| 文件名 |这是文件的正在创建的名称。 |
| 文件内容 |将写入到文件内容。 |

1. 选择**+ 新步骤**以添加操作。  
   ![SharePoint online 操作图像 1](./media/connectors-create-api-sharepointonline/action-1.png)  
2. 选择**添加操作**链接。 搜索框中，你可以搜索的任何操作你想要学习这将打开。 例如，SharePoint 操作感兴趣。    
   ![SharePoint online 操作图像 2](./media/connectors-create-api-sharepointonline/action-2.png)    
3. 输入*sharepoint*搜索与 SharePoint 相关的操作。
4. 选择**SharePoint Online 的创建文件**作为要执行的操作。   **请注意**： 系统将提示你授权逻辑应用访问 SharePoint 帐户，如果你以前未创建与 SharePoint Online 的连接。    
   ![SharePoint online 操作图像 3](./media/connectors-create-api-sharepointonline/action-3.png)    
5. **创建文件**控件打开。   
   ![SharePoint online 操作图像 4](./media/connectors-create-api-sharepointonline/action-4.png)     
6. 选择**站点 URL**然后浏览以查找你想要创建文件的站点。     
   ![SharePoint online 操作图像 5](./media/connectors-create-api-sharepointonline/action-5.png)  
7. 选择**文件夹路径**然后浏览以查找将放置新的文件的文件夹。  
   ![SharePoint online 操作图像 6](./media/connectors-create-api-sharepointonline/action-6.png)  
8. 选择**文件名**控制并输入你想要创建的文件的名称。 在这里，你可以直接输入文件名，或者可以使用任何从前面创建的触发器的属性。 执行此操作通过从列表中选择属性**从当创建新项目的输出**。 选择后，此列表并仅显示**文件名**控件。 我可以在本演练中，来选择所创建的文件的名称作为 ID (的新列表项 ID) **SharePoint Online 的创建文件**操作。    
   ![SharePoint online 操作图像 7](./media/connectors-create-api-sharepointonline/action-7.png)  
9. 选择**文件内容**控制并输入将写入到将创建的文件的内容。 文件内容，请注意，你可以使用任何从前面创建的触发器的属性。 只需从显示的列表中选择的属性。 或者，也可以输入**文件内容**直接在控件的文本。 在此示例中，我选择某些属性，并添加空格和连字符分隔每个属性。        
   ![SharePoint online 操作图像 8](./media/connectors-create-api-sharepointonline/action-8.png)  
10. 将所做的更改保存到你的工作流  
11. 祝贺你，你现在拥有一个新项添加到 SharePoint Online 列表时，会触发一个完全正常运行逻辑应用。 然后，该应用会创建一个文件，使用某些新列表项中的属性。  你现在可以测试它通过在 SharePoint 列表中创建新项。 

