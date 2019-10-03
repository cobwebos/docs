---
title: 使用 .NET 定义存储访问策略-Azure 存储
description: 了解如何使用 .NET 客户端库定义存储访问策略。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990736"
---
# <a name="define-a-stored-access-policy-with-net"></a>使用 .NET 定义存储访问策略

存储访问策略在服务器端对服务级别的共享访问签名 (SAS) 提供额外的控制级别。 定义存储访问策略可将共享访问签名分组, 并为策略所绑定的共享访问签名提供额外限制。 你可以使用存储访问策略来更改 SAS 的开始时间、到期时间或权限, 或者在颁发 SAS 后对其进行撤消。
  
 以下存储资源支持存储访问策略:  
  
- Blob 容器  
- 文件共享  
- 队列  
- 表  
  
> [!NOTE]
> 容器上的存储访问策略可以与共享访问签名关联, 该签名授予对容器本身或它包含的 blob 的权限。 同样, 文件共享上的存储访问策略可以与为共享本身授予权限的共享访问签名或其包含的文件相关联。  
>
> 仅服务 SAS 支持存储访问策略。 帐户 SAS 或用户委托 SAS 不支持存储访问策略。  

## <a name="create-a-stored-access-policy"></a>创建存储访问策略

下面的代码在容器上创建存储访问策略。 可以使用访问策略指定对容器或其 Blob 上的服务 SAS 的约束。

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

## <a name="see-also"></a>请参阅

- [使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](storage-sas-overview.md)
- [定义存储访问策略](/rest/api/storageservices/define-stored-access-policy)

