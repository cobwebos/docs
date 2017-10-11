## <a name="what-are-service-bus-topics-and-subscriptions"></a>什么是 Service Bus 主题和订阅？
服务总线主题和订阅支持*发布/订阅*消息通信模型。 使用主题和订阅时，分布式应用程序组件不能直接与每个其他;而是通过充当中介的主题的消息交换它们。

![TopicConcepts](./media/howto-service-bus-topics/sb-topics-01.png)

与服务总线队列，每条消息由单个使用方，相比主题和订阅提供是"一个对多"形式的通信，使用发布/订阅模式。 就可以注册到主题的多个订阅。 当消息发送到主题时，它然后进行，每个订阅以处理独立。

将主题订阅类似于接收发送至主题的消息副本的虚拟队列。 你可以选择注册每个订阅的方式，可用于筛选或限制哪些主题订阅接收发送到主题的哪些消息的主题的筛选器规则。

服务总线主题和订阅使你能够轻松扩展和跨多个用户和应用程序处理非常大消息数。

## <a name="create-a-namespace"></a>创建命名空间
若要开始在 Azure 中使用 Service Bus 主题和订阅，必须首先创建*服务命名空间*。 命名空间提供用于应用程序中的 Service Bus 资源进行寻址的范围容器。

创建命名空间：

1. 登录到[Azure 门户][Azure portal]。
2. 在门户的左侧的导航窗格中，单击**新建**，然后单击**企业集成**，然后单击**Service Bus**。
3. 在**创建命名空间**对话框中，输入命名空间名称。 系统会立即检查以查看名称是否可用。
4. 确保该命名空间名称可用之后，选择定价层 （基本、 标准或高级）。
5. 在**订阅**字段中，选择要在其中创建命名空间的 Azure 订阅。
6. 在**资源组**字段中，选择现有资源组的命名空间将实时，或者创建一个新。      
7. 在**位置**，选择的国家或地区应在其中托管你的命名空间。
   
    ![创建命名空间][create-namespace]
8. 单击**创建**按钮。 系统现在创建你的命名空间，并启用它。 你可能需要等待几分钟，因为系统设置资源为你的帐户。

### <a name="obtain-the-credentials"></a>获取的凭据
1. 在命名空间列表中，单击新创建的命名空间名称。
2. 在**Service Bus 命名空间**边栏选项卡，单击**共享访问策略**。
3. 在**共享访问策略**边栏选项卡，单击**RootManageSharedAccessKey**。
   
    ![连接信息][connection-info]
4. 在**策略： RootManageSharedAccessKey**边栏选项卡，单击复制按钮旁边**连接字符串 – 主键**，以将连接字符串复制到剪贴板以供将来使用。
   
    ![连接字符串][connection-string]

[Azure portal]: https://portal.azure.com
[create-namespace]: ./media/howto-service-bus-topics/create-namespace.png
[connection-info]: ./media/howto-service-bus-topics/connection-info.png
[connection-string]: ./media/howto-service-bus-topics/connection-string.png


