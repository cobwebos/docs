1. 登录到 [Azure 门户][Azure portal]。
2. 在门户的左侧导航窗格中，依次单击“+ 创建资源”、“企业集成”和“中继”。
3. 在“创建命名空间”  对话框中，输入命名空间名称。 系统会立即检查该名称是否可用。
4. 在“订阅”  字段中，选择要创建命名空间的 Azure 订阅。
5. 在“[资源组](../articles/azure-resource-manager/resource-group-portal.md)”字段中，选择将保留命名空间的现有资源组，或者创建一个新资源组。      
6. 在“位置” 中，选择应托管命名空间的国家或地区。
   
    ![创建命名空间][create-namespace]
7. 单击“创建” 。 系统现已创建命名空间并已将其启用。 几分钟后，系统将为用户的帐户预配资源。

### <a name="obtain-the-management-credentials"></a>获取管理凭据

1. 单击“所有资源”，然后单击新创建的命名空间名称。
2. 在“中继命名空间”窗口中，单击“共享访问策略”。
3. 在“共享访问策略”窗口中，单击“RootManageSharedAccessKey”。
   
    ![connection-info][connection-info]
4. 在“策略: RootManageSharedAccessKey”屏幕中，单击“连接字符串 - 主键”旁边的“复制”按钮，将连接字符串复制到剪贴板供以后使用。 将此值粘贴到记事本或其他某个临时位置。
   
    ![connection-string][connection-string]

5. 重复上述步骤，将**主键**的值复制和粘贴到临时位置，以供将来使用。  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
