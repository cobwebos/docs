---
title: Azure Service Fabric-通过支持托管标识部署新的 Azure Service Fabric 群集 |Microsoft Docs
description: 本文介绍如何创建启用了托管标识的新 Service Fabric 群集
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: 80fb06c96f727d2b78faf4f100d59341aeaeff42
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624957"
---
# <a name="create-a-new-azure-service-fabric-cluster-with-managed-identity-support-preview"></a>使用托管标识支持创建新的 Azure Service Fabric 群集 (预览版)

为了访问 Azure Service Fabric 应用程序的托管标识功能, 你必须首先在群集上启用托管标识令牌服务。 此服务负责使用其托管标识对 Service Fabric 应用程序进行身份验证, 并负责代表用户获取访问令牌。 启用该服务后, 可以在左窗格中的 "**系统**" 部分下的 "Service Fabric Explorer" 下查看该服务, 并在其他系统服务旁的 name **Fabric:/system/ManagedIdentityTokenService**下运行。

> [!NOTE]
> 启用**托管标识令牌服务**需要 Service Fabric 运行时版本6.5.658.9590 或更高版本。  

## <a name="enable-the-managed-identity-token-service"></a>启用托管标识令牌服务 
若要在创建群集时启用托管标识令牌服务, 可以在 Azure 资源管理器模板中使用以下代码片段:

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

## <a name="errors"></a>错误

如果部署失败并出现此消息, 则表示群集未处于所需的 Service Fabric 版本 (支持的最低运行时为 6.5 CU2):



```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>后续步骤
* [使用系统分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [使用用户分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [利用服务代码中 Service Fabric 应用程序的托管标识](./how-to-managed-identity-service-fabric-app-code.md)
* [向 Azure Service Fabric 应用程序授予其他 Azure 资源的访问权限](./how-to-grant-access-other-resources.md)

## <a name="related-articles"></a>相关文章
* 查看 Azure 中的[托管标识支持](./concepts-managed-identity.md)Service Fabric

* [在现有 Azure Service Fabric 群集中启用托管标识支持](./configure-existing-cluster-enable-managed-identity-token-service.md)
