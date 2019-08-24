---
title: 授予对 Azure 事件中心的访问权限
description: 本文提供了有关授权访问 Azure 事件中心资源的不同选项的信息。
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 9847f2f51da57526f84a7fcb3929e95ae267546c
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992544"
---
# <a name="authorize-access-to-azure-event-hubs"></a>授予对 Azure 事件中心的访问权限
每次从事件中心发布或使用事件/数据时, 客户端都将尝试访问事件中心资源。 对安全资源的每个请求都必须经过授权, 以便服务能够确保客户端具有发布/使用数据所需的权限。 

Azure 事件中心提供以下选项用于授权对安全资源的访问:

## <a name="azure-active-directory"></a>Azure Active Directory
事件中心资源的 Azure Active Directory (Azure AD) 集成提供了基于角色的访问控制 (RBAC), 以便对客户端对资源的访问进行精细控制。 你可以使用基于角色的访问控制 (RBAC) 向安全主体授予权限, 这可能是用户、组或应用程序服务主体。 安全主体经 Azure AD 进行身份验证后会返回 OAuth 2.0 令牌。 令牌可用于授权请求访问事件中心资源。

有关使用 Azure AD 进行身份验证的详细信息, 请参阅[使用 Azure Active Directory 对 Azure 事件中心的请求进行身份验证](authenticate-application.md)。 有关使用 Azure AD 进行授权的详细信息, 请参阅[使用 Azure Active Directory 授予对事件中心资源的访问权限](authorize-access-azure-active-directory.md)。

## <a name="share-access-signatures"></a>共享访问签名 
事件中心资源的共享访问签名 (SAS) 提供对事件中心资源的有限委托访问。 在签名有效的时间间隔内添加约束或对其授予的权限的约束为管理资源提供了灵活性。 有关详细信息, 请参阅[使用共享访问签名 (SAS) 进行身份验证](authenticate-shared-access-signature.md)。 

使用 Azure AD 返回的 OAuth 2.0 令牌授权用户或应用程序可提供更高的安全性, 并可通过共享访问签名 (SAS) 轻松使用。 在 Azure AD 中, 无需将访问令牌与代码一起存储, 并可能会导致潜在的安全漏洞。 尽管你可以继续使用共享访问签名 (SAS) 授予对事件中心资源的精细访问权限, 但 Azure AD 提供了类似的功能, 而无需管理 SAS 令牌, 也无需担心吊销已泄露的 SAS。 

默认情况下, 所有事件中心资源都是安全的, 仅供帐户所有者使用。 尽管可以使用以上概述的任何授权策略来授予客户端对事件中心资源的访问权限。 只有通过 Azure 资源管理器部署模型创建的事件中心资源支持 Azure AD 授权。 Microsoft 建议尽可能使用 Azure AD, 以实现最高安全性和易用性。

有关使用 SAS 的授权的详细信息, 请参阅[使用共享访问签名授权访问事件中心资源](authorize-access-shared-access-signature.md)。

## <a name="next-steps"></a>后续步骤
- 查看 GitHub 存储库中发布的[RBAC 示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)。 
- 请参阅以下文章：
    - [使用 Azure Active Directory 对应用程序中的 Azure 事件中心的请求进行身份验证](authenticate-application.md)
    - [使用 Azure Active Directory 验证托管标识, 以访问事件中心资源](authenticate-managed-identity.md)
    - [使用共享访问签名对 Azure 事件中心的请求进行身份验证](authenticate-shared-access-signature.md)
    - [使用 Azure Active Directory 授权访问事件中心资源](authorize-access-azure-active-directory.md)
    - [使用共享访问签名授予对事件中心资源的访问权限](authorize-access-shared-access-signature.md)

