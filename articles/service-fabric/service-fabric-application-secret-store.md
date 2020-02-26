---
title: Azure Service Fabric 中央机密存储
description: 本文介绍如何在 Azure Service Fabric 中使用中央机密存储。
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 11fb94a9fba40e6f2474ad64f5eb0c454be28ca0
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589158"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Azure Service Fabric 中的中心机密存储 
本文介绍如何在 Azure Service Fabric 中使用中央机密存储（CSS）在 Service Fabric 应用程序中创建机密。 CSS 是本地密钥存储缓存，用于保留在内存中加密的敏感数据，例如密码、令牌和密钥。

## <a name="enable-central-secrets-store"></a>启用中心机密存储
将以下脚本添加到 `fabricSettings` 下的群集配置，以启用 CSS。 对于 CSS，我们建议使用除群集证书以外的证书。 请确保已在所有节点上安装加密证书，并且该 `NetworkService` 对证书的私钥具有读取权限。
  ```json
    "fabricSettings": 
    [
        ...
    {
        "name":  "CentralSecretService",
        "parameters":  [
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
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
     }
        ...
     ]
```
## <a name="declare-a-secret-resource"></a>声明密钥资源
可以使用 Azure 资源管理器模板或 REST API 创建机密资源。

### <a name="use-resource-manager"></a>使用资源管理器

使用以下模板来使用资源管理器创建密钥资源。 该模板将创建 `supersecret` 的机密资源，但尚未为机密资源设置值。


```json
   "resources": [
      {
        "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
          }
        }
      ]
```

### <a name="use-the-rest-api"></a>使用 REST API

若要使用 REST API 创建 `supersecret` 机密资源，请向 `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`发出 PUT 请求。 需要使用群集证书或管理员客户端证书来创建密钥资源。

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>设置机密值

### <a name="use-the-resource-manager-template"></a>使用资源管理器模板

使用以下资源管理器模板来创建和设置密钥值。 此模板将 `supersecret` 密钥资源的机密值设置为 `ver1`版本。
```json
  {
  "parameters": {
  "supersecret": {
      "type": "string",
      "metadata": {
        "description": "supersecret value"
      }
   }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
        }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "supersecret/ver1",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/supersecret"
      ],
      "properties": {
        "value": "[parameters('supersecret')]"
      }
    }
  ],
  ```
### <a name="use-the-rest-api"></a>使用 REST API

使用以下脚本来使用 REST API 设置机密值。
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>检查机密值
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>在应用程序中使用机密

请按照以下步骤在 Service Fabric 应用程序中使用该机密。

1. 使用以下代码片段在**settings .xml**文件中添加一个节。 请注意，此处的值为 {`secretname:version`} 格式。

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. 导入**applicationmanifest.xml**中的部分。
   ```xml
     <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
           </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```

   环境变量 `SecretPath` 将指向存储所有机密的目录。 "`testsecrets`" 部分下列出的每个参数都存储在单独的文件中。 应用程序现在可以使用机密，如下所示：
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. 将机密装载到容器中。 要使密钥在容器内可用所需的唯一更改是在 `<ConfigPackage>`中 `specify` 装入点。
以下代码片段是修改后的**applicationmanifest.xml**。  

   ```xml
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
           <!-- Linux Container
            <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
           -->
         </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```
   密码在容器中的装入点下可用。

1. 可以通过指定 `Type='SecretsStoreRef`将机密绑定到进程环境变量。 以下代码片段举例说明了如何将 `ver1` `supersecret` 版本绑定到环境变量 `MySuperSecret` **servicemanifest.xml**。

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>后续步骤
详细了解[应用程序和服务安全性](service-fabric-application-and-service-security.md)。
