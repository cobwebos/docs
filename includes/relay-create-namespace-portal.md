1. 登录到[Azure 门户][Azure portal]。
2. 在门户的左侧的导航窗格中，单击**新建**，然后单击**企业集成**，然后单击**中继**。
3. 在**创建命名空间**对话框中，输入命名空间名称。 系统会立即检查以查看名称是否可用。
4. 在**订阅**字段中，选择要在其中创建命名空间的 Azure 订阅。
5. 在**[资源组](../articles/azure-resource-manager/resource-group-portal.md)**字段中，选择现有资源组的命名空间将实时，或者创建一个新。      
6. 在**位置**，选择的国家或地区应在其中托管你的命名空间。
   
    ![创建命名空间][create-namespace]
7. 单击“创建”。 系统现在创建你的命名空间，并启用它。 几分钟后，系统将设置为你的帐户的资源。

### <a name="obtain-the-management-credentials"></a>获取管理凭据
1. 在命名空间列表中，单击新创建的命名空间名称。
2. 在命名空间边栏选项卡，单击**共享访问策略**。
3. 在**共享访问策略**边栏选项卡，单击**RootManageSharedAccessKey**。
   
    ![连接信息][connection-info]
4. 在**策略： RootManageSharedAccessKey**边栏选项卡，单击复制按钮旁边**连接字符串 – 主键**，以将连接字符串复制到剪贴板以供将来使用。 将此值粘贴到记事本或其他临时位置。
   
    ![连接字符串][connection-string]

5. 重复上述步骤，复制和粘贴的值**主键**到临时位置，以供将来使用。  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
