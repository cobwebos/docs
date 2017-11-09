本示例介绍在 SharePoint Online 列表中创建新项时，如何使用“SharePoint Online - 创建新项时”触发器启动逻辑应用工作流。

> [!NOTE]
> 如果尚未与 SharePoint Online 建立*连接*，系统会提示登录到 SharePoint 帐户。  
> 
> 

1. 在逻辑应用设计器的搜索框中输入 *dropbox*，并选择“Dropbox - 创建新项时”触发器。  
   ![SharePoint Online 触发器图像](./media/connectors-create-api-sharepointonline/trigger-1.png)  
2. 此时会显示“创建新项时”控件。  
   ![SharePoint Online 触发器图像 2](./media/connectors-create-api-sharepointonline/trigger-2.png)   
3. 选择一个**站点 URL**。 这是要监视的、可以触发工作流的新项所在的 SharePoint Online 站点。  
   ![SharePoint Online 触发器图像 3](./media/connectors-create-api-sharepointonline/trigger-3.png)   
4. 选择一个**列表名称**。 这是要监视的、可以触发工作流的新项所在的 SharePoint Online 站点上的列表。  
   ![SharePoint Online 触发器图像 4](./media/connectors-create-api-sharepointonline/trigger-4.png)   

此时，已使用将开始运行工作流中的其他触发器和操作的触发器配置了逻辑应用。 每当在选定的 SharePoint Online 列表中创建新项时，就会发生此情况。  

