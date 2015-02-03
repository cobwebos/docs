## 使用 EventProcessorHost 接收消息

[EventProcessorHost]是通过管理持久检查点，简化了从事件中心接收事件的.NET 类，它并行接收从这些事件中心。使用[EventProcessorHost]，您可以将事件拆分跨多个接收方，即使在不同节点中托管。此示例演示如何使用[EventProcessorHost]的一位接收方。[扩展的事件处理示例]演示如何使用[EventProcessorHost]与多个接收方。

接收模式的有关事件中心的详细信息，请参阅[事件中心概述]。

若要使用[EventProcessorHost]，您必须具有[Azure 存储帐户]：

1. 登录到[Azure 管理门户]，然后单击**新建**在屏幕的底部。

2. 单击**Data Services**，然后**存储**，然后**快速创建**，然后键入您的存储帐户的名称。选择所需的区域，然后单击**创建存储帐户**。

   	![][11]

3. 单击新创建的存储帐户，然后单击**管理访问密钥**：

   	![][12]

	将复制以供将来使用的访问密钥。

4. 在 Visual Studio 中创建新的 Visual C# 桌面应用程序项目使用**控制台应用程序**项目模板。将该项目**接收方**。

   	![][14]

5. 在解决方案资源管理器中右键单击解决方案，然后依次**管理 NuGet 包**。 

	**管理 NuGet 包**对话框随即出现。

6. Search for `Microsoft Azure Service Bus Event Hub - EventProcessorHost`, click **Install**, and accept the terms of use. 

	![][13]

	这会下载、 安装，并添加对引用 < href ="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost"> Azure Service Bus 事件中心 EventProcessorHost NuGet 程序包 </a >，提供其依赖关系。

7. 创建新的类称为**SimpleEventProcessor**，该文件顶部添加以下语句：

		using Microsoft.ServiceBus.Messaging;
		using System.Diagnostics;
		using System.Threading.Tasks;

	然后，将插入以下代码作为类的正文：

		class SimpleEventProcessor : IEventProcessor
	    {
	        Stopwatch checkpointStopWatch;
	        
	        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
	        {
	            Console.WriteLine(string.Format("Processor Shuting Down.  Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason.ToString()));
	            if (reason == CloseReason.Shutdown)
	            {
	                await context.CheckpointAsync();
	            }
	        }
	
	        Task IEventProcessor.OpenAsync(PartitionContext context)
	        {
	            Console.WriteLine(string.Format("SimpleEventProcessor initialize.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset));
	            this.checkpointStopWatch = new Stopwatch();
	            this.checkpointStopWatch.Start();
	            return Task.FromResult<object>(null);
	        }
	
	        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
	        {
	            foreach (EventData eventData in messages)
	            {
	                string data = Encoding.UTF8.GetString(eventData.GetBytes());
	                
	                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{2}'",
	                    context.Lease.PartitionId, data));
	            }
	
	            //Call checkpoint every 5 minutes, so that worker can resume processing from the 5 minutes back if it restarts.
	            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
	            {
	                await context.CheckpointAsync();
	                lock (this)
	                {
	                    this.checkpointStopWatch.Reset();
	                }
	            }
	        }
	    }

	此类将调用**EventProcessorHost**以处理从事件中心收到的事件。请注意， `SimpleEventProcessor`类使用秒表来定期调用的检查点方法**EventProcessorHost**上下文。这可确保，如果接收方重新启动，则它将丢失不超过五分钟的处理工作。

8. 在**程序**类中，在顶部添加以下 using 语句：

		using Microsoft.ServiceBus.Messaging;
		using System.Threading.Tasks;
	
	然后，添加下面的代码**Main**方法中，替换事件中心的名称和连接字符串的存储帐户和前面的章节中复制的密钥：

		string eventHubConnectionString = "{event hub connection string}";
        string eventHubName = "{event hub name}";
        string storageAccountName = "{storage account name}";
        string storageAccountKey = "{storage account key}";
        string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
                    storageAccountName, storageAccountKey);

        string eventProcessorHostName = Guid.NewGuid().ToString();
        EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
        eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>().Wait();
            
        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();

> [AZURE.NOTE] 本教程使用的单个实例[EventProcessorHost]。若要增加吞吐量，建议您运行的多个实例[EventProcessorHost]，如下所示[扩展的事件处理示例]。在这些情况下，不同的实例自动协调与每个其他以便进行负载平衡接收的事件。如果您希望为每个进程的多个接收方 * 全部 * 事件，则必须使用**ConsumerGroup**概念。从不同的计算机中接收事件，它可能会很有用指定的名称[EventProcessorHost]实例基于机 （或角色） 中部署它们。有关这些主题的详细信息，请参阅[事件中心概述]。

<!-- Links -->
[事件中心概述]: http://msdn.microsoft.com/zh-cn/library/azure/dn821413.aspx
[扩展的事件处理示例]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[Azure 存储帐户]: http://www.windowsazure.cn/zh-cn/documentation/articles/storage-create-storage-account/
[EventProcessorHost]: http://msdn.microsoft.com/zh-cn/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx 

<!-- Images -->

[11]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp3.png
[13]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp1.png
[14]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png
