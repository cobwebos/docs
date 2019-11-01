---
title: Azure Service Fabric-使用 Service Fabric application KeyVault 引用 |Microsoft Docs
description: 本文介绍如何使用 service fabric KeyVaultReference 支持应用程序机密。
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 09/20/2019
ms.author: atsenthi
ms.openlocfilehash: b0f1a081727721ea0325276cf9edd52c6d71fb6b
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2019
ms.locfileid: "73243855"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>对 Service Fabric 应用程序（预览版）的 KeyVaultReference 支持

构建云应用程序时，常见的难题是如何安全地存储应用程序所需的机密。 例如，你可能想要将容器存储区凭据存储在 keyvault 中，并在应用程序清单中引用它。 Service Fabric KeyVaultReference 使用 Service Fabric 管理的标识，并使其易于引用 keyvault 的机密。 本文的其余部分详细介绍了如何使用 Service Fabric KeyVaultReference，并提供了一些典型的用法。

## <a name="prerequisites"></a>必备组件

- 应用程序的托管标识（MIT）
    
    Service Fabric KeyVaultReference 支持使用应用程序的托管标识，因此 planing 使用 KeyVaultReferences 的应用程序应使用托管标识。 按照此[文档](concepts-managed-identity.md)为应用程序启用托管标识。

- 中央机密存储区（CSS）。

    中央机密存储区（CSS）是服务结构的加密本地机密缓存，提取后，KeyVaultReference 会缓存在 CSS 中。

    将以下字段添加到 `fabricSettings` 下的群集配置，以启用 KeyVaultReference 支持所需的所有功能。

    ```json
    "fabricSettings": 
    [
        ...
    {
        "parameters":  [
            "name":  "CentralSecretService"
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                }
                ],
            },
            {
                "name":  "ManagedIdentityTokenService",
                "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                }
                ]
            }
            ]
    ```

    > [!NOTE] 
    > 建议为 CSS 使用单独的加密证书。 可以将其添加到 "CentralSecretService" 部分。

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```

- 向应用程序授予对 keyvault 的托管标识访问权限

    参考本[文档](how-to-grant-access-other-resources.md)，了解如何向托管标识授予对 keyvault 的访问权限。 另请注意，如果使用系统分配的托管标识，则仅在部署应用程序后创建托管标识。

## <a name="keyvault-secret-as-application-parameter"></a>Keyvault secret 作为应用程序参数
假设应用程序需要读取存储在 keyvault 中的后端数据库密码，Service Fabric KeyVaultReference 支持使其变得简单。 以下示例使用 Service Fabric KeyVaultReference 支持从 keyvault 读取 `DBPassword` 机密。

- 将节添加到 settings .xml

    定义类型 `KeyVaultReference` 和值 `DBPassword` 参数 `<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- 引用 Applicationmanifest.xml 中的新节 `<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="ttkappuser" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- 在应用程序中使用 KeyVaultReference

    Service Fabric 服务实例化时，将使用应用程序的托管标识解析 KeyVaultReference 参数。 `<Section  Name=dbsecrets>` 下列出的每个参数将是 Value SecretPath 指向的文件夹下的文件。 下面C#的代码段演示如何在应用程序中读取 this->dbpassword。

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > 对于容器方案，可以使用装入点来控制将 `secrets` 装载到的位置。

## <a name="keyvault-secret-as-environment-variable"></a>Keyvault secret 作为环境变量

Service Fabric 环境变量现在支持 KeyVaultReference 类型，下面的示例演示如何将环境变量绑定到 KeyVault 中存储的机密。

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>Keyvault secret 作为容器存储库密码
KeyVaultReference 是容器 RepositoryCredentials 支持的类型，下面的示例演示如何使用 keyvault 引用作为容器存储库密码。
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>常见问题解答
- 需要为 KeyVaultReference 支持启用托管标识，如果在不启用托管标识的情况下使用 KeyVaultReference，应用程序激活将会失败。

- 如果使用系统分配的标识，则它仅在部署应用程序后创建，这将创建循环依赖项。 部署应用程序后，可以将系统分配的标识访问权限授予 keyvault。 可以按名称 {cluster}/{application name}/{servicename} 查找系统分配的标识。

- Keyvault 需要与 service fabric 群集位于同一订阅中。 

## <a name="next-steps"></a>后续步骤

* [Azure KeyVault 文档](https://docs.microsoft.com/azure/key-vault/)
