---
title: "创建与 Azure 服务绑定的 Azure Function | Microsoft Docs"
description: "生成 Azure Function，即与其他 Azure 服务交互的无服务应用程序。"
services: functions
documentationcenter: dev-center-name
author: yochay
manager: manager-alias
editor: 
tags: 
keywords: "Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务体系结构"
ms.assetid: ab86065d-6050-46c9-a336-1bfc1fa4b5a1
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/25/2016
ms.author: rachelap@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9ffd199c9e3c621a808ade109ed044b6c9b689b7


---
# <a name="create-an-azure-function-which-binds-to-an-azure-service"></a>创建要与 Azure 服务绑定的 Azure Function
[!INCLUDE [Getting Started Note](../../includes/functions-getting-started.md)]

此短片介绍如何创建在 Azure 队列上侦听消息的 Azure Function 并将消息复制到 Azure Blob。

## <a name="watch-the-video"></a>观看视频
>[!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-an-Azure-Function-which-binds-to-an-Azure-service/player]
>
>

## <a name="create-an-input-queue-trigger-function"></a>创建输入队列触发器函数
此函数的目的是每隔 10 秒向队列写入一条消息。 为了实现这个目的，必须先创建函数和消息队列，然后添加代码，以便将消息写入新创建的队列。

1. 转到 Azure 门户并找到 Azure Function App。
2. 单击“新建函数” > “计时器触发器 - Node”。 将函数命名为 **FunctionsBindingsDemo1**
3. 为计划输入值“0/10     *”。 此值为 cron 表达式形式。 这样会将计时器计划为每 10 秒运行一次。
4. 单击“创建”按钮创建该函数。
   
    ![添加触发器计时器函数](./media/functions-create-an-azure-connected-function/new-trigger-timer-function.png)
5. 查看日志中的活动，确保函数正常运行。 可能需要单击右上角的“日志”链接才能显示日志窗格。
   
   ![查看日志，确保函数正常运行](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-view-log.png)

### <a name="add-a-message-queue"></a>添加消息队列
1. 转到“集成”选项卡。
2. 选择“新建输出” > “Azure 存储队列” > “选择”。
3. 在“消息参数名称”文本框中输入“myQueueItem”。
4. 选择一个存储帐户，如果没有现成的存储帐户，可单击“新建”创建一个。
5. 在“队列名称”文本框中，输入“functions-bindings”。
6. 单击“保存” 。  
   
   ![添加触发器计时器函数](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-integrate-tab.png)

### <a name="write-to-the-message-queue"></a>写入到消息队列
1. 回到“开发”选项卡，在现有代码后将以下代码添加到该函数：
   
    ```javascript
   
    function myQueueItem() 
      {
        return {
        msg: "some message goes here",
        time: "time goes here"
      }
    }
   
    ```
2. 修改现有的函数代码，调用在步骤 1 中添加的代码。 将以下代码插入到函数的第 9 行，*if* 语句的后面。
   
    ```javascript
   
    var toBeQed = myQueueItem();
    toBeQed.time = timeStamp;
    context.bindings.myQueue = toBeQed;
   
    ```
   
    此代码创建 **myQueueItem** 并将其 **time** 属性设置为当前的时间戳。 然后，它会将新队列项添加到上下文的 myQueue 绑定。
3. 单击“保存并运行”。
4. 在 Visual Studio 中查看队列，确保代码正常运行。
   
   * 打开 Visual Studio，转到“视图” > “Cloud Explorer”。
   * 找到在创建 myQueue 队列时使用过的存储帐户和 **functions-bindings** 队列。 应该会看到多行日志数据。 用户可能需要通过 Visual Studio 登录到 Azure。  

