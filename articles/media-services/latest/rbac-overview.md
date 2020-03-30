---
title: 媒体服务帐户的基于角色的访问控制 - Azure | Microsoft Docs
description: 本文介绍 Azure 媒体服务帐户的基于角色的访问控制 (RBAC)。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/23/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 93b2cd3a2565b14ea07d6db6b14dd146e4223528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "66236918"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>媒体服务帐户的基于角色的访问控制 (RBAC)

目前，Azure 媒体服务不定义任何特定于服务的自定义角色。 若要获得媒体服务帐户的完全访问权限，客户可以使用内置的“所有者”或“参与者”角色。******** 这两个角色的主要区别是：所有者可以控制谁有权访问某个资源，而参与者则不可以。******** 也可使用内置的“读者”角色，但用户或应用程序将只能对媒体服务 API 进行读取访问。**** 

## <a name="design-principles"></a>设计原理

V3 API 的主要设计原则之一是使 API 更安全。 v3 API 不会在 **Get** 或 **List** 操作中返回机密或凭据。 在响应中，密钥始终为 null、空值或进行了净化。 用户需要调用单独的操作方法来获取机密或凭据。 “读者”角色**** 不能调用多项操作，例如 Asset.ListContainerSas、StreamingLocator.ListContentKeys、ContentKeyPolicies.GetPolicyPropertiesWithSecrets。 可以通过单独的操作根据需要采用自定义角色来设置更细致的 RBAC 安全权限。

若要列出媒体服务支持的操作，请执行以下代码：

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

[内置角色定义](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)一文会确切地告知你该角色授予的权限。 

有关详细信息，请参阅以下文章：

- [经典订阅管理员角色、Azure RBAC 角色和 Azure AD 管理员角色](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Azure 资源的 RBAC 是什么？](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [使用 RBAC 管理访问权限](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [媒体服务资源提供程序操作](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>后续步骤

- [使用媒体服务 v3 API 进行开发](media-services-apis-overview.md)
- [使用媒体服务 .NET 获取内容密钥策略](get-content-key-policy-dotnet-howto.md)
