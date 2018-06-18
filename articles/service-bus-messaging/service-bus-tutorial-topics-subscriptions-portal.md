---
title: 教程 - 通过 Azure 门户使用发布/订阅渠道和主题筛选器更新零售库存分类 | Microsoft Docs
description: 本教程介绍如何从主题和订阅发送和接收消息，以及如何使用 .NET 添加和使用筛选器规则
services: service-bus-messaging
author: sethmanheim
manager: timlt
ms.author: sethm
ms.date: 05/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: f504f3bf513a20d8590d9907106b1fd12f907877
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34651301"
---
# <a name="tutorial-update-inventory-using-azure-portal-and-topicssubscriptions"></a>教程：使用 Azure 门户和主题/订阅更新库存

Microsoft Azure 服务总线是一种多租户云消息传送服务，可以在应用程序和服务之间发送信息。 异步操作可实现灵活的中转消息传送、结构化的先进先出 (FIFO) 消息传送以及发布/订阅功能。 本教程介绍如何通过 Azure 门户和 .NET 将零售库存方案中的服务总线主题和订阅与发布/订阅渠道配合使用。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 使用 Azure 门户创建一个服务总线主题和一个或多个对该主题的订阅
> * 使用 .NET 代码添加主题筛选器
> * 创建两条具有不同内容的消息
> * 发送消息并验证它们是否已到达预期的订阅
> * 从订阅接收消息

此方案的一个示例是为多个零售店更新库存分类。 在此方案中，每个商店或商店组都获取适用于它们的消息来更新其分类。 本教程展示了如何使用订阅和筛选器实现此方案。 首先，创建一个包含 3 个订阅的主题，添加一些规则和筛选器，然后从主题和订阅发送和接收消息。

![主题](./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png)

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户][]。

## <a name="prerequisites"></a>先决条件

若要完成本教程，请确保已安装：

