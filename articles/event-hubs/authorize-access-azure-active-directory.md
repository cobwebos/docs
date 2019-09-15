---
title: 使用 Azure Active Directory 授予访问权限
description: 本文提供了有关使用 Azure Active Directory 授权访问事件中心资源的信息。
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 0d8d1b37e7f5ebb6eef1c76e4324041c48ab8986
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995741"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>使用 Azure Active Directory 授权访问事件中心资源
Azure 事件中心支持使用 Azure Active Directory （Azure AD）向事件中心资源的请求授权。 使用 Azure AD，你可以使用基于角色的访问控制（RBAC）向安全主体（可以是用户或应用程序服务主体）授予权限。 若要了解有关角色和角色分配的详细信息，请参阅[了解不同的角色](../role-based-access-control/overview.md)。

## <a name="overview"></a>概述
当安全主体（用户或应用程序）尝试访问事件中心资源时，必须对该请求进行授权。 使用 Azure AD 是，访问资源的过程包括两个步骤。 

 1. 首先，对安全主体的身份进行身份验证，并返回 OAuth 2.0 令牌。 请求令牌的资源名称为`https://eventhubs.azure.net/`。
 1. 接下来，令牌作为请求的一部分传递到事件中心服务，以授权访问指定的资源。

身份验证步骤要求应用程序请求在运行时包含 OAuth 2.0 访问令牌。 如果应用程序在 Azure 实体（如 Azure VM、虚拟机规模集或 Azure Function app）内运行，则它可以使用托管标识来访问资源。 若要了解如何对事件中心服务的托管标识发出的请求进行身份验证，请参阅[使用 Azure 资源的 Azure Active Directory 和托管标识对 Azure 事件中心资源的访问权限进行身份验证](authenticate-managed-identity.md)。 

授权步骤需要将一个或多个 RBAC 角色分配给安全主体。 Azure 事件中心提供了包含事件中心资源权限集的 RBAC 角色。 分配给安全主体的角色确定了该主体拥有的权限。 有关 RBAC 角色的详细信息，请参阅[Azure 事件中心的内置 RBAC 角色](#built-in-rbac-roles-for-azure-event-hubs)。 

向事件中心发出请求的本机应用程序和 web 应用程序还可以授权 Azure AD。 若要了解如何请求访问令牌并使用它来授权事件中心资源的请求，请参阅[使用应用程序中的 Azure AD 验证对 Azure 事件中心的访问权限](authenticate-application.md)。 

## <a name="assign-rbac-roles-for-access-rights"></a>为访问权限分配 RBAC 角色
Azure Active Directory (Azure AD) 通过[基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 授权访问受保护的资源。 Azure 事件中心定义一组内置 RBAC 角色，其中包含用于访问事件中心数据的常用权限集，还可以定义用于访问数据的自定义角色。

将 RBAC 角色分配到 Azure AD 安全主体后，Azure 会向该安全主体授予对这些资源的访问权限。 可以将访问权限限定为订阅级别、资源组、事件中心命名空间或其下的任何资源。 Azure AD 安全主体可以是用户或应用程序服务主体，也可以是[Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="built-in-rbac-roles-for-azure-event-hubs"></a>Azure 事件中心的内置 RBAC 角色
Azure 提供下列内置 RBAC 角色，可使用 Azure AD 和 OAuth 授权访问事件中心数据：

- [Azure 事件中心数据所有者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner)：使用此角色可以完全访问事件中心资源。
- [Azure 事件中心数据发送](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver)程序：使用此角色授予对事件中心资源的发送访问权限。
- [Azure 事件中心数据接收方](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender)：使用此角色可向事件中心资源授予使用/接收访问权限。

## <a name="resource-scope"></a>资源范围 
在将 RBAC 角色分配到某个安全主体之前，请确定该安全主体应该获取的访问范围。 最佳做法指出，最好是授予尽可能小的范围。

以下列表描述了从最窄的范围开始，可以将对事件中心资源的访问范围限定为的级别：

- **使用者组**：在此范围内，角色分配仅适用于此实体。 目前，Azure 门户不支持在此级别向安全主体分配 RBAC 角色。 
- **事件中心**：角色分配适用于事件中心实体和它下面的使用者组。
- **命名空间**：角色分配跨越命名空间和与其关联的使用者组的整个事件中心拓扑。
- **资源组**：角色分配适用于资源组下的所有事件中心资源。
- **订阅**：角色分配适用于订阅中所有资源组中的所有事件中心资源。

> [!NOTE]
> 请记住，RBAC 角色分配可能需要最多五分钟的时间进行传播。 

有关如何定义内置角色的详细信息，请参阅[了解角色定义](../role-based-access-control/role-definitions.md#management-and-data-operations)。 若要了解如何创建自定义 RBAC 角色，请参阅[针对 Azure 基于角色的访问控制创建自定义角色](../role-based-access-control/custom-roles.md)。

## <a name="next-steps"></a>后续步骤
- 了解如何向安全主体分配内置 RBAC 角色的详细说明，请参阅[使用 Azure Active Directory 对事件中心资源的访问权限进行身份验证](authenticate-application.md)。
- 了解[如何通过 RBAC 创建自定义角色](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole)。
- 了解[如何结合使用 Azure Active Directory 与 EH](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

请参阅以下相关文章：

- [使用 Azure Active Directory 对应用程序中的 Azure 事件中心的请求进行身份验证](authenticate-application.md)
- [使用 Azure Active Directory 验证托管标识，以访问事件中心资源](authenticate-managed-identity.md)
- [使用共享访问签名对 Azure 事件中心的请求进行身份验证](authenticate-shared-access-signature.md)
- [使用共享访问签名授予对事件中心资源的访问权限](authorize-access-shared-access-signature.md)