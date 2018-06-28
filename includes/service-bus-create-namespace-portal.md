若要开始在 Azure 中使用服务总线消息实体，必须先使用在 Azure 中唯一的名称创建一个命名空间。 命名空间提供了用于对应用程序中的 Service Bus 资源进行寻址的范围容器。

创建命名空间：

1. 登录到 [Azure 门户][Azure portal]。
2. 在门户的左侧导航窗格中，依次单击“+ 创建资源”、“企业集成”和“服务总线”。
3. 在“创建命名空间”  对话框中，输入命名空间名称。 系统会立即检查该名称是否可用。
4. 在确保命名空间名称可用后，选择定价层（基础版、标准版或高级版）。
5. 在“订阅”  字段中，选择要创建命名空间的 Azure 订阅。
6. 在“资源组”字段中，选择将保留命名空间的现有资源组，或者创建一个新资源组。      
7. 在“位置” 中，选择应托管命名空间的国家或地区。
   
    ![创建命名空间][create-namespace]
8. 单击“创建”。 系统现已创建命名空间并已将其启用。 可能需要等待几分钟，因为系统将为帐户配置资源。

### <a name="obtain-the-management-credentials"></a>获取管理凭据
创建新的命名空间时，会自动生成一项初始的共享访问签名 (SAS) 规则，将一对主密钥和辅助密钥关联到一起，向每个密钥授予对命名空间的所有资产的完全控制权限。 请参阅[服务总线身份验证和授权](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md)，了解如何创建更多的规则，对常规的发送者和接收者的权限进行更多限制。 若要复制初始规则，请执行以下步骤： 

1.  单击“所有资源”，然后单击新创建的命名空间名称。
2. 在命名空间窗口中，单击“共享访问策略”。
3. 在“共享访问策略”屏幕中，单击“RootManageSharedAccessKey”。
   
    ![connection-info][connection-info]
4. 在“策略: RootManageSharedAccessKey”窗口中，单击“连接字符串 - 主键”旁边的复制按钮，将连接字符串复制到剪贴板供以后使用。 将此值粘贴到记事本或其他某个临时位置。
   
    ![connection-string][connection-string]

5. 重复上述步骤，将**主键**的值复制和粘贴到临时位置，以供将来使用。

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
