---
title: Azure Service Fabric-配置容器存储凭据 |Microsoft Docs
description: 配置存储库凭据以从容器注册表下载映像
services: service-fabric
documentationcenter: .net
author: arya
manager: gkhanna
ms.assetid: b93d31e5-9e4c-4405-b266-c0efa4643d97
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 8/1/2019
ms.author: arya
ms.openlocfilehash: c415739934e2318ea5287d5eed9f8235029b666f
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74405623"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>配置应用程序的存储库凭据以下载容器映像

将 `RepositoryCredentials` 添加到 ApplicationManifest.xml 文件的 `ContainerHostPolicies`，配置容器注册表身份验证。 为 myregistry.azurecr.io 容器注册表添加帐户和密码，以便服务从存储库下载容器映像。

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
有关证书和加密语义的详细信息，请参阅[机密管理](service-fabric-application-secret-management.md)。

## <a name="configure-cluster-wide-credentials"></a>配置群集级凭据

Service Fabric 允许你配置群集范围的凭据，这些凭据可由应用程序用作默认存储库凭据。

可以通过使用 `true` 或 `false` 值将 `UseDefaultRepositoryCredentials` 特性添加到 Applicationmanifest.xml 中 `ContainerHostPolicies` 来启用或禁用此功能。

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

然后 Service Fabric 使用默认存储库凭据，这些凭据可在 "`Hosting`" 部分下的 Clustermanifest.xml 中指定。  如果 `UseDefaultRepositoryCredentials` 为 `true`，则 Service Fabric 将从 ClusterManifest 中读取以下值：

* DefaultContainerRepositoryAccountName (string)
* DefaultContainerRepositoryPassword (string)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* DefaultContainerRepositoryPasswordType (string) --- 从 6.4 版运行时起受支持

下面是可以在 ClusterManifestTemplate 文件的 `Hosting` 部分中添加的内容的示例。 可以在群集创建时或配置升级后期添加 `Hosting` 节。 有关详细信息，请参阅[更改 Azure Service Fabric 群集设置](service-fabric-cluster-fabric-settings.md)和[管理 Azure Service Fabric 应用程序机密](service-fabric-application-secret-management.md)

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

## <a name="leveraging-the-managed-identity-of-the-virtual-machine-scale-set-by-using-managed-identity-service-msi"></a>通过使用托管标识服务（MSI）利用虚拟机规模集的托管标识

Service Fabric 支持使用令牌作为凭据为容器下载映像。  此功能利用底层虚拟机规模集的托管标识对注册表进行身份验证，从而无需管理用户凭据。  有关 MSI 的详细信息，请参阅[托管服务标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。  使用此功能需要执行以下步骤：

1.  确保为 VM 启用系统分配的托管标识（请参阅下面的屏幕截图）

    ![创建虚拟机规模集标识](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2.  然后，将权限授予 VM （SS），以从注册表中提取或读取映像。  通过 Azure 边栏选项卡中转到你的 ACR 的访问控制（IAM），并为 VM （SS）授予正确的权限，如下所示：

    ![将 VM 主体添加到 ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3.  完成上述步骤后，请修改 applicationmanifest.xml 文件。  查找标记为 "ContainerHostPolicies" 的标记，并 `‘UseTokenAuthenticationCredentials=”true”`中添加属性。

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
    > 当 `UseTokenAuthenticationCredentials` 为 true 时，标志 `UseDefaultRepositoryCredentials` 设置为 true 将导致部署过程中出现错误。

## <a name="next-steps"></a>后续步骤

* 查看有关[容器注册表身份验证](/azure/container-registry/container-registry-authentication)的详细信息。
