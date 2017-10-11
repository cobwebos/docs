请确保你已创建了服务总线命名空间中，如所示[此处][namespace-how-to]。

1. 登录到[Azure 门户][azure-portal]。
2. 在门户的左侧的导航窗格中，单击**Service Bus** (如果看不到**Service Bus**，单击**更多的服务**)。
3. 单击要在其中创建队列的命名空间。 在这种情况下，它是**nstest1**。
   
    ![创建队列][createqueue1]
4. 在**Service Bus 命名空间**边栏选项卡，选择**队列**，然后单击**添加队列**。
   
    ![选择队列][createqueue2]
5. 输入**队列名称**并保留其他值各自的默认值。
   
    ![选择新建][createqueue3]
6. 在边栏选项卡的底部，单击**创建**。

[createqueue1]: ./media/service-bus-create-queue-portal/create-queue1.png
[createqueue2]: ./media/service-bus-create-queue-portal/create-queue2.png
[createqueue3]: ./media/service-bus-create-queue-portal/create-queue3.png

[namespace-how-to]: ../articles/service-bus-messaging/service-bus-create-namespace-portal.md
[azure-portal]: https://portal.azure.com
