

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 选择“新建” > “Web + 移动” > “通知中心”。
   
      ![Azure 门户 - 创建通知中心](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
      
3. 在“通知中心”框中，键入一个唯一名称。 选择“区域”、“订阅”和“资源组”（如果已经有一个）。 
   
    如果已有要在其中创建中心的服务总线命名空间，请执行以下操作：

    a. 在“命名空间”区域中，选择“选择现有”链接。 
   
    b. 选择“创建” 。

    如果尚无服务总线命名空间，可以使用基于中心名称创建的默认名称（如果该命名空间名称可用）。
   
      ![Azure 门户 - 设置通知中心属性](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

    创建命名空间和通知中心后，将打开 Azure 门户。 
   
      ![Azure 门户 - 通知中心门户页](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-page.png)

4. 选择“设置” > “访问策略”。 记下向你提供的两个连接字符串。 稍后在处理推送通知时将需要它们。
   
      ![Azure 门户 - 通知中心连接字符串](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)

