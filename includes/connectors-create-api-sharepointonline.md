

要连接到 **SharePoint Online**，需要向 SharePoint Online 提供标识（用户名和密码、智能卡凭据等）。 经过身份验证后，可以继续在逻辑应用中使用 SharePoint Online 连接器。 

逻辑应用的设计器打开时，按照以下步骤操作登录到 SharePoint，创建要在逻辑应用中使用的**连接**：

1. 在搜索框中输入“SharePoint”，并等待该搜索返回与 SharePoint Online 有关的所有触发器和操作：   
   ![配置 SharePoint][1]  
2. 选择“SharePoint Online - 创建文件时”触发器  
3. 选择“登录到 SharePoint Online”：   
   ![配置 SharePoint][2]    
4. 提供 SharePoint 凭据进行登录，以使用 SharePoint 进行身份验证   
   ![配置 SharePoint][3]     
5. 身份验证完成后，会你将重定向到逻辑应用。 就这么简单，连接完成创建。 请注意底部的消息，指示现在已连接到 SharePoint。  
   ![配置 SharePoint][4]  
6. 然后可以添加完成逻辑应用所需的其他触发器和操作。   

[1]: ./media/connectors-create-api-sharepointonline/connectionconfig1.png
[2]: ./media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ./media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ./media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ./media/connectors-create-api-sharepointonline/connectionconfig5.png
