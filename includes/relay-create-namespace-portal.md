1. 登录到 [Azure 门户][Azure portal]。
2. 在左侧菜单中，选择“+ 创建资源”。 然后，选择“企业集成” > “中继”。
3. 在“创建命名空间”下输入命名空间名称。 系统会立即检查该名称是否可用。
4. 在“订阅”框中，选择要在其中创建命名空间的 Azure 订阅。
5. 在[“资源组”](../articles/azure-resource-manager/resource-group-portal.md)框中，选择要在其中放置命名空间的现有资源组，或者创建新资源组。  
6. 在“位置”中，选择应托管命名空间的国家或地区。
   
    ![创建命名空间][create-namespace]
7. 选择“创建”。 系统已创建命名空间并已将其启用。 几分钟后，系统将为用户的帐户预配资源。

### <a name="get-management-credentials"></a>获取管理凭据

1. 选择“所有资源”，然后选择新创建的命名空间名称。
2. 在“中继命名空间”下，选择“共享访问策略”。  
3. 在“共享访问策略”下，选择“RootManageSharedAccessKey”。
   
    ![connection-info][connection-info]
4. 在“策略: RootManageSharedAccessKey”下选择“连接字符串 - 主密钥”旁边的“复制”按钮。 这样会将连接字符串复制到剪贴板，供以后使用。 将此值粘贴到记事本或其他某个临时位置。
   
    ![connection-string][connection-string]

5. 重复上述步骤，将**主密钥**的值复制和粘贴到临时位置，供以后使用。  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
