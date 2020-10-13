---
title: 事件中心中的 Azure 架构注册表（预览版）
description: 本文概述了 Azure 事件中心（预览版）支持的架构注册表。
ms.topic: overview
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: a0cc54f6ada1a5900e817c8e22fc192cd73f6550
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91653564"
---
# <a name="azure-schema-registry-in-event-hubs-preview"></a>事件中心中的 Azure 架构注册表（预览版）
在许多事件流式处理和消息传送场景中，事件或消息有效负载都包含结构化数据，这些数据使用架构驱动格式（例如 Apache Avro）进行序列化或反序列化。 发送方和接收方可能都需要使用与 JSON 架构相同的架构文档来验证数据的完整性。 对于架构驱动格式，消息使用者必须能够使用该架构，才能反序列化数据。 

Azure 架构注册表是事件中心的一项功能，它为事件驱动的应用程序和以消息为中心的应用程序的架构文档提供了一个中央存储库。 它使生成者和使用者应用程序可以灵活地交换数据而无需在它们之间管理和共享架构，还可以灵活地按不同速率进行发展。 架构注册表还为可重用架构提供了一个简单的管理框架，并通过分组构造（架构组）定义了架构之间的关系。

> [!NOTE]
> - 架构注册表功能目前处于预览状态，不建议用于生产工作负载 。
> - 仅标准层和专用层提供此功能，基本层未提供  。

借助架构驱动的序列化框架（例如 Apache Avro ），将序列化元数据外部化为共享架构还有助于显著降低每个数据集包含的类型信息和字段名称的每条消息开销，因为它具有标记格式（如 JSON）。 将架构与事件一起存储，并将其存储在事件基础结构中，可确保序列化/反序列化所需的元数据始终是可访问的，并且架构始终置于正确的位置。 

## <a name="event-hubs-namespace"></a>事件中心命名空间
事件中心命名空间现在可与事件中心（或 Kafka 主题）一起托管架构组。 它可托管架构注册表并可具有多个架构组。 尽管架构注册表是在 Azure 事件中心进行托管，但它仍可普遍用于所有 Azure 消息传送服务和任何其他消息或事件代理。 这些架构组每一个都是一个单独的安全存储库，适用于一组架构。 这些组可以与特定应用程序或组织单位保持一致。 

## <a name="schema-groups"></a>架构组
架构组是基于业务条件的类似架构的逻辑组。 一个架构组可以保存架构的多个版本。 架构组上的兼容性强制设置有助于确保更新的架构版本后向兼容。

分组机制规定的安全边界有助于确保在多个合作伙伴之间共享命名空间的情况下，不会通过元数据无意中泄漏商业机密。 它还允许应用程序所有者管理独立于共享同一命名空间的其他应用程序的架构。


## <a name="schemas"></a>架构
架构定义了生成者与使用者之间的协定。 事件中心架构注册表中定义的架构可帮助管理事件数据之外的协定，从而消除有效负载开销。 架构具有名称、类型（例如记录、数组等）、兼容模式（无、前向兼容、后向兼容、完全兼容）和序列化类型（目前仅限 Avro）。 可以创建架构的多个版本，并检索和使用架构的特定版本。 

## <a name="client-sdks"></a>客户端 SDK
可以使用以下库中的一种，其中包括 Avro 序列化程序，可以使用它来序列化和反序列化包含架构注册表架构标识符和 Avro 编码数据的有效负载。

- [.NET - Microsoft.Azure.Data.SchemaRegistry.ApacheAvro](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro)
- [Java - azure-data-schemaregistry-avro](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/)
- [Python - azure-schemaregistry-avroserializer](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer)
- [JavaScript - @azure/schema-registry-avro](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro)
- [Apache Kafka](https://github.com/Azure/azure-schema-registry-for-kafka/tree/master/avro/samples) - 运行 Azure 架构注册表支持的与 Kafka 集成的 Apache Avro 序列化程序和反序列化程序。 可以在任何 Apache Kafka 场景中使用 Java 客户端的 Apache Kafka 客户端序列化程序，也可以在任何基于 Apache Kafka® 的部署或云服务中使用。 

下图显示了事件中心的架构注册表的信息流： 

:::image type="content" source="./media/schema-registry-overview/flow-diagram.png" alt-text="流程图":::

## <a name="standard-vs-dedicated-limits"></a>标准限制与专用限制
对于事件中心标准层和专用层的相同和不同限制（例如命名空间中的架构组的数目），请参阅[架构注册表限制](../azure-resource-manager/management/azure-subscription-service-limits.md#schema-registry-limitations)

## <a name="role-based-access-control"></a>基于角色的访问控制
以编程方式访问架构注册表时，需要在 Azure Active Directory (Azure AD) 中注册应用程序，并将应用程序的安全主体添加到以下基于角色的访问控制 (RBAC) 角色之一：

| 角色 | 说明 | 
| ---- | ----------- | 
| 所有者 | 读取、写入和删除架构注册表组和架构。 |
| 参与者 | 读取、写入和删除架构注册表组和架构。 |
| [架构注册表读取器（预览版）](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | 读取和列出架构注册表组和架构。 |
| [架构注册表参与者（预览）](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | 读取、写入和删除架构注册表组和架构。 |

有关使用 Azure 门户注册应用程序的说明，请参阅[使用 Azure AD](../active-directory/develop/quickstart-register-app.md) 注册应用。 请记下要在代码中使用的客户端 ID（应用程序 ID）、租户 ID 和机密。 

## <a name="next-steps"></a>后续步骤

- 若要了解如何使用 Azure 门户创建架构注册表，请参阅[使用 Azure 门户创建事件中心架构注册表](create-schema-registry.md)。
- 请参阅下面的架构注册表 Avro 客户端库示例。
    - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro/tests/Samples)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/src/samples)
    - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro/samples )
    - [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer/samples )
    - [Azure 架构注册表的 Kafka Avro 集成](https://github.com/Azure/azure-schema-registry-for-kafka/tree/master/avro/samples)
