---
title: Azure 服务结构中央机密存储
description: 本文介绍如何在 Azure 服务结构中使用中央机密存储。
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 11fb94a9fba40e6f2474ad64f5eb0c454be28ca0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589158"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Azure 服务结构中的中央机密存储 
本文介绍如何在 Azure 服务结构中使用中央机密存储 （CSS） 在服务结构应用程序中创建机密。 CSS 是本地机密存储缓存，用于在内存中加密敏感数据（如密码、令牌和密钥）。

## <a name="enable-central-secrets-store"></a>启用中央机密存储
将以下脚本添加到群集配置下`fabricSettings`，以启用 CSS。 我们建议您使用 CSS 的群集证书以外的证书。 确保加密证书安装在所有节点上，`NetworkService`并且已读取证书私钥的权限。
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
## <a name="declare-a-secret-resource"></a>声明机密资源
可以使用 Azure 资源管理器模板或 REST API 创建机密资源。

### <a name="use-resource-manager"></a>使用资源管理器

使用以下模板使用资源管理器创建机密资源。 模板创建一个秘密`supersecret`资源，但尚未为机密资源设置任何值。


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

要使用`supersecret`REST API 创建机密资源，请对`https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`发出 PUT 请求。 您需要群集证书或管理客户端证书来创建机密资源。

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>设置机密值

### <a name="use-the-resource-manager-template"></a>使用资源管理器模板

使用以下资源管理器模板创建和设置机密值。 此模板将`supersecret`机密资源的秘密值集为版本`ver1`。
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

使用以下脚本使用 REST API 设置机密值。
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>检查机密值
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>使用应用程序中的机密

按照以下步骤在 Service Fabric 应用程序中使用机密。

1. 在**设置.xml**文件中添加包含以下代码段的部分。 请注意，该值采用格式 =`secretname:version`。

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. 导入**应用程序清单.xml**中的部分。
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

   环境变量`SecretPath`将指向存储所有机密的目录。 `testsecrets`节下列出的每个参数都存储在单独的文件中。 应用程序现在可以使用机密，如下所示：
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. 将机密安装到容器中。 使机密在容器内可用所需的唯一更改是 到`specify`中的`<ConfigPackage>`装载点。
以下代码段是修改后的**应用程序清单.xml**。  

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
   机密可在容器内的装载点下使用。

1. 可以通过指定`Type='SecretsStoreRef`将机密绑定到进程环境变量。 以下代码段是如何在**ServiceManifest.xml**`supersecret`中`ver1`将版本绑定到`MySuperSecret`环境变量的示例。

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>后续步骤
了解有关[应用程序和服务安全性](service-fabric-application-and-service-security.md)的更多。
