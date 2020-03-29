---
title: Azure 服务结构 - 配置容器存储库凭据
description: 配置存储库凭据以从容器注册表下载映像
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 9bd6e6a0a22f7568760f014897fd28ff47e9450b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934977"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>为应用程序配置存储库凭据以下载容器映像

通过添加到`RepositoryCredentials`应用程序清单`ContainerHostPolicies`的部分来配置容器注册表身份验证。 添加容器注册表的帐户和*密码（myregistry.azurecr.io*在下面的示例中），这允许服务从存储库下载容器映像。

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

建议使用部署到群集所有节点的加密证书来加密存储库密码。 当 Service Fabric 将服务包部署到群集时，即可使用加密证书解密密码文本。 Invoke-ServiceFabricEncryptText cmdlet 用于为密码创建密码文本，后者将添加到 ApplicationManifest.xml 文件中。
有关证书和加密语义的更多信息，请参阅[密钥管理](service-fabric-application-secret-management.md)。

## <a name="configure-cluster-wide-credentials"></a>配置群集级凭据

Service Fabric 允许您配置群集范围的凭据，这些凭据可以由应用程序用作默认存储库凭据。

`UseDefaultRepositoryCredentials`此功能可以通过将属性`ContainerHostPolicies`添加到 具有`true`或 值的 ApplicationManifest.xml 中来`false`启用或禁用此功能。

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

然后，Service Fabric 使用默认存储库凭据，可在`Hosting`"群集清单"中分区下指定。  如果 `UseDefaultRepositoryCredentials` 为 `true`，则 Service Fabric 将从 ClusterManifest 中读取以下值：

* DefaultContainerRepositoryAccountName (string)
* DefaultContainerRepositoryPassword (string)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* 默认容器存储库密码类型（字符串）

下面是在群集清单模板.json 文件中`Hosting`可以添加的内容的示例。 可以在群集创建时或配置升级后期添加 `Hosting` 节。 有关详细信息，请参阅[更改 Azure Service Fabric 群集设置](service-fabric-cluster-fabric-settings.md)和[管理 Azure Service Fabric 应用程序机密](service-fabric-application-secret-management.md)

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

Service Fabric 支持使用令牌作为凭据来下载容器的图像。  此功能利用基础虚拟机规模集的*托管标识*对注册表进行身份验证，无需管理用户凭据。  有关详细信息[，请参阅 Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。  使用此功能需要以下步骤：

1. 确保为 VM 启用*系统分配的托管标识*。

    ![Azure 门户：创建虚拟机缩放集标识选项](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2. 向虚拟机缩放集授予从注册表中提取/读取映像的权限。 从 Azure 容器注册表在 Azure 门户中的访问控制 （IAM） 边栏选项卡中，为虚拟机添加*角色分配*：

    ![将 VM 主体添加到 ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. 接下来，修改应用程序清单。 在`ContainerHostPolicies`部分中，添加属性`‘UseTokenAuthenticationCredentials=”true”`。

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
    > 设置为`UseDefaultRepositoryCredentials`true 时`UseTokenAuthenticationCredentials`的标志为 true 将导致部署期间出错。

## <a name="next-steps"></a>后续步骤

* 查看有关[容器注册表身份验证](../container-registry/container-registry-authentication.md)的更多。
