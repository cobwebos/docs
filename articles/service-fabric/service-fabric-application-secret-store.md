---
title: Service Fabric 密钥存储
description: 本文介绍如何使用机密存储 Service Fabric。
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 16608d9eaf12fc9abc535ef316d7b5e8b74a8b37
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457509"
---
#  <a name="service-fabric-secrets-store"></a>Service Fabric 密钥存储
本文介绍如何使用 Service Fabric 机密存储（CSS）在 Service Fabric 应用程序中创建和使用机密。 CSS 是本地密钥存储缓存，用于保留在内存中加密的敏感数据，例如密码、令牌和密钥。

## <a name="enabling-secrets-store"></a>启用密钥存储
 将以下添加到 `fabricSettings` 下的群集配置，以启用 CSS。 建议使用与用于 CSS 的群集证书不同的证书。 请确保已在所有节点上安装加密证书，并 `NetworkService` 对证书的私钥具有读取权限。
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
## <a name="declare-secret-resource"></a>声明密钥资源
您可以使用资源管理器模板或使用 REST API 创建机密资源。

* 使用 Resource Manager 模板
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
上述模板创建 `supersecret` 的机密资源，但尚未为机密资源设置值。

* 使用 REST API

若要创建机密资源，`supersecret` 向 `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`发出 PUT 请求。 你需要群集证书或管理员客户端证书来创建机密。

```powershell
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint>
```

## <a name="set-secret-value"></a>设置机密值
* 使用 Resource Manager 模板

以下资源管理器模板为版本 `ver1`的机密 `supersecret` 创建和设置值。
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
* 使用 REST API

```powershell
$Params = @{"properties": {"value": "mysecretpassword"}}
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
## <a name="using-the-secret-in-your-application"></a>在应用程序中使用机密

1.  在 settings .xml 文件中添加包含以下内容的部分。 请注意，此处的值的格式为 {`secretname:version`}

```xml
  <Section Name="testsecrets">
   <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
  </Section>
```
2. 现在，导入 Applicationmanifest.xml 中的节
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

环境变量 "SecretPath" 将指向存储所有密码的目录。 "`testsecrets`" 一节中列出的每个参数将存储在单独的文件中。 应用程序现在可以使用机密，如下所示
```C#
secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
```
3. 将机密装载到容器

仅更改在容器中提供机密所需的更改，以在 `<ConfigPackage>`中指定一个装入点。
下面是修改后的 Applicationmanifest.xml  

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
密码将在容器中的装入点下提供。

4. 将机密绑定到环境变量 

可以通过指定 Type = ' SecretsStoreRef ' 将机密绑定到进程环境变量。 下面的示例演示如何将 `ver1` `supersecret` 版本绑定到环境变量 `MySuperSecret` Servicemanifest.xml。

```xml
<EnvironmentVariables>
  <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
</EnvironmentVariables>
```
## <a name="next-steps"></a>后续步骤
深入了解[应用程序和服务安全性](service-fabric-application-and-service-security.md)
