---
title: 在现有服务结构群集中配置托管标识支持
description: 下面了解如何在现有 Azure 服务结构群集中启用托管标识支持
ms.topic: article
ms.date: 03/11/2019
ms.custom: sfrev
ms.openlocfilehash: 73c890e960f26b8e0e3fa924d9ff6b7a4cd4a4dc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415689"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster"></a>在现有服务结构群集中配置托管标识支持

要在 Service Fabric 应用程序中[对 Azure 资源使用托管标识](../active-directory/managed-identities-azure-resources/overview.md)，请首先在群集上启用*托管标识令牌服务*。 此服务负责使用 Service Fabric 应用程序的托管标识对这些应用程序进行身份验证，以及代表它们获取访问令牌。 启用此服务以后，即可在 Service Fabric Explorer 中左侧窗格的“系统”部分**** 看到它，它以 **fabric:/System/ManagedIdentityTokenService** 名称运行。

> [!NOTE]
> 若要启用**托管标识令牌服务**，必须使用 Service Fabric 运行时 6.5.658.9590 或更高版本。  
>
> 可以在 Azure 门户中查找 Service Fabric 版群集，方法是：打开群集资源，然后在“基本信息”部分查找“Service Fabric 版本”属性。********
>
> 如果群集处于“手动”升级模式，则需先将其升级到**** 6.5.658.9590 或更高版本。

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>在现有群集中启用*托管标识令牌服务*

要在现有群集中启用托管标识令牌服务，您需要启动群集升级，指定两个更改：（1） 启用托管标识令牌服务，以及 （2） 请求重新启动每个节点。 首先，添加群集 Azure 资源管理器模板的以下代码段：

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

若要让更改生效，还需更改升级策略，指定在升级进展到群集时，在每个节点上以强制方式重启 Service Fabric 运行时。 此重启确保新启用的系统服务在每个节点上启动并运行。 在下面的代码段中`forceRestart`，是启用重新启动的基本设置。 对于其余参数，请使用下面描述的值或使用为群集资源指定的现有自定义值。 通过在服务交换矩阵资源或resources.azure.com上选择"交换矩阵升级"选项，可以从 Azure 门户查看结构升级策略的自定义设置（"升级说明"）。 升级策略（"升级描述"）的默认选项无法从电源壳或resources.azure.com查看。 有关详细信息，请参阅[群集升级策略](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.servicefabric.models.clusterupgradepolicy?view=azure-dotnet)。  

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
> 成功完成升级以后，请勿忘记回退 `forceRestart` 设置，尽量减少后续升级的影响。 

## <a name="errors-and-troubleshooting"></a>错误和故障排除

如果部署失败并出现以下错误，则表明群集运行时所在的 Service Fabric 的版本不够高：

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>后续步骤
* [使用系统分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [使用用户分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [从服务代码中利用 Service Fabric 应用程序的托管标识](./how-to-managed-identity-service-fabric-app-code.md)
* [为 Azure Service Fabric 应用程序授予对其他 Azure 资源的访问权限](./how-to-grant-access-other-resources.md)
