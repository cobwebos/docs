---
title: 使用 Azure Active Directory 授予访问权限
description: 本文提供有关使用 Azure Active Directory 授权访问事件中心资源的信息。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 101e40420493156c7b1a0c3c5b767eda023e62c6
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87831832"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>使用 Azure Active Directory 授权访问事件中心资源
Azure 事件中心支持使用 Azure Active Directory (Azure AD) 授权对事件中心资源的请求。 可以通过 Azure AD 使用基于角色的访问控制 (RBAC) 授予对服务主体的访问权限，该服务主体可能是用户或应用程序服务主体。 若要详细了解角色和角色分配，请参阅[了解不同的角色](../role-based-access-control/overview.md)。

## <a name="overview"></a>概述
当安全主体（用户或应用程序）尝试访问事件中心资源时，必须授权该请求。 使用 Azure AD 是，访问资源的过程包括两个步骤。 

 1. 首先，验证安全主体的身份并返回 OAuth 2.0 令牌。 用于请求令牌的资源名称为 `https://eventhubs.azure.net/`。 对于 Kafka 客户端，请求令牌的资源为 `https://<namespace>.servicebus.windows.net`。
 1. 接下来，将该令牌作为请求的一部分传递给事件中心服务，用于授权访问指定的资源。

身份验证步骤要求应用程序请求包含在运行时使用的 OAuth 2.0 访问令牌。 如果应用程序在 Azure 实体（如 Azure VM、虚拟机规模集或 Azure 函数应用）中运行，它可以使用托管标识来访问资源。 若要了解如何对托管标识向事件中心服务发出的请求进行身份验证，请参阅[对使用 Azure Active Directory 和 Azure 资源的托管标识访问 Azure 事件中心资源进行身份验证](authenticate-managed-identity.md)。 

授权步骤要求向安全主体分配一个或多个 Azure 角色。 Azure 事件中心为 Azure 角色提供了包含事件中心资源的权限集的 Azure 角色。 分配给安全主体的角色确定了该主体拥有的权限。 有关 Azure 角色的详细信息，请参阅 azure[事件中心的 azure 内置角色](#azure-built-in-roles-for-azure-event-hubs)。 

向事件中心发出请求的本机应用程序和 Web 应用程序也可以使用 Azure AD 进行授权。 若要了解如何请求访问令牌并使用它来授权对事件中心资源的请求，请参阅[对使用 Azure AD 从应用程序访问 Azure 事件中心进行身份验证](authenticate-application.md)。 

## <a name="assign-azure-roles-for-access-rights"></a>为访问权限分配 Azure 角色
Azure Active Directory (Azure AD) 通过[AZURE RBAC (的 azure 基于角色的访问控制](../role-based-access-control/overview.md)来授予对受保护资源的访问权限。 Azure 事件中心定义一组 Azure 内置角色，这些角色包含用于访问事件中心数据的公用权限集，还可以定义用于访问数据的自定义角色。

将 Azure 角色分配到 Azure AD 安全主体时，Azure 会向该安全主体授予对这些资源的访问权限。 访问权限的范围可限定为订阅、资源组、事件中心命名空间级别或其下的任何资源。 Azure AD 安全主体可以是用户或应用程序服务主体，也可以是 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="azure-built-in-roles-for-azure-event-hubs"></a>Azure 事件中心的 azure 内置角色
Azure 提供以下 Azure 内置角色，用于授权使用 Azure AD 和 OAuth 访问事件中心数据：

- [Azure 事件中心数据所有者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner)：使用此角色可以授予对事件中心资源的完全访问权限。
- [Azure 事件中心数据发送者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender)：使用此角色可以授予对事件中心资源的发送访问权限。
- [Azure 事件中心数据接收者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver)：使用此角色可以授予对事件中心资源的使用/接收访问权限。

## <a name="resource-scope"></a>资源范围 
向安全主体分配 Azure 角色之前，请确定安全主体应具有的访问权限的范围。 最佳做法指出，最好是授予尽可能小的范围。

以下列表描述了可将事件中心资源访问权限限定到哪些级别，从最小的范围开始：

- **使用者组**：在此范围，角色分配仅应用到此实体。 目前，Azure 门户不支持将 Azure 角色分配到此级别的安全主体。 
- **事件中心**：角色分配将应用到事件中心实体及其下面的使用者组。
- **命名空间**：角色分配横跨命名空间下事件中心的整个拓扑，并延伸至与之关联的使用者组。
- **资源组**：角色分配将应用到资源组下的所有事件中心资源。
- **订阅**：角色分配将应用到订阅的所有资源组中的所有事件中心资源。

> [!NOTE]
> - 请记住，Azure 角色分配可能需要长达五分钟才能传播。 
> - 此内容适用于事件中心及用于 Apache Kafka 的事件中心。 有关用于 Kafka 的事件中心支持的详细信息，请参阅[用于 Kafka 的事件中心 - 安全性和身份验证](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication)。


有关如何定义内置角色的详细信息，请参阅[了解角色定义](../role-based-access-control/role-definitions.md#management-and-data-operations)。 有关创建 Azure 自定义角色的详细信息，请参阅[azure 自定义角色](../role-based-access-control/custom-roles.md)。



## <a name="samples"></a>示例
- [Microsoft.Azure.EventHubs 示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)。 
    
    这些示例使用旧的 **Microsoft.Azure.EventHubs** 库，但你可以轻松地将其更新为使用最新的 **Azure.Messaging.EventHubs** 库。 若要将示例从使用旧库迁移到使用新库，请参阅[从 Microsoft.Azure.EventHubs 迁移到 Azure.Messaging.EventHubs 的指南](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)。
- [Azure.Messaging.EventHubs 示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    此示例已更新为使用最新 **Azure.Messaging.EventHubs** 库。
- [用于 Kafka 的事件中心 - OAuth 示例](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)。 


## <a name="next-steps"></a>后续步骤
- 了解如何向安全主体分配 Azure 内置角色，请参阅[使用 Azure Active Directory 对事件中心资源的访问权限进行身份验证](authenticate-application.md)。
- 了解[如何使用 RBAC 创建自定义角色](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole)。
- 了解[如何将 Azure Active Directory 与 EH 配合使用](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

请参阅以下相关文章：

- [使用 Azure Active Directory 对应用程序的 Azure 事件中心请求进行身份验证](authenticate-application.md)
- [使用 Azure Active Directory 对托管标识的事件中心资源访问进行身份验证](authenticate-managed-identity.md)
- [使用共享访问签名对 Azure 事件中心请求进行身份验证](authenticate-shared-access-signature.md)
- [使用共享访问签名授权访问事件中心资源](authorize-access-shared-access-signature.md)
