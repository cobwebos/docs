## 将消息发送到事件中心
在本部分中，您将编写用于将事件发送到事件中心的 Windows 控制台应用程序。

1. 在 Visual Studio 中创建新的 Visual C# 桌面应用程序项目使用**控制台应用程序**项目模板。将该项目**发件人**。

   	![][7]

2. 在解决方案资源管理器中右键单击解决方案，然后依次**解决方案管理 NuGet 包...**. 

	此时将显示管理 NuGet 包对话框。

3. 搜索`Microsoft Azure Service Bus`，单击**安装**，并接受使用条款。 

	![][8]

	这会下载、 安装，并添加对的引用 <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus/">Azure Service Bus 库 NuGet 包</a>.

4. 在顶部添加以下 using 语句**Program.cs**文件：

		using Microsoft.ServiceBus.Messaging;

5. 以下静态字段添加到**程序**类中，替换的值替换为您在上一节中创建事件集线器的名称和连接字符串与**发送**权限：

		static string eventHubName = "{event hub name}";
        static string connectionString = "{send connection string}";

6. 添加以下方法向**程序**类：

		static async Task SendingRandomMessages()
        {
            var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
            while (true)
            {
                try
                {
                    var message = Guid.NewGuid().ToString();
                    Console.WriteLine("{0} > Sending message: {1}", DateTime.Now.ToString(), message);
                    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                }
                catch (Exception exception)
                {
                    Console.ForegroundColor = ConsoleColor.Red;
                    Console.WriteLine("{0} > Exception: {1}", DateTime.Now.ToString(), exception.Message);
                    Console.ResetColor();
                }

                await Task.Delay(200);
            }
        }

	此方法将不断地将事件发送到事件中心有一个 200 毫秒的延迟。

7. 最后，添加以下行到**Main**方法：

		Console.WriteLine("Press Ctrl-C to stop the sender process");
        Console.WriteLine("Press Enter to start now");
        Console.ReadLine();
        SendingRandomMessages().Wait();


<!-- Images -->
[7]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp2.png
