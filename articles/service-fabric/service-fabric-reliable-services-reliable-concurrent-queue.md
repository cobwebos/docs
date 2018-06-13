---
title: Azure Service Fabric 中的可靠并发队列
description: 可靠并发队列是一种高吞吐量队列，适用于并行排队和取消排队。
services: service-fabric
documentationcenter: .net
author: sangarg
manager: timlt
editor: raja,tyadam,masnider,vturecek
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: sangarg
ms.openlocfilehash: e04123f7870921a2979564d0f6c68424d4d7711c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206571"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Azure Service Fabric 中的可靠并发队列简介
可靠并发队列是一种异步的、事务性的已复制队列，其特点是排队和取消排队操作的高并发性。 它旨在降低[可靠队列](https://msdn.microsoft.com/library/azure/dn971527.aspx)提供的严格的 FIFO 排序要求，代之以“尽力排序”要求，从而提高吞吐量并降低延迟。

## <a name="apis"></a>API

|并发队列                |可靠并发队列                                         |
|--------------------------------|------------------------------------------------------------------|
| void Enqueue(T item)           | Task EnqueueAsync(ITransaction tx, T item)                       |
| bool TryDequeue(out T result)  | Task< ConditionalValue < T > > TryDequeueAsync(ITransaction tx)  |
| int Count()                    | long Count()                                                     |

## <a name="comparison-with-reliable-queuehttpsmsdnmicrosoftcomlibraryazuredn971527aspx"></a>与[可靠队列](https://msdn.microsoft.com/library/azure/dn971527.aspx)比较

可靠并发队列作为[可靠队列](https://msdn.microsoft.com/library/azure/dn971527.aspx)的替代推出， 其使用范围局限于不需要严格 FIFO 排序的情况，这种情况下，在尽量保证 FIFO 的同时，还需要考虑到并发性。  [可靠队列](https://msdn.microsoft.com/library/azure/dn971527.aspx)以锁定方式强制实施 FIFO 排序，一次只允许一个事务排队，一个事务取消排队。 相对而言，可靠并发队列对排序的要求较松，允许任意数目的并发事务交叉进行排队和取消排队操作。 可靠并发队列的原则是“尽力排序”，但无法保证两个值的相对顺序。

在有多个并发事务需要执行排队和/或取消排队操作的情况下，可靠并发队列相对于[可靠队列](https://msdn.microsoft.com/library/azure/dn971527.aspx)而言可以提高吞吐量并降低延迟。

可靠并发队列的一个使用示例是[消息队列](https://en.wikipedia.org/wiki/Message_queue)方案。 在该方案中，一个或多个消息生成者会创建项并将其添加到队列中，同时还会有一个或多个消息使用者从队列拉取消息并对其进行处理。 多个生成者和使用者可以独立操作，使用并发事务来处理队列。

## <a name="usage-guidelines"></a>使用指南
* 队列希望队列中的项的保留期较短。 换句话说，项呆在队列中的时间不宜过长。
* 队列不保证严格的 FIFO 排序。
* 队列不读取自己的写入。 项在事务中排队时，该项对于同一事务中的取消排队者来说为不可见。
* 取消排队不是相互隔离的。 如果项 A 在事务 txnA 中取消排队，则即使 txnA 尚未提交，项 A 也不会对并发事务 txnB 可见。  如果 txnA 中止，A 会立刻变得对 txnB 可见。
* 可以先使用 TryDequeueAsync，再中止事务，从而实现 TryPeekAsync 行为。 “编程模式”部分提供了一个这样的示例。
* 计数是非事务性的。 可以通过计数来了解队列中的元素数目，但计数只代表一个时间点的情况，可靠性不强。
* 不应在事务处于活动状态时对取消排队项执行开销昂贵的处理，以免事务长时间运行，对系统造成性能影响。

## <a name="code-snippets"></a>代码片段
让我们看看一些代码片段及其预期输出。 本部分忽略异常处理。

### <a name="enqueueasync"></a>EnqueueAsync
下面是使用 EnqueueAsync 的一些代码片段及其预期输出。

- 案例 1：单个排队任务

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

假定任务已成功完成，且没有并发事务在修改队列。 用户可以预期队列包含的项采用以下顺序：

> 10, 20

> 20, 10


- 案例 2：并行排队任务

```
// Parallel Task 1
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}

// Parallel Task 2
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 30, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 40, cancellationToken);

    await txn.CommitAsync();
}
```

假定任务已成功完成且是以并行方式运行的，同时没有其他并发事务在修改队列。 无法推断队列中项的顺序。 就此代码片段来说，项的显示顺序可能是 4! 种 可能的顺序之一。  队列会尝试让项保持原有的（排队）顺序，但在出现并发操作或错误的情况下，也可能会强制其重新排序。


### <a name="dequeueasync"></a>DequeueAsync
下面是使用 TryDequeueAsync 的一些代码片段及预期输出。 假定已在队列中填充以下项：
> 10, 20, 30, 40, 50, 60

- 案例 1：单个取消排队任务

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

假定任务已成功完成，且没有并发事务在修改队列。 由于无法推断队列中项的顺序，可能会采用任意顺序取消任意三个项的排队。 队列会尝试让项保持原有的（排队）顺序，但在出现并发操作或错误的情况下，也可能会强制其重新排序。  

- 案例 2：并行取消排队任务

```
// Parallel Task 1
List<int> dequeue1;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}

// Parallel Task 2
List<int> dequeue2;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}
```

假定任务已成功完成且是以并行方式运行的，同时没有其他并发事务在修改队列。 由于无法推断队列中项的顺序，列表 dequeue1 和 dequeue2 均会包含采用任意顺序的任意两个项。

同一项不会出现在两个列表中。 因此，如果 dequeue1 包含 10、30，则 dequeue2 就会包含 20、40。

- 案例 3：通过中止事务排定取消排队任务顺序

中止正在取消排队的事务，项就会重新回到队列头。 项回到队列头的顺序是不确定的。 请看以下代码：

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
假定项在取消排队时的顺序如下：
> 10, 20

中止事务后，项会采用以下顺序之一添加回队列头：
> 10, 20

> 20, 10

事务未成功提交的所有案例均是如此。

## <a name="programming-patterns"></a>编程模式
此部分介绍一些编程模式，在使用 ReliableConcurrentQueue 时可能会用到这些模式。

### <a name="batch-dequeues"></a>按批取消排队
建议的编程模式是，通过使用者任务按批取消排队，而不是一次执行一个取消排队操作。 用户可以选择限制每个批处理之间的延迟，或者限制批处理大小。 以下代码片段显示了此编程模型。  请注意，在此示例中，提交事务后，处理就会开始。因此，如果在处理过程中发生错误，则会丢失未处理的项，这些项不会得到处理。  也可以让处理在事务范围内进行，但这种方式可能会对性能造成负面影响，并且需要对已处理的项进行相应的安排。

```
int batchSize = 5;
long delayMs = 100;

while(!cancellationToken.IsCancellationRequested)
{
    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        for(int i = 0; i < batchSize; ++i)
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
            else
            {
                // else break the for loop
                break;
            }
        }

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }

    int delayFactor = batchSize - processItems.Count;
    await Task.Delay(TimeSpan.FromMilliseconds(delayMs * delayFactor), cancellationToken);
}
```

### <a name="best-effort-notification-based-processing"></a>基于通知的尽力处理
另一种有趣的编程模式是使用计数 API。 在这里，我们可以为队列实施基于通知的尽力处理。 可以使用队列计数来限制排队或取消排队任务。  请注意，与前面的示例一样，由于处理在事务外部进行，如果在处理过程中发生错误，则可能会丢失未处理的项。

```
int threshold = 5;
long delayMs = 1000;

while(!cancellationToken.IsCancellationRequested)
{
    while (this.Queue.Count < threshold)
    {
        cancellationToken.ThrowIfCancellationRequested();

        // If the queue does not have the threshold number of items, delay the task and check again
        await Task.Delay(TimeSpan.FromMilliseconds(delayMs), cancellationToken);
    }

    // If there are approximately threshold number of items, try and process the queue

    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
        } while (processItems.Count < threshold && ret.HasValue);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
}
```

### <a name="best-effort-drain"></a>尽力清空
考虑到数据结构的并发特性，不保证队列会被清空。  即使队列没有正在进行的用户操作，也可能会出现对 TryDequeueAsync 进行具体调用时不返回项（此前已排队并提交）的情况。  排队的项最终必定会变得对取消排队操作可见，但在没有带外通信机制的情况下，独立的使用者无法知道队列是否已达到稳定状态，即使系统已停止所有生成者且不允许新的排队操作。 因此，清空操作只能尽力而为，其执行情况如下所示。

用户应停止所有后续的生成者和使用者任务，等待正在进行的事务提交或中止，并尝试清空队列。  如果用户知道队列中预计会有多少项，则可设置一个通知，在所有项都已取消排队后发出指示。

```
int numItemsDequeued;
int batchSize = 5;

ConditionalValue ret;

do
{
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if(ret.HasValue)
            {
                // Buffer the dequeues
                processItems.Add(ret.Value);
            }
        } while (ret.HasValue && processItems.Count < batchSize);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
} while (ret.HasValue);
```

### <a name="peek"></a>速览
可靠并发队列不提供 TryPeekAsync API。 用户可以先使用 TryDequeueAsync，再中止事务，从而获取速览语义。 在以下示例中，仅当项的值大于 10 时，才会处理取消排队操作。

```
using (var txn = this.StateManager.CreateTransaction())
{
    ConditionalValue ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);
    bool valueProcessed = false;

    if (ret.HasValue)
    {
        if (ret.Value > 10)
        {
            // Process the item
            Console.WriteLine("Value : " + ret.Value);
            valueProcessed = true;
        }
    }

    if (valueProcessed)
    {
        await txn.CommitAsync();    
    }
    else
    {
        await txn.AbortAsync();
    }
}
```

## <a name="must-read"></a>必读
* [Reliable Services 快速入门](service-fabric-reliable-services-quick-start.md)
* [使用 Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Reliable Services 通知](service-fabric-reliable-services-notifications.md)
* [Reliable Services 备份和还原（灾难恢复）](service-fabric-reliable-services-backup-restore.md)
* [可靠状态管理器配置](service-fabric-reliable-services-configuration.md)
* [Service Fabric Web API 服务入门](service-fabric-reliable-services-communication-webapi.md)
* [Reliable Services 编程模型的高级用法](service-fabric-reliable-services-advanced-usage.md)
* [可靠集合的开发人员参考](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
