### <a name="prerequisites"></a>先决条件
* 一个 [Yammer](https://www.yammer.com/) 帐户 

可以在逻辑应用中使用 Yammer 帐户之前，必须先授权该逻辑应用可以连接到你的 Yammer 帐户。 好在，可以从 Azure 门户中的逻辑应用内轻松地执行此操作。 

下面介绍授权逻辑应用连接到 Yammer 帐户的步骤：

1. 若要创建与 Yammer 的连接，请在逻辑应用设计器的下拉列表中选择“显示 Microsoft 托管的 API”，然后在搜索框中输入“Yammer”。 选择要使用的触发器或操作：  
   ![](./media/connectors-create-api-yammer/yammer-1.png)
2. 如果以前尚未创建任何与 Yammer 的连接，系统会提示你提供 Yammer 凭据。 这些凭据将用于授权逻辑应用连接到你的 Yammer 帐户，然后访问该帐户中的数据：  
   ![](./media/connectors-create-api-yammer/yammer-2.png)
3. 提供 Yammer 用户名和密码，对逻辑应用进行授权：  
   ![](./media/connectors-create-api-yammer/yammer-3.png)   
4. 请注意，连接已创建，现在可以随时在逻辑应用中继续执行其他步骤：  
   ![](./media/connectors-create-api-yammer/yammer-4.png)   

