---
title: 授予对 Azure 事件中心的访问权限
description: 本文介绍授权访问 Azure 事件中心资源的不同选项。
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: b5a037eaf310aa28c76d831dc9fe56eefaddbe56
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123461"
---
# <a name="authorize-access-to-azure-event-hubs"></a>授予对 Azure 事件中心的访问权限
每次你发布或使用事件中心的事件/数据时，客户端会尝试访问事件中心资源。 每个对安全资源的请求都必须经过授权，以便服务确保客户端具有发布/使用数据所需的权限。 

Azure 事件中心提供以下用于授权访问安全资源的选项：

## <a name="azure-active-directory"></a>Azure Active Directory
事件中心资源的 Azure Active Directory (Azure AD) 集成提供了基于角色的访问控制 (RBAC)，用于对客户端对资源的访问进行精细控制。 可以使用基于角色的访问控制 (RBAC) 授予对安全主体的权限，该服务主体可能是用户、组或应用程序服务主体。 安全主体经 Azure AD 进行身份验证后会返回 OAuth 2.0 令牌。 令牌可用于授权访问事件中心资源的请求。

有关使用 Azure AD 进行身份验证的详细信息，请参阅以下文章：

- [使用 Azure Active Directory 对发往 Azure 事件中心的请求进行身份验证](authenticate-application.md)
- [使用 Azure Active Directory 授权访问事件中心资源](authorize-access-azure-active-directory.md)。

## <a name="shared-access-signatures"></a>共享访问签名 
事件中心资源的共享访问签名 (SAS) 针对事件中心资源提供有限的委托访问权限。 通过对签名的有效时间间隔或对它授予的权限添加约束，可灵活地管理资源。 有关详细信息，请参阅[使用共享访问签名 (SAS) 进行身份验证](authenticate-shared-access-signature.md)。 

使用 Azure AD 返回的 OAuth 2.0 令牌授权用户或应用程序可提供比共享访问签名 (SAS) 更高的安全性和易用性。 使用 Azure AD 时，不需将访问令牌与代码存储在一起，因此没有潜在的安全漏洞风险。 尽管你可以继续使用共享访问签名 (SAS) 授予对事件中心资源的精细访问权限，但 Azure AD 提供了类似的功能，并且不需要管理 SAS 令牌，也不需要担心吊销已泄密的 SAS。 

默认情况下，所有事件中心资源都受到保护，并且只能由帐户所有者使用。 虽然你可以使用上述任何授权策略为客户端授予访问事件中心资源的权限， 但仅通过 Azure 资源管理器部署模型创建的事件中心资源支持 Azure AD 授权。 Microsoft 建议尽可能使用 Azure AD，以便最大程度地确保安全性和易用性。

若要详细了解如何使用 SAS 进行授权，请参阅[使用共享访问签名授权访问事件中心资源](authorize-access-shared-access-signature.md)。

## <a name="next-steps"></a>后续步骤
- 请参阅在 GitHub 存储库中发布的 [RBAC 示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)。 
- 请参阅以下文章：
    - [使用 Azure Active Directory 对应用程序的 Azure 事件中心请求进行身份验证](authenticate-application.md)
    - [使用 Azure Active Directory 对托管标识的事件中心资源访问进行身份验证](authenticate-managed-identity.md)
    - [使用共享访问签名对 Azure 事件中心请求进行身份验证](authenticate-shared-access-signature.md)
    - [使用 Azure Active Directory 授权访问事件中心资源](authorize-access-azure-active-directory.md)
    - [使用共享访问签名授权访问事件中心资源](authorize-access-shared-access-signature.md)

