---
title: Azure Service Fabric - 配置容器存储库凭据
description: 配置存储库凭据以从容器注册表下载映像
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 47a3fb39693bf6143d4033eed437f65b7e63eabb
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978673"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>配置应用程序的存储库凭据以下载容器映像

通过将 `RepositoryCredentials` 添加到应用程序清单的 `ContainerHostPolicies` 部分来配置容器注册表身份验证。 在以下示例中，为容器注册表 (*myregistry.azurecr.io* 添加帐户和密码) ，这允许服务从存储库下载容器映像。

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

建议使用部署到群集所有节点的加密证书，对存储库密码加密。 当 Service Fabric 将服务包部署到群集时，即可使用加密证书解密密码文本。 Invoke-ServiceFabricEncryptText cmdlet 用于为密码创建密码文本，后者将添加到 ApplicationManifest.xml 文件中。
有关证书和加密语义的详细信息，请参阅[机密管理](service-fabric-application-secret-management.md)。

## <a name="configure-cluster-wide-credentials"></a>配置群集级凭据

Service Fabric 允许你配置群集级凭据，应用程序可以将这些凭据用作默认存储库凭据。

可以通过在 ApplicationManifest.xml 中向 `ContainerHostPolicies` 添加 `UseDefaultRepositoryCredentials` 属性来启用或禁用此功能；将其值设为 `true` 则启用，将其值设为 `false` 则禁用。

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

然后，Service Fabric 会使用可在 ClusterManifest 中的 `Hosting` 部分下指定的默认存储库凭据。  如果 `UseDefaultRepositoryCredentials` 为 `true`，则 Service Fabric 将从 ClusterManifest 中读取以下值：

* DefaultContainerRepositoryAccountName (string)
* DefaultContainerRepositoryPassword (string)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* DefaultContainerRepositoryPasswordType（字符串）

下面是可以在 ClusterManifestTemplate.json 文件中的 `Hosting` 部分内添加的内容的示例。 可以在群集创建时或配置升级后期添加 `Hosting` 节。 有关详细信息，请参阅[更改 Azure Service Fabric 群集设置](service-fabric-cluster-fabric-settings.md)和[管理 Azure Service Fabric 应用程序机密](service-fabric-application-secret-management.md)

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          }
        ]
      },
]
```

## <a name="use-tokens-as-registry-credentials"></a>使用令牌作为注册表凭据

Service Fabric 支持使用令牌作为凭据下载容器的映像。  此功能利用基础虚拟机规模集的托管标识对注册表进行身份验证，从而消除了管理用户凭据的需要。  请参阅 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)获取详细信息。  使用此功能需要执行以下步骤：

1. 确保已为 VM 启用系统分配的托管标识。

    ![Azure 门户：创建虚拟机规模集标识选项](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

> [!NOTE]
> 对于用户分配的托管标识，请跳过此步骤。 如果规模集只与一个用户分配的托管标识相关联，则以下剩余步骤将起作用。

2. 向虚拟机规模集授予从注册表中拉取/读取映像的权限。 从 Azure 门户中 Azure 容器注册表的“访问控制(IAM)”边栏选项卡中，为虚拟机添加角色分配：

    ![将 VM 主体添加到 ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. 接下来，修改应用程序清单。 在 `ContainerHostPolicies` 部分中，添加属性 `‘UseTokenAuthenticationCredentials=”true”`。

    ```xml
      <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" UseTokenAuthenticationCredentials="true">
          <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        </ContainerHostPolicies>
        <ResourceGovernancePolicy CodePackageRef="NodeService.Code" MemoryInMB="256"/>
      </Policies>
      </ServiceManifestImport>
    ```

    > [!NOTE]
    > 当 `UseTokenAuthenticationCredentials` 为 true 时，将标志 `UseDefaultRepositoryCredentials` 设置为 true 将导致部署过程中出现错误。

## <a name="next-steps"></a>后续步骤

* 了解有关[容器注册表身份验证](../container-registry/container-registry-authentication.md)的更多内容。
