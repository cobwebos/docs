---
title: 媒体服务帐户-Azure 基于角色的访问控制 |Microsoft Docs
description: 本文介绍 Azure 媒体服务帐户的基于角色的访问控制 (RBAC)。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: f72e98d8874a5a5dc94deb882affdf66388b13c9
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548519"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>媒体服务帐户的基于角色的访问控制 (RBAC)

目前，Azure 媒体服务不未定义任何自定义角色特定于到该服务。 客户可以使用的内置角色**所有者**或**参与者**获取到媒体服务帐户的完全访问权限。 这些角色之间的主要区别是：**所有者**可以控制谁有权访问资源并**参与者**不能。 内置的读取器帐户仅具有读取访问权限的媒体服务帐户。 

## <a name="design-principles"></a>设计原理

V3 API 的主要设计原则之一是使 API 更安全。 v3 Api 不返回机密或凭据上**获取**或**列表**操作。 在响应中，密钥始终为 null、空值或进行了净化。 用户需要调用一个单独的操作方法来获取机密信息或凭据。 **读取器**角色不能调用操作，以便它不能调用 Asset.ListContainerSas，StreamingLocator.ListContentKeys，ContentKeyPolicies.GetPolicyPropertiesWithSecrets 等操作。 具有单独的操作，可根据需要自定义角色中设置更精细的 RBAC 安全权限。

若要列出操作媒体服务支持，请执行：

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

[内置角色定义](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)文章告诉您完全什么角色的授权。 

有关详细信息，请参阅以下文章：

- [经典订阅管理员角色、Azure RBAC 角色和 Azure AD 管理员角色](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [什么是 Azure 资源的 RBAC？](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [使用 RBAC 管理访问权限](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)

## <a name="next-steps"></a>后续步骤

- [使用媒体服务 v3 Api 进行开发](media-services-apis-overview.md)
- [获取内容密钥的策略使用媒体服务.NET](get-content-key-policy-dotnet-howto.md)