- [Visual Studio 2017 Update 3（版本 15.3 (26730.01)）](http://www.visualstudio.com/vs)或更高版本。
- [NET Core SDK](https://www.microsoft.com/net/download/windows) 2.0 或更高版本。

## <a name="service-bus-topics-and-subscriptions"></a>服务总线主题和订阅

每个[对主题的订阅](service-bus-messaging-overview.md#topics)都可以接收每条消息的副本。 主题在协议和语义方面与服务总线队列完全兼容。 服务总线主题支持一系列选择规则，这些规则具有筛选条件和用来设置或修改消息属性的可选操作。 规则每次匹配时，都会生成一条消息。 若要深入了解规则、筛选器和操作，请单击此[链接](topic-filters.md)。

## <a name="log-on-to-the-azure-portal"></a>登录到 Azure 门户。

首先转到 [Azure 门户][Azure portal]，使用 Azure 订阅登录。 第一步是创建类型为“消息传送”的服务总线命名空间。

## <a name="create-a-service-bus-namespace"></a>创建服务总线命名空间

服务总线消息传送命名空间提供唯一的作用域容器，可以通过其[完全限定的域名][]进行引用，而在该域名中，可以创建一个或多个队列、主题和订阅。 以下示例在新的或现有的[资源组](/azure/azure-resource-manager/resource-group-portal)中创建一个服务总线消息传送命名空间：

1. 在门户的左侧导航窗格中，依次单击“+ 创建资源”、“企业集成”和“服务总线”。
2. 在“创建命名空间”  对话框中，输入命名空间名称。 系统会立即检查该名称是否可用。
3. 在确保命名空间名称可用后，请选择定价层（标准或高级）。
4. 在“订阅”  字段中，选择要创建命名空间的 Azure 订阅。
5. 在“资源组”字段中，选择将保留命名空间的现有资源组，或者创建一个新资源组。      
6. 在“位置” 中，选择应托管命名空间的国家或地区。
7. 单击“创建”。 系统现已创建命名空间并已将其启用。 可能需要等待几分钟，因为系统将为帐户配置资源。

  ![命名空间](./media/service-bus-tutorial-topics-subscriptions-portal/create-namespace.png)

### <a name="obtain-the-management-credentials"></a>获取管理凭据

创建新的命名空间时，会自动生成一项初始的共享访问签名 (SAS) 规则，将一对主密钥和辅助密钥关联到一起，向每个密钥授予对命名空间的所有资产的完全控制权限。 若要复制初始规则，请执行以下步骤：

1. 单击“所有资源”，然后单击新创建的命名空间名称。
2. 在命名空间窗口中，单击“共享访问策略”。
3. 在“共享访问策略”屏幕中，单击“RootManageSharedAccessKey”。
4. 在“策略: RootManageSharedAccessKey”窗口中，单击“主连接字符串”旁边的“复制”按钮，将连接字符串复制到剪贴板供以后使用。 将此值粘贴到记事本或其他某个临时位置。

    ![connection-string][connection-string]
5. 重复上述步骤，将**主键**的值复制和粘贴到临时位置，供以后使用。

## <a name="create-a-topic-and-subscriptions"></a>创建主题和订阅

若要创建服务总线主题，请指定要在其中创建该主题的命名空间。 以下示例演示如何在门户中创建主题：

1. 在门户左侧的导航窗格中，单击“服务总线”（如果未看到“服务总线”，请单击“所有服务”）。
2. 单击要在其中创建主题的命名空间。
3. 在命名空间窗口中单击“主题”，然后在“主题”窗口中单击“+ 主题”。
4. 输入主题**名称**，其他值则保留默认值。
5. 在窗口底部，单击“创建”。
6. 记下主题名称。
7. 选择刚创建的主题。
8. 单击“+ 订阅”，输入订阅名称 **S1**，所有其他值则保留默认值。
9. 再重复上述步骤两次，创建名为 **S2** 和 **S3** 的订阅。

## <a name="create-filter-rules-on-subscriptions"></a>在订阅上创建筛选规则

预配命名空间和主题/订阅并且拥有所需的凭据后，便可以在订阅上创建筛选规则，然后发送和接收消息。 可以在[此 GitHub 示例文件夹](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/GettingStarted\BasicSendReceiveTutorialwithFilters)中检查代码。

### <a name="send-and-receive-messages"></a>发送和接收消息

若要运行此代码，请执行以下操作：

1. 在命令提示符或 PowerShell 提示符窗口中发出以下命令，克隆[服务总线 GitHub 存储库](https://github.com/Azure/azure-service-bus/)：

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. 导航到示例文件夹 `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveTutorialwithFilters`。

3. 在本教程的[获取管理凭据](#obtain-the-management-credentials)部分获取复制到记事本的连接字符串。 此外还需要在上一部分创建的主题的名称。

4. 在命令提示符窗口中键入以下命令：

   ```shell
   dotnet build
   ```

5. 导航到 `BasicSendReceiveTutorialwithFilters\bin\Debug\netcoreapp2.0` 文件夹。

6. 键入以下命令以运行程序。 请务必将 `myConnectionString` 替换为先前获得的值，将 `myTopicName` 替换为所创建主题的名称：

   ```shell
   dotnet BasicSendReceiveTutorialwithFilters.dll -ConnectionString "myConnectionString" -TopicName "myTopicName"
   ``` 
7. 首先按控制台中的说明选择筛选器创建操作。 在创建筛选器时，其中一项操作是删除默认筛选器。 使用 PowerShell 或 CLI 时，不需删除默认筛选器，但如果是在代码中操作，则必须删除它们。 控制台命令 1 和 3 用于管理以前创建的订阅上的筛选器：

   - 执行操作 1：删除默认筛选器。
   - 执行操作 2：添加自己的筛选器。
   - 执行操作 3：（可选）删除自己的筛选器。 请注意，这不会重新创建默认筛选器。

    ![显示 2 的输出](./media/service-bus-tutorial-topics-subscriptions-portal/create-rules.png)

8. 创建筛选器以后，即可发送消息。 按 4 即可观察到 10 条消息发送到主题：

    ![发送输出](./media/service-bus-tutorial-topics-subscriptions-portal/send-output.png)

9. 按 5 即可观察到这些消息被接收。 如果没有返回 10 条消息，请按“m”以显示菜单，然后再次按 5。

    ![接收输出](./media/service-bus-tutorial-topics-subscriptions-portal/receive-output.png)

## <a name="clean-up-resources"></a>清理资源

不再需要命名空间和队列时，可将其删除。 为此，请在门户中选择这些资源，然后单击“删除”。

## <a name="understand-the-sample-code"></a>了解示例代码

此部分包含有关示例代码功能的更多详细信息。

### <a name="get-connection-string-and-topic"></a>获取连接字符串和主题

首先，此代码声明一组变量，这些变量推动程序的剩余执行操作。

```csharp
string ServiceBusConnectionString;
string TopicName;

static string[] Subscriptions = { "S1", "S2", "S3" };
static IDictionary<string, string[]> SubscriptionFilters = new Dictionary<string, string[]> {
    { "S1", new[] { "StoreId IN('Store1', 'Store2', 'Store3')", "StoreId = 'Store4'"} },
    { "S2", new[] { "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'" } },
    { "S3", new[] { "sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')" } }
};
// You can have only have one action per rule and this sample code supports only one action for the first filter, which is used to create the first rule. 
static IDictionary<string, string> SubscriptionAction = new Dictionary<string, string> {
    { "S1", "" },
    { "S2", "" },
    { "S3", "SET sys.Label = 'SalesEvent'"  }
};
static string[] Store = { "Store1", "Store2", "Store3", "Store4", "Store5", "Store6", "Store7", "Store8", "Store9", "Store10" };
static string SysField = "sys.To";
static string CustomField = "StoreId";
static int NrOfMessagesPerStore = 1; // Send at least 1.
```

连接字符串和主题名称通过所示的命令行参数传入，然后通过 `Main()` 方法读取：

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string TopicName = "";

    for (int i = 0; i < args.Length; i++)
    {
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i + 1]}");
            ServiceBusConnectionString = args[i + 1]; // Alternatively enter your connection string here.
        }
        else if (args[i] == "-TopicName")
        {
            Console.WriteLine($"TopicName: {args[i + 1]}");
            TopicName = args[i + 1]; // Alternatively enter your queue name here.
        }
    }

    if (ServiceBusConnectionString != "" && TopicName != "")
    {
        Program P = StartProgram(ServiceBusConnectionString, TopicName);
        P.PresentMenu().GetAwaiter().GetResult();
    }
    else
    {
        Console.WriteLine("Specify -Connectionstring and -TopicName to execute the example.");
        Console.ReadKey();
    }
}
```

### <a name="remove-default-filters"></a>删除默认筛选器

创建订阅时，服务总线会为每个订阅创建默认筛选器。 该筛选器允许接收发送到主题的每条消息。 若要使用自定义筛选器，则可删除默认筛选器，如以下代码所示：

```csharp
private async Task RemoveDefaultFilters()
{
    Console.WriteLine($"Starting to remove default filters.");

    try
    {
        foreach (var subscription in Subscriptions)
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, subscription);
            await s.RemoveRuleAsync(RuleDescription.DefaultRuleName);
            Console.WriteLine($"Default filter for {subscription} has been removed.");
            await s.CloseAsync();
        }

        Console.WriteLine("All default Rules have been removed.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="create-filters"></a>创建筛选器

以下代码添加在本教程中定义的自定义筛选器：

```csharp
private async Task CreateCustomFilters()
{
    try
    {
        for (int i = 0; i < Subscriptions.Length; i++)
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, Subscriptions[i]);
            string[] filters = SubscriptionFilters[Subscriptions[i]];
            if (filters[0] != "")
            {
                int count = 0;
                foreach (var myFilter in filters)
                {
                    count++;

                    string action = SubscriptionAction[Subscriptions[i]];
                    if (action != "")
                    {
                        await s.AddRuleAsync(new RuleDescription
                        {
                            Filter = new SqlFilter(myFilter),
                            Action = new SqlRuleAction(action),
                            Name = $"MyRule{count}"
                        });
                    }
                    else
                    {
                        await s.AddRuleAsync(new RuleDescription
                        {
                            Filter = new SqlFilter(myFilter),
                            Name = $"MyRule{count}"
                        });
                    }
                }
            }

            Console.WriteLine($"Filters and actions for {Subscriptions[i]} have been created.");
        }

        Console.WriteLine("All filters and actions have been created.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="remove-your-custom-created-filters"></a>删除创建的自定义筛选器

若要删除订阅上的所有筛选器，请执行下述演示了详细操作方法的代码：

```csharp
private async Task CleanUpCustomFilters()
{
    foreach (var subscription in Subscriptions)
    {
        try
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, subscription);
            IEnumerable<RuleDescription> rules = await s.GetRulesAsync();
            foreach (RuleDescription r in rules)
            {
                await s.RemoveRuleAsync(r.Name);
                Console.WriteLine($"Rule {r.Name} has been removed.");
            }
            await s.CloseAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
    Console.WriteLine("All default filters have been removed.\n");

    await PresentMenu();
}
```

### <a name="send-messages"></a>发送消息

向主题发送消息类似于向队列发送消息。 此示例演示如何使用任务列表和异步处理来发送消息：

```csharp
public async Task SendMessages()
{
    try
    {
        TopicClient tc = new TopicClient(ServiceBusConnectionString, TopicName);

        var taskList = new List<Task>();
        for (int i = 0; i < Store.Length; i++)
        {
            taskList.Add(SendItems(tc, Store[i]));
        }

        await Task.WhenAll(taskList);
        await tc.CloseAsync();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }
    Console.WriteLine("\nAll messages sent.\n");
}

private async Task SendItems(TopicClient tc, string store)
{
    for (int i = 0; i < NrOfMessagesPerStore; i++)
    {
        Random r = new Random();
        Item item = new Item(r.Next(5), r.Next(5), r.Next(5));

        // Note the extension class which is serializing an deserializing messages
        Message message = item.AsMessage();
        message.To = store;
        message.UserProperties.Add("StoreId", store);
        message.UserProperties.Add("Price", item.getPrice().ToString());
        message.UserProperties.Add("Color", item.getColor());
        message.UserProperties.Add("Category", item.getItemCategory());

        await tc.SendAsync(message);
        Console.WriteLine($"Sent item to Store {store}. Price={item.getPrice()}, Color={item.getColor()}, Category={item.getItemCategory()}"); ;
    }
}
```

### <a name="receive-messages"></a>接收消息

消息再次通过任务列表接收，代码使用批处理。 可以使用批处理进行发送和接收，但此示例仅演示如何进行批量接收。 事实上，你不会从循环中脱离出来，而是继续进行循环并设置更大的时间跨度，例如一分钟。 对代理的接收调用在此时段内会始终保持开放状态，消息在到达后会立即返回，然后发出新的接收调用。 此概念称为长轮询。 更典型的选项是使用接收泵，详见[快速入门](service-bus-quickstart-portal.md)以及存储库中的多个其他的示例。

```csharp
public async Task Receive()
{
    var taskList = new List<Task>();
    for (var i = 0; i < Subscriptions.Length; i++)
    {
        taskList.Add(this.ReceiveMessages(Subscriptions[i]));
    }

    await Task.WhenAll(taskList);
}

private async Task ReceiveMessages(string subscription)
{
    var entityPath = EntityNameHelper.FormatSubscriptionPath(TopicName, subscription);
    var receiver = new MessageReceiver(ServiceBusConnectionString, entityPath, ReceiveMode.PeekLock, RetryPolicy.Default, 100);

    while (true)
    {
        try
        {
            IList<Message> messages = await receiver.ReceiveAsync(10, TimeSpan.FromSeconds(2));
            if (messages.Any())
            {
                foreach (var message in messages)
                {
                    lock (Console.Out)
                    {
                        Item item = message.As<Item>();
                        IDictionary<string, object> myUserProperties = message.UserProperties;
                        Console.WriteLine($"StoreId={myUserProperties["StoreId"]}");

                        if (message.Label != null)
                        {
                            Console.WriteLine($"Label={message.Label}");
                        }

                        Console.WriteLine(
                            $"Item data: Price={item.getPrice()}, Color={item.getColor()}, Category={item.getItemCategory()}");
                    }

                    await receiver.CompleteAsync(message.SystemProperties.LockToken);
                }
            }
            else
            {
                break;
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }

    await receiver.CloseAsync();
}
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用 Azure 门户预配资源，然后从服务总线主题及其订阅发送并接收消息。 你已了解如何：

> [!div class="checklist"]
> * 使用 Azure 门户创建一个服务总线主题和一个或多个对该主题的订阅
> * 使用 .NET 代码添加主题筛选器
> * 创建两条具有不同内容的消息
> * 发送消息并验证它们是否已到达预期的订阅
> * 从订阅接收消息

若要通过更多示例来了解如何发送和接收消息，请从 [GitHub 上的服务总线示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted)着手。

若要详细了解如何使用服务总线的发布/订阅功能，请转到下一教程。

> [!div class="nextstepaction"]
> [使用 PowerShell 和主题/订阅更新库存](service-bus-tutorial-topics-subscriptions-powershell.md)

[免费帐户]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[完全限定的域名]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[connection-string]: ./media/service-bus-tutorial-topics-subscriptions-portal/connection-string.png
[service-bus-flow]: ./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png