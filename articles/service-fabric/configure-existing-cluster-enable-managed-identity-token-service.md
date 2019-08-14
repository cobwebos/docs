---
title: Azure Service Fabric-配置现有 Azure Service Fabric 群集以启用托管标识支持 |Microsoft Docs
description: 本文介绍如何配置现有 Azure Service Fabric 群集以启用对托管标识的支持
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: dc9d7ba9499f2c98370b14fd88d38dffc65480e5
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68964030"
---
# <a name="configure-an-existing-azure-service-fabric-cluster-to-enable-managed-identity-support"></a>配置现有 Azure Service Fabric 群集以启用托管标识支持
为了访问 Azure Service Fabric 应用程序的托管标识功能, 你必须首先在群集上启用**托管标识令牌服务**。 此服务负责使用其托管标识对 Service Fabric 应用程序进行身份验证, 并负责代表用户获取访问令牌。 启用该服务后, 可以在左窗格中的 "**系统**" 部分下的 "Service Fabric Explorer" 下查看该服务, 并在名称**Fabric:/System/ManagedIdentityTokenService**下运行。

> [!NOTE]
> 启用**托管标识令牌服务**需要 Service Fabric 运行时版本6.5.658.9590 或更高版本。  
> 
> 可以通过打开群集资源并在 "**概要**" 部分中检查 " **Service Fabric 版本**" 属性, 从 Azure 门户找到群集的 Service Fabric 版本。
> 
> 如果群集处于**手动**升级模式, 则需要首先将其升级到6.5.658.9590 或更高版本。


## <a name="enable-the-managed-identity-token-service-in-an-existing-cluster"></a>在现有群集中启用托管标识令牌服务
若要在现有群集中启用托管标识令牌服务, 你将需要启动群集升级, 指定两个更改: 启用托管标识令牌服务并请求重新启动每个节点。 为此, 请在 Azure 资源管理器模板中添加以下两个代码片段:

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

为了使更改生效, 你还需要更改升级策略, 以便在升级通过群集时, 在每个节点上指定 Service Fabric 运行时的强制重新启动。 此重新启动可确保新启用的系统服务在每个节点上启动并运行。 在下面的代码片段`forceRestart`中, 是基本设置; 对其余设置使用现有值。  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> 成功完成升级后, 请不要忘记回滚`forceRestart`设置, 以最大程度地降低后续升级的影响。 

## <a name="errors-and-troubleshooting"></a>错误和疑难解答

如果部署失败, 并出现以下消息, 则表示群集运行的 Service Fabric 版本不足:

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
