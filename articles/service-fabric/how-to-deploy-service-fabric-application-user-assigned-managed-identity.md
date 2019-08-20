---
title: Azure Service Fabric 使用用户分配的托管标识部署应用程序 |Microsoft Docs
description: 本文介绍如何使用用户分配的托管标识部署 Service Fabric 应用程序
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 08/09/2019
ms.author: atsenthi
ms.openlocfilehash: f8dfaa39f02aefbdda1f34afa5011ce5fadbae49
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624921"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity-preview"></a>使用用户分配的托管标识 (预览版) 部署 Service Fabric 应用程序

若要使用托管标识部署 Service Fabric 应用程序, 应用程序需要通过 Azure 资源管理器部署, 通常使用 Azure 资源管理器模板。 有关如何通过 Azure 资源管理器部署 Service Fabric 应用程序的详细信息, 请参阅将[应用程序和服务作为 Azure 资源管理器资源管理](service-fabric-application-arm-resource.md)。

> [!NOTE] 
> 
> 未部署为 Azure 资源的应用程序**不能**具有托管标识。 
>
> API 版本`"2019-06-01-preview"`支持具有托管标识的 Service Fabric 应用程序部署。 还可以对应用程序类型、应用程序类型版本和服务资源使用相同的 API 版本。
>

## <a name="user-assigned-identity"></a>用户分配的标识

若要启用具有用户分配的标识的应用程序, 请首先将**标识**属性添加到**userAssigned**类型的应用程序资源和引用的用户分配的标识。 然后在**应用程序**资源的**properties**节中添加**managedIdentities**节, 其中包含每个用户分配的标识的友好名称列表 principalId 映射。

### <a name="application-template"></a>应用程序模板

若要使用用户分配的标识来启用应用程序, 请首先将**identity**属性添加到类型为**userAssigned**的应用程序资源和被引用用户分配的标识, 然后将**managedIdentities**对象**添加到properties**节, 其中包含为每个用户分配的标识 principalId 映射的友好名称列表。

    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
      ],
      "identity": {
        "type" : "userAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
        }
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        },
        "managedIdentities": [
          {
            "name" : "[parameters('userAssignedIdentityName')]",
            "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
          }
        ]
      }
    }

在上面的示例中, 用户分配的标识的资源名称被用作应用程序的托管标识的友好名称。 下面的示例假定实际友好名称为 "AdminUser"。

### <a name="application-package"></a>应用程序包

1. 对于 Azure 资源管理器模板的`managedIdentities`部分中定义的每个标识, 请在 "**主体**" 部分下的应用程序清单中`<ManagedIdentity>`添加标记。 该`Name`特性需要与`managedIdentities`节中`name`定义的属性相匹配。

    **Applicationmanifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. 在**ServiceManifestImport**节中, 为使用托管标识的服务添加**IdentityBindingPolicy** 。 此策略将`AdminUser`标识映射到特定于服务的标识名称, 稍后需要将该名称添加到服务清单中。

    **Applicationmanifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. 更新服务清单以在**资源**部分中添加`ServiceIdentityRef` **对 microsoft.managedidentity** , 并将名称与应用程序清单中`IdentityBindingPolicy`的匹配:

    **Servicemanifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>后续步骤

* [如何在 Service Fabric 应用程序代码中使用托管标识](how-to-managed-identity-service-fabric-app-code.md)
* [如何向 Service Fabric 应用程序授予对其他 Azure 资源的访问权限](how-to-grant-access-other-resources.md)