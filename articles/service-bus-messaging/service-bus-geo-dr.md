---
title: "Azure 服务总线异地灾难恢复 | Microsoft 文档"
description: "如何使用地理区域进行故障转移并在 Azure 服务总线中执行灾难恢复"
services: service-bus-messaging
documentationcenter: 
author: christianwolf42
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: sethm
ms.openlocfilehash: 49f2992245d694f85b7b1f1c34339f1445c9d699
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2017
---
# <a name="azure-service-bus-geo-disaster-recovery-preview"></a>Azure 服务总线异地灾难恢复（预览版）

当区域数据中心遭遇停机的情况下，在不同区域或数据中心进行数据处理以实现连续运行就显得至关重要。 在这种情况下，异地灾难恢复和异地复制对于任何企业而言都是至关重要的功能。 Azure 服务总线支持在命名空间级别的异地灾难恢复和异地复制。 

异地灾难恢复预览版目前仅在两个区域提供（美国中北部和美国中南部）。

## <a name="outages-and-disasters"></a>中断和灾难

[使应用程序免受服务总线中断和灾难影响的最佳做法](service-bus-outages-disasters.md)一文中对“中断”和“灾难”进行了区分，需特别注意。 中断是指 Azure 服务总线暂时不可用，可能会影响服务的某些组件，如消息存储，甚至是整个数据中心。 但是，问题解决后，服务总线将恢复可用。 通常情况下，中断不会导致消息或其他数据丢失。 例如，数据中心的电源故障可能会导致此类中断。

