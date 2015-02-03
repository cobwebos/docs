<h2><a name="what-are-service-bus-topics"></a>什么是 Service Bus 主题和订阅</h2>

Service Bus 主题和订阅支持**发布/订阅消息通信**模型。使用主题和订阅时，分布式应用程序的组件不直接相互通信，而是通过充当中介的主题的消息交换。

![TopicConcepts](./media/howto-service-bus-topics/sb-topics-01.png)

与 Service Bus 队列，其中由单个使用者处理每条消息时，不同主题和订阅提供**一多**使用发布/订阅模式的通信形式。可向一个主题注册多个订阅。当一条消息发送到主题时，它然后由对每个订阅可用来处理独立。

主题订阅类似于接收发送至主题的消息副本的虚拟队列。您可以选择注册每个订阅的方式，以便您可以筛选/限制哪些主题订阅接收到主题消息的主题的筛选器规则。

利用 Service Bus 主题和订阅，您可以进行扩展以处理跨大量用户和应用程序的许多消息。

<h2><a name="create-a-service-namespace"></a>创建服务命名空间</h2>

若要开始在 Azure 中使用 Service Bus 主题和订阅，必须首先创建一个服务命名空间。服务命名空间提供用于在您的应用程序中的 Service Bus 资源进行寻址的范围容器。

创建服务命名空间：

1.  登录到[Azure 管理门户][]。

2.  在管理门户的左侧的导航窗格中，单击
    **Service Bus**。

3.  在管理门户的下方窗格中，单击**创建**。   
    ![][0]

4.  在**添加新的命名空间**对话框中，输入命名空间名称。
    系统会立即检查该名称是否可用。   
    ![][2]

5.  确保命名空间名称可用后，选择的国家或地区，应在其中托管您的命名空间 （请确保使用相同的国家/地区部署你的计算资源）。

	重要：选取**同一区域**您想要选择用于部署应用程序。这将为你提供最佳性能。

6. 	单击复选标记。系统现已创建您的服务命名空间并已将其启用。您可能需要等待几分钟，因为系统将为您的帐户配置资源。

	![][6]


<h2><a name="obtain-default-credentials"></a>获得命名空间的默认管理凭据</h2>

若要执行管理操作，例如对新的命名空间中创建主题或订阅中，您必须获取该命名空间的管理凭据。从管理门户或从 Visual Studio 服务器资源管理器，您可以获取这些凭据。

###若要从门户获取管理凭据

1.  在左侧的导航窗格中，单击**Service Bus**节点以显示可用命名空间的列表：   
    ![][0]

2.  从显示的列表中选择刚刚创建的命名空间：   
    ![][3]

3.  单击**连接信息**。   
    ![][4]

4.  在**访问连接信息**对话框中，查找**默认颁发者**和**默认密钥**条目。记下这些值，因为您将在下面使用此信息来对命名空间执行操作。 

###若要从服务器资源管理器获取管理凭据

若要获取连接信息 （而不管理门户中使用 Visual Studio，请按照上述过程[此处](http://http://msdn.microsoft.com/zh-cn/library/windowsazure/ff687127.aspx)，一节中**从 Visual Studio 连接到 Azure**。当您登录到 Azure， **Service Bus**节点下的**Microsoft Azure**树中的在服务器资源管理器会自动填充的任何已创建的命名空间。右键单击任何命名空间，然后单击**属性**若要查看连接字符串和其他与在 Visual Studio 中显示此命名空间关联的元数据**属性**窗格。 

记下**SharedAccessKey**值，或将其复制到剪贴板：

![][34]

 
  [Azure 管理门户]: http://manage.windowsazure.com
  [0]: ./media/howto-service-bus-topics/sb-queues-13.png
  [2]: ./media/howto-service-bus-topics/sb-queues-04.png
  [3]: ./media/howto-service-bus-topics/sb-queues-09.png
  [4]: ./media/howto-service-bus-topics/sb-queues-06.png
  
  [6]: ./media/howto-service-bus-topics/getting-started-multi-tier-27.png
  [34]: ./media/howto-service-bus-topics/VSProperties.png
