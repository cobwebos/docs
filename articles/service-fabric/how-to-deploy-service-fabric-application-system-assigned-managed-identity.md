---
title: Azure Service Fabric 使用系统分配的托管标识部署 Azure Service Fabric 应用程序 |Microsoft Docs
description: 本文介绍如何将系统分配的托管标识分配给 Azure Service Fabric 应用程序
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: f9d7f87e9d7b1761c165e08e37cd6a3237c8212a
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965528"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity"></a>部署包含系统分配的托管标识的 Service Fabric 应用程序

为了访问 Azure Service Fabric 应用程序的托管标识功能, 你必须首先在群集上启用托管标识令牌服务。 此服务负责使用其托管标识对 Service Fabric 应用程序进行身份验证, 并负责代表用户获取访问令牌。 启用该服务后, 可以在左窗格中的 "**系统**" 部分下的 "Service Fabric Explorer" 下查看该服务, 并在其他系统服务旁的 name **Fabric:/system/ManagedIdentityTokenService**下运行。

> [!NOTE] 
> 从 API 版本`"2019-06-01-preview"`开始, 支持部署具有托管标识的 Service Fabric 应用程序。 还可以对应用程序类型、应用程序类型版本和服务资源使用相同的 API 版本。 支持的最低 Service Fabric 运行时为 6.5 CU2。

## <a name="system-assigned-managed-identity"></a>系统分配的托管标识

### <a name="application-template"></a>应用程序模板

若要使用系统分配的托管标识启用应用程序, 请使用类型**systemAssigned**将**标识**属性添加到应用程序资源, 如以下示例中所示:

```json
    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
此属性分别向 Azure 资源管理器、托管标识和 Service Fabric 资源提供程序声明此资源应具有隐式 (`system assigned`) 托管标识。

### <a name="application-and-service-package"></a>应用程序和服务包

1. 更新应用程序清单以在**主体**节中添加**对 microsoft.managedidentity**元素, 其中包含单一条目, 如下所示:

    **Applicationmanifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    这会将分配给应用程序的标识作为资源映射到友好名称, 以便进一步分配给构成应用程序的服务。 

2. 在对应于正在分配托管标识的服务的**ServiceManifestImport**节中, 添加**IdentityBindingPolicy**元素, 如下所示:

    **Applicationmanifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    此元素将应用程序的标识分配给服务;如果没有此分配, 服务将无法访问应用程序的标识。 在上面的代码片段中`SystemAssigned` , 标识 (即保留关键字) 映射到友好名称`WebAdmin`下的服务定义。

3. 更新服务清单以在**Resources**节中添加**对 microsoft.managedidentity**元素, 该元素的名称与应用程序清单中`ServiceIdentityRef` `IdentityBindingPolicy`定义的设置值匹配:

    **Servicemanifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    这是从服务定义的角度来看, 从标识到服务的等效映射。 此标识由其在应用程序清单中声明`WebAdmin`的友好名称 () 引用。

## <a name="related-articles"></a>相关文章

* 查看 Azure 中的[托管标识支持](./concepts-managed-identity.md)Service Fabric

* [部署新的](./configure-new-azure-service-fabric-enable-managed-identity.md)支持托管标识的 Azure Service Fabric 群集 

* 在现有 Azure Service Fabric 群集中[启用托管标识](./configure-existing-cluster-enable-managed-identity-token-service.md)

* 利用源代码中 Service Fabric 应用程序的[托管标识](./how-to-managed-identity-service-fabric-app-code.md)

* [使用用户分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)

* [向 Azure Service Fabric 应用程序授予其他 Azure 资源的访问权限](./how-to-grant-access-other-resources.md)