根据定义，灾难是指永久或长期丢失服务总线[缩放单位](service-bus-architecture.md#service-bus-scale-units)或数据中心。 数据中心可能会也可能不会恢复可用，或可能停用数小时或数天。 例如，火灾、洪灾或地震等可能导致此类灾难。 永久性灾难可能会导致一些消息或其他数据丢失。 不过，在大多数情况下，都不应该会有数据丢失，并且在数据中心备份后，便可以恢复消息。

Azure 服务总线的异地灾难恢复功能是一项面向灾难恢复的解决方案。 本文中所述的概念和工作流适用于灾难方案，而不适用于暂时或临时中断。  

## <a name="basic-concepts-and-terms"></a>基本概念和术语

灾难恢复功能可实现元数据灾难恢复，并且依赖于主要和辅助灾难恢复命名空间。 请注意，异地灾难恢复功能仅适用于[高级命名空间](service-bus-premium-messaging.md)。 不需要对连接字符串进行任何更改，因为连接是通过别名建立的。

本文涉及以下术语：

-  别名：主连接字符串。

-  主/辅助命名空间：描述别名所对应的命名空间。 主命名空间为“主动式”，会接收消息，辅助命名空间为“被动式”，不会接收消息。 两者之间的元数据保持同步，因此可以无缝接受消息，而不必更改任何应用程序代码。

-  元数据：Azure 服务总线中的对象的表示形式。 目前，我们仅支持元数据。

-  故障转移：激活辅助命名空间的过程。 一旦以前的主命名空间再次可用，你必须从中拉取消息，然后删除该命名空间。 若要创建另一个故障转移，请将新的辅助命名空间添加到配对。 若要在故障转移后重用以前的主命名空间，必须先从命名空间中删除所有现有实体。 请务必在这样做前先接收所有消息。

## <a name="failover-workflow"></a>故障转移工作流

下面的部分概述了设置初始故障转移的整个过程，以及如何从这一点开始。

![1][]

首先设置主命名空间和辅助命名空间，然后创建配对。 此配对提供可用于连接的别名。 由于使用别名，因此，无需更改连接字符串。 只有新的命名空间可以添加到故障转移配对。 最后，必须添加一些触发逻辑（例如，一些业务逻辑可以检测命名空间是否不可用，并启动故障转移）。 可以使用服务总线的[消息浏览](message-browsing.md)功能来检查命名空间的可用性。

在设置了监视和灾难恢复后，可以查看故障转移过程。 如果触发器启动故障转移，或手动启动故障转移，则需要以下两个步骤：

1. 如果又出现其他中断的情况下，你需要能够再次进行故障转移。 因此，请设置一个辅助被动命名空间，并更新配对。 
2. 一旦新命名空间可用，请从以前的主命名空间拉取消息。 在此之后，请重复使用，或者删除旧的主命名空间。

![#N/A][]

## <a name="set-up-disaster-recovery"></a>设置灾难恢复

本部分介绍如何生成你自己的服务总线异地灾难恢复代码。 若要执行此操作，需要在独立的位置拥有两个命名空间，例如，美国南部和美国中北部。 下面的示例使用 Visual Studio 2017。

1.  在 Visual Studio 中创建一个新的控制台应用 (.Net Framework) 项目，并为其提供一个名称；例如，SBGeoDR。

2.  安装以下 NuGet 包：
    1.  Microsoft.IdentityModel.Clients.ActiveDirectory
    2.  Microsoft.Azure.Management.ServiceBus

3. 请确保你使用的 Newtonsoft.Json NuGet 包的版本为 10.0.3。

3.  向代码添加以下 `using` 语句：

    ```csharp
    using System.Threading;
    using Microsoft.Azure.Management.ServiceBus;
    using Microsoft.Azure.Management.ServiceBus.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

4. 修改 `main()` 方法以添加两个高级命名空间：

    ```csharp
    // 1. Create primary namespace (optional).

    var namespaceParams = new SBNamespace()
    {
        Location = "South Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    var namespace1 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, namespaceParams);

    // 2. Create secondary namespace (optional if you already have an empty namespace available).

    var namespaceParams2 = new SBNamespace()
    {
        Location = "North Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    // If you re-run this program while namespaces are still paired this operation will fail with a bad request.
    // This is because we block all updates on secondary namespaces once it is paired.

    var namespace2 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRSecondaryNS, namespaceParams2);
    ```

5. 在两个命名空间之间启用配对，并获取稍后用于连接到实体的别名：

    ```csharp
    // 3. Pair the namespaces to enable DR.

    ArmDisasterRecovery drStatus = client.DisasterRecoveryConfigs.CreateOrUpdate(
        resourceGroupName,
        geoDRPrimaryNS,
        alias,
        new ArmDisasterRecovery { PartnerNamespace = geoDRSecondaryNS });

    // A similar loop can be used to check if other operations (Failover, BreakPairing, Delete) 
    // mentioned below have been successful.
    while (drStatus.ProvisioningState != ProvisioningStateDR.Succeeded)
    {
        Console.WriteLine("Waiting for DR to be set up. Current state: " +
        drStatus.ProvisioningState);
        drStatus = client.DisasterRecoveryConfigs.Get(
        resourceGroupName,
        geoDRPrimaryNS,
        alias);

        Thread.CurrentThread.Join(TimeSpan.FromSeconds(30));
    }
    ```

已成功设置两个配对的命名空间。 现在可以创建实体来观察元数据同步。 如果随后要立即执行故障转移，应为元数据同步留出一些时间。 可以添加一个短的休眠时间，例如：

```csharp
client.Topics.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", new SBTopic());
client.Subscriptions.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", "myTopic-Sub1", new SBSubscription());

// sleeping to allow metadata to sync across primary and secondary
Thread.Sleep(1000 * 60);
```

此时，可以通过门户或 Azure 资源管理器添加实体，并查看同步方式。 除非计划手动执行故障转移，否则应创建一个应用来监视主命名空间和启动故障转移（如果它变得不可用）。 

## <a name="initiate-a-failover"></a>启动故障转移

以下代码演示如何启动故障转移：

```csharp
// Note that this failover operation is always run against the secondary namespace 
// (because primary might be down at time of failover).

client.DisasterRecoveryConfigs.FailOver(resourceGroupName, geoDRSecondaryNS, alias);
```

触发故障转移后，添加一个新的被动命名空间并重新建立配对。 上一节中显示了用于创建新配对的代码。 此外，一旦故障转移完成，必须从旧的主命名空间删除消息。 有关如何从队列接收消息的示例，请参阅[队列入门](service-bus-dotnet-get-started-with-queues.md)。

## <a name="how-to-disable-geo-disaster-recovery"></a>如何禁用异地灾难恢复

下面的代码演示如何禁用命名空间配对：

```csharp
client.DisasterRecoveryConfigs.BreakPairing(resourceGroupName, geoDRPrimaryNS, alias);
```

下面的代码将删除你创建的别名：

```csharp
// Delete the DR config (alias).
// Note that this operation must run against the namespace to which the alias is currently pointing.
// If you break the pairing and want to delete the namespaces afterwards, you must delete the alias first.

client.DisasterRecoveryConfigs.Delete(resourceGroupName, geoDRPrimaryNS, alias);
```

## <a name="steps-after-a-failover-failback"></a>故障转移后的步骤（故障回复）

故障转移后，执行以下两个步骤：

1.  创建新的被动辅助命名空间。 上一节中显示的代码。
2.  从队列中删除其余消息。

## <a name="alias-connection-string-and-test-code"></a>别名连接字符串和测试代码

如果要测试故障转移过程，可以编写一个示例应用程序，使用别名将消息推送到主命名空间。 若要执行此操作，请确保从主动命名空间获取别名连接字符串。 在当前的预览版本中，没有其他接口可以直接获取连接字符串。 以下示例演示了故障转移前后的代码连接：

```csharp
var accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRPrimaryNS, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;

if(aliasPrimaryConnectionString == null)
{
    accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRSecondaryNS, "RootManageSharedAccessKey");
    aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
    aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
}
```

## <a name="next-steps"></a>后续步骤

- 请参阅此处的异地灾难恢复 [REST API 参考](/rest/api/servicebus/disasterrecoveryconfigs)。
- 在 GitHub 上运行异地灾难恢复[示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2)。
- 请参阅异地灾难恢复[将消息发送到别名的示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1)。

若要了解有关服务总线消息传送的详细信息，请参阅以下文章：

* [服务总线基础知识](service-bus-fundamentals-hybrid-solutions.md)
* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Rest API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png
