---
title: Azure Service Fabric 使用系统分配的托管标识部署 Azure Service Fabric 应用程序 |Microsoft Docs
description: 本文介绍如何将系统分配的托管标识分配给 Azure Service Fabric 应用程序
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: cf971d71c2566d91bc5a2490d47521725c62b17d
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973410"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity-preview"></a>使用系统分配的托管标识部署 Service Fabric 应用程序（预览）

若要访问 Azure Service Fabric 应用程序的托管标识功能，必须先在群集上启用托管标识令牌服务。 此服务负责使用 Service Fabric 应用程序的托管标识对这些应用程序进行身份验证，以及代表它们获取访问令牌。 启用此服务以后，即可在 Service Fabric Explorer 中左侧窗格的“系统”部分看到它，它在其他系统服务旁边以 **fabric:/System/ManagedIdentityTokenService** 名称运行。

> [!NOTE] 
> 从 API 版本 `"2019-06-01-preview"` 开始，我们就支持使用托管标识部署 Service Fabric 应用程序。 另外，不管应用程序类型、应用程序类型版本和服务资源如何，你都可以使用同一 API 版本。 支持的最低 Service Fabric 运行时为 6.5 CU2。 在 additoin 中，生成/包环境还应具有 SF .Net SDK CU2 或更高版本

## <a name="system-assigned-managed-identity"></a>系统分配的托管标识

### <a name="application-template"></a>应用程序模板

若要为应用程序启用系统分配的托管标识，请将类型为 **systemAssigned** 的 **identity** 属性添加到应用程序资源，如以下示例所示：

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
此属性向 Azure 资源管理器、托管标识和 Service Fabric 资源提供程序分别声明：此资源应该有一个隐式 (`system assigned`) 托管标识。

### <a name="application-and-service-package"></a>应用程序和服务包

1. 更新应用程序清单，在 **Principals** 节添加包含单个条目的 **ManagedIdentity** 元素，如下所示：

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    这会将分配给应用程序作为资源的标识映射到某个易记名称，以便进一步分配给包含该应用程序的服务。 

2. 在 **ServiceManifestImport** 节（对应于已向其分配了托管标识的服务）中添加 **IdentityBindingPolicy** 元素，如下所示：

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    此元素将应用程序的标识分配给服务；没有此分配，服务将无法访问应用程序的标识。 在上面的代码片段中，`SystemAssigned` 标识（保留关键字）映射到服务的定义，采用的易记名称为 `WebAdmin`。

3. 更新服务清单，将 **ManagedIdentity** 元素添加到 **Resources** 节中，其名称与 `ServiceIdentityRef` 设置的值匹配，该设置来自应用程序清单中的 `IdentityBindingPolicy` 定义：

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    这是一个等效映射，等效于将标识映射到服务（如上所述），但却是从服务定义的角度来看。 在这里，标识按其易记名称 (`WebAdmin`) 引用，如应用程序清单所述。

## <a name="next-steps"></a>后续步骤
* 查看 Azure Service Fabric 中的[托管标识支持](./concepts-managed-identity.md)
* [部署](./configure-new-azure-service-fabric-enable-managed-identity.md)支持托管标识的新 Azure Service Fabric 群集 
* 在现有 Azure Service Fabric 群集中[启用托管标识](./configure-existing-cluster-enable-managed-identity-token-service.md)
* 利用 Service Fabric 应用程序的[托管标识（来自源代码）](./how-to-managed-identity-service-fabric-app-code.md)
* [使用用户分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [为 Azure Service Fabric 应用程序授予对其他 Azure 资源的访问权限](./how-to-grant-access-other-resources.md)