## <a name="create-an-output-queue-trigger-function"></a>创建输出队列触发器函数
1. 单击“新建函数” > “队列触发器 - C#”。 将函数命名为 **FunctionsBindingsDemo2**。 请注意，可以在同一函数应用中混合使用多种语言（此示例混合使用了 Node 和 C#）。
2. 在“队列名称”字段中，输入“functions-bindings”。
3. 选择要使用的存储帐户，或者新建一个。
4. 单击“创建” 
5. 查看函数的日志并查看 Visual Studio 中是否进行了更新，确保新函数正常运行。 函数的日志显示函数正在运行且项目已取消排队。 由于函数以输入触发器的形式绑定到 **functions-bindings** 输出队列，因此在 Visual Studio 中刷新 **functions-bindings** 队列就可以看到相关项目已消失。 这些项目已取消排队。   
   
   ![添加输出队列计时器函数](./media/functions-create-an-azure-connected-function/functionsbindingsdemo2-integrate-tab.png)   

### <a name="modify-the-queue-item-type-from-json-to-object"></a>将队列项目类型从 JSON 修改为对象
1. 将 **FunctionsBindingsDemo2** 中的代码替换为以下代码：    
   
    ```cs
   
    using System;
   
    public static void Run(QItem myQueueItem, ICollector<TableItem> myTable, TraceWriter log)
    {
      TableItem myItem = new TableItem
      {
        PartitionKey = "key",
        RowKey = Guid.NewGuid().ToString(),
        Time = DateTime.Now.ToString("hh.mm.ss.ffffff"),
        Msg = myQueueItem.Msg,
        OriginalTime = myQueueItem.Time    
      };
      log.Verbose($"C# Queue trigger function processed: {myQueueItem.Msg} | {myQueueItem.Time}");
    }
   
    public class TableItem
    {
      public string PartitionKey {get; set;}
      public string RowKey {get; set;}
      public string Time {get; set;}
      public string Msg {get; set;}
      public string OriginalTime {get; set;}
    }
   
    public class QItem
    {
      public string Msg { get; set;}
      public string Time { get; set;}
    }
   
    ```
   
    此代码添加了 **TableItem** 和 **QItem** 这两个类，用于从队列读取数据以及向队列写入数据。 另外还修改了 **Run** 函数，使之接受 **QItem** 和 **TraceWriter** 参数而不是 **string** 和 **TraceWriter** 参数。 
2. 单击“保存”按钮  。
3. 检查日志，确保代码正常运行。 请注意，Azure Functions 会自动为用户序列化和反序列化对象，因此可以通过面向对象的方式方便地访问队列，以便传递数据。 

## <a name="store-messages-in-an-azure-table"></a>在 Azure 表中存储消息
队列可以协同运行以后，即可加入 Azure 表来永久存储队列数据。

1. 转到“集成”选项卡。
2. 创建用于输出的 Azure 存储表，将其命名为 **myTable**。
3. 出现“应将数据写入哪个表?”这个问题时，回答 **functionsbindings**。
4. 将 **PartitionKey** 设置从 **{project-id}** 更改为 **{partition}**。
5. 选择存储帐户，或者新建一个。
6. 单击“保存” 。
7. 转到“开发”选项卡。
8. 创建用来表示 Azure 表的 **TableItem** 类，并修改 Run 函数，使之接受新建的 TableItem 对象。 请注意，必须使用 **PartitionKey** 和 **RowKey** 属性才能正常运行。
   
    ```cs
   
    public static void Run(QItem myQueueItem, ICollector<TableItem> myTable, TraceWriter log)
    {    
      TableItem myItem = new TableItem
      {
        PartitionKey = "key",
        RowKey = Guid.NewGuid().ToString(),
        Time = DateTime.Now.ToString("hh.mm.ss.ffffff"),
        Msg = myQueueItem.Msg,
        OriginalTime = myQueueItem.Time    
      };
   
      log.Verbose($"C# Queue trigger function processed: {myQueueItem.RowKey} | {myQueueItem.Msg} | {myQueueItem.Time}");
    }
   
    public class TableItem
    {
      public string PartitionKey {get; set;}
      public string RowKey {get; set;}
      public string Time {get; set;}
      public string Msg {get; set;}
      public string OriginalTime {get; set;}
    }
    ```
9. 单击“保存” 。
10. 查看函数的日志并在 Visual Studio 中查看，确保代码正常运行。 若要在 Visual Studio 中进行验证，可使用 **Cloud Explorer** 导航到 **functionbindings** Azure 表，验证其中是否存在行。

[!INCLUDE [Getting Started Note](../../includes/functions-bindings-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO2-->


