---
title: Azure Service Fabric 使用用户分配的托管标识部署应用程序 |Microsoft Docs
description: 本文介绍如何使用用户分配的托管标识部署 Service Fabric 应用程序
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 08/09/2019
ms.author: atsenthi
ms.openlocfilehash: 0cc1e51a4d5f9ad54866066a4247e1588da381a6
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71037493"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity-preview"></a>使用用户分配的托管标识部署 Service Fabric 应用程序（预览）

若要使用托管标识部署 Service Fabric 应用程序，需通过 Azure 资源管理器部署应用程序，通常需要使用 Azure 资源管理器模板。 若要详细了解如何通过 Azure 资源管理器部署 Service Fabric 应用程序，请参阅[将应用程序和服务作为 Azure 资源管理器资源进行管理](service-fabric-application-arm-resource.md)。

> [!NOTE] 
> 
> 未部署为 Azure 资源的应用程序**不能**有托管标识。 
>
> API 版本 `"2019-06-01-preview"` 支持使用托管标识部署 Service Fabric 应用程序。 另外，不管应用程序类型、应用程序类型版本和服务资源如何，你都可以使用同一 API 版本。
>

## <a name="user-assigned-identity"></a>用户分配的标识

若要为应用程序启用用户分配的托管标识，请先将类型为 **userAssigned** 的 **identity** 属性添加到应用程序资源和引用的用户分配标识。 然后将 **managedIdentities** 节添加到 **application** 资源的 **properties** 节中，该资源包含一个易记名称的列表，可以为每个用户分配的标识进行 principalId 映射。 有关用户分配的标识的详细信息[，请参阅创建、列出或删除用户分配的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell)。

### <a name="application-template"></a>应用程序模板

若要为应用程序启用用户分配的托管标识，请先将类型为 **userAssigned** 的 **identity** 属性添加到应用程序资源和引用的用户分配标识，然后将 **managedIdentities** 对象添加到 **properties** 节中，该节包含一个易记名称的列表，可以为每个用户分配的标识进行 principalId 映射。

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

在上面的示例中，用户分配标识的资源名称用作应用程序的托管标识的易记名称。 以下示例假定实际的易记名称为“AdminUser”。

### <a name="application-package"></a>应用程序包

1. 对于在 Azure 资源管理器模板的 `managedIdentities` 节中定义的每个标识，请在应用程序清单的 **Principals** 节下添加 `<ManagedIdentity>` 标记。 `Name` 属性需与 `managedIdentities` 节中定义的 `name` 属性匹配。

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. 在 **ServiceManifestImport** 节中，为使用托管标识的服务添加 **IdentityBindingPolicy**。 此策略将 `AdminUser` 标识映射到特定于服务的标识名称，该名称需在以后添加到服务清单中。

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. 更新服务清单，将 **ManagedIdentity** 添加到 **Resources** 节中，其名称与应用程序清单的 `IdentityBindingPolicy` 中的 `ServiceIdentityRef` 匹配：

    **ServiceManifest.xml**

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
* [如何为 Service Fabric 应用程序授予对其他 Azure 资源的访问权限](how-to-grant-access-other-resources.md)
