---
title: "Azure 事件中心异地灾难恢复 | Microsoft Docs"
description: "如何使用地理区域进行故障转移并在 Azure 事件中心执行灾难恢复"
services: event-hubs
documentationcenter: 
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: sethm
ms.openlocfilehash: 94c2782b3166fbc65ae755291a82a2a14556b96f
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2017
---
# <a name="azure-event-hubs-geo-disaster-recovery-preview"></a>Azure 事件中心异地灾难恢复（预览）

当区域数据中心遭遇停机的情况下，在不同区域或数据中心进行数据处理以实现连续运行就显得至关重要。 在这种情况下，异地灾难恢复和异地复制对于任何企业而言都是至关重要的功能。 Azure 事件中心支持命名空间级别的异地灾难恢复和异地复制。 

Azure 事件中心的异地灾难恢复功能是一种灾难恢复解决方案。 本文中所述的概念和工作流适用于灾难方案，而不适用于暂时或临时中断。

有关 Microsoft Azure 中的灾难恢复的详细讨论，请参阅[此文](/azure/architecture/resiliency/disaster-recovery-azure-applications)。 

## <a name="terminology"></a>术语

**配对**：主命名空间称为主动命名空间，可接收消息。 故障转移命名空间是被动命名空间，不会接收消息。 两者之间的元数据保持同步，因此可以无缝接受消息，而不必更改任何应用程序代码。 在主动区域与被动区域之间建立灾难恢复配置称为将区域配对。

**别名**：所设置的灾难恢复配置的名称。 别名提供单个稳定的完全限定域名 (FQDN) 连接字符串。 应用程序使用此别名连接字符串连接到命名空间。

**元数据**：指与命名空间关联的事件中心名称、使用者组、分区、吞吐量单位、实体和属性。

## <a name="enable-geo-disaster-recovery"></a>启用异地灾难恢复

通过 3 个步骤启用事件中心异地灾难恢复： 

1. 创建一个地理配对，这样就会创建别名连接字符串并提供实时元数据复制。 
2. 将现有的客户端连接字符串更新为在步骤 1 中创建的别名。
3. 启动故障转移：地理配对将会中断，别名指向用作其新主命名空间的辅助命名空间。

下图演示了此工作流：

![地理配对流][1] 

### <a name="step-1-create-a-geo-pairing"></a>步骤 1：创建地理配对

若要在两个区域之间创建配对，需要主命名空间和辅助命名空间。 然后创建别名来构成地理配对。 将命名空间与别名配对后，会定期在两个命名空间中复制元数据。 

以下代码演示了如何执行此操作：

```csharp
ArmDisasterRecovery adr = await client.DisasterRecoveryConfigs.CreateOrUpdateAsync(
                                    config.PrimaryResourceGroupName,
                                    config.PrimaryNamespace,
                                    config.Alias,
                                    new ArmDisasterRecovery(){ PartnerNamespace = config.SecondaryNamespace});
```

### <a name="step-2-update-existing-client-connection-strings"></a>步骤 2：更新现有的客户端连接字符串

完成地理配对后，必须将指向主命名空间的连接字符串更新为指向别名连接字符串。 如以下示例中所示获取连接字符串：

```csharp
var accessKeys = await client.Namespaces.ListKeysAsync(config.PrimaryResourceGroupName,
                                                       config.PrimaryNamespace, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
```

### <a name="step-3-initiate-a-failover"></a>步骤 3：启动故障转移

如果发生灾难或者启动到辅助命名空间的故障转移，元数据和数据会开始流入辅助命名空间。 由于应用程序使用别名连接字符串，会自动开始读取和写入辅助命名空间中的事件中心，因此无需执行其他操作。 

以下代码演示如何触发故障转移：

```csharp
await client.DisasterRecoveryConfigs.FailOverAsync(config.SecondaryResourceGroupName,
                                                   config.SecondaryNamespace, config.Alias);
```

故障转移完成后，如果需要提取主命名空间中的数据，必须使用主命名空间中事件中心的显式连接字符串。

### <a name="other-operations-optional"></a>其他操作（可选）

还可按以下代码所示，中断地理配对或删除别名。 请注意，若要删除别名连接字符串，必须先中断地理配对：

```csharp
// Break pairing
await client.DisasterRecoveryConfigs.BreakPairingAsync(config.PrimaryResourceGroupName,
                                                       config.PrimaryNamespace, config.Alias);

// Delete alias connection string
// Before the alias is deleted, pairing must be broken
await client.DisasterRecoveryConfigs.DeleteAsync(config.PrimaryResourceGroupName,
                                                 config.PrimaryNamespace, config.Alias);
```

## <a name="considerations-for-public-preview"></a>公共预览版注意事项

请注意此版本的以下事项：

1. 异地灾难恢复功能仅在美国中北部和美国中南部区域可用。 
2. 只有新建的命名空间才支持此功能。
3. 在预览版中，只会启用元数据复制， 而不会复制实际数据。
4. 在预览版中启用此功能不会产生费用。 但是，主命名空间和辅助命名空间会产生保留吞吐量单位的费用。

## <a name="next-steps"></a>后续步骤

* [GitHub 上的示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient)演练了一个针对灾难恢复方案创建地理配对并启动故障转移的简单工作流。
* [REST API 参考](/rest/api/eventhub/disasterrecoveryconfigs)介绍了用于执行异地灾难恢复配置的 API。

有关事件中心的详细信息，请访问以下链接：

* 使用 [事件中心教程](event-hubs-dotnet-standard-getstarted-send.md)
* [事件中心常见问题](event-hubs-faq.md)
* [使用事件中心的示例应用程序](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/eh-geodr1.png

