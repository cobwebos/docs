---
title: 管理 Azure Service Fabric 网格应用程序机密 | Microsoft Docs
description: 管理应用程序机密，以便可以安全地创建并部署 Service Fabric 网格应用程序。
services: service-fabric-mesh
keywords: 机密
author: aljo
ms.author: aljo
ms.date: 11/28/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: chackdan
ms.openlocfilehash: d92726ebc2cd4c6c44afdb2d2a9f53ab5441ac32
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2018
ms.locfileid: "52891850"
---
# <a name="manage-service-fabric-mesh-application-secrets"></a>管理 Service Fabric 网格应用程序机密
Service Fabric 网格支持将机密作为 Azure 资源。 Service Fabric 网格机密可以是任何敏感文本信息，例如存储连接字符串、密码或应该安全存储和传输的其他值。 本文展示了如何使用 Service Fabric Secure Store Service 来部署和维护机密。

网格应用程序机密包括：
* 一个**机密**资源，它是一个用于存储文本机密的容器。 **机密**资源中包含的机密将安全地进行存储和传输。
* 存储在**机密**资源容器中的一个或多个**机密/值**资源。 每个**机密/值**资源都由版本号予以区分。 无法修改**机密/值**资源的版本，只能追加新版本。

管理机密包括以下步骤：
1. 在 Azure 资源模型 YAML 或 JSON 文件中使用 inlinedValue 种类和 SecretsStoreRef contentType 定义声明一个网格**机密**资源。
2. 在 Azure 资源模型 YAML 或 JSON 文件中声明将存储在**机密**资源（来自步骤 1）中的网格**机密/值**资源。
3. 修改网格应用程序以引用网格机密值。
4. 部署或滚动升级网格应用程序以使用机密值。
5. 使用 Azure "az" CLI 命令进行 Secure Store Service 生命周期管理。

## <a name="declare-a-mesh-secrets-resource"></a>声明网格机密资源
网格机密资源是在 Azure 资源模型 JSON 或 YAML 文件中使用 inlinedValue 种类和 SecretsStoreRef contentType 定义声明的。 网格机密资源支持源自 Secure Store Service 的机密。 
>
下面是有关如何在 JSON 文件中声明网格机密资源的示例：

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "eastus",
      "metadata": {
        "description": "Location of the resources."
      }
    },
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MySecret.txt",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "My Mesh Application Secret",
        "contentType": "SecretsStoreRef",
        "value": "mysecret",
      }
    },
  ]
}
```
下面是有关如何在 YAML 文件中声明网格机密资源的示例：
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="declare-mesh-secretsvalues-resources"></a>声明网格机密/值资源
网格机密/值资源依赖于在前面的步骤中定义的网格机密资源。

关于 "resources" 部分中 "value:" 与 "name:" 字段之间的关系：由冒号分隔的 "name:" 字符串的第二部分是用于机密的版本号，冒号之前的名称需要与它依赖的网格机密值匹配。 例如，对于元素 ```name: mysecret:1.0```，版本号是 1.0，并且名称 ```mysecret``` 必须与前面定义的 ```"value": "mysecret"``` 匹配。

>
下面是有关如何在 JSON 文件中声明网格机密/值资源的示例：

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "eastus",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "my-secret-value-v1": {
      "type": "string",
      "metadata": {
        "description": "My Mesh Application Secret Value."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MySecret.txt",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]",
      "properties": {
        "kind": "inlinedValue",
        "description": "My Mesh Application Secret",
        "contentType": "SecretsStoreRef",
        "value": "mysecret",
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "mysecret:1.0",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        'Microsoft.ServiceFabricMesh/secrets/MySecret.txt'
      ],
      "properties": {
        "value": "[parameters('my-secret-value-v1)]"
      }
    }
  ]
}
```
下面是有关如何在 YAML 文件中声明网格机密/值资源的示例：
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          Secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
            - name: mysecret:1.0
            description: My Mesh Application Secret Value
            secret_type: value
            content_type: text/plain
            value: "P@ssw0rd#1234"
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="modify-mesh-application-to-reference-mesh-secret-values"></a>修改网格应用程序以引用网格机密值
要使用 Secure Store Service 机密值，Service Fabric 网格应用程序需要知道以下两个字符串：
1. Micrsoft.ServiceFabricMesh/Secrets.name 包含文件的名称，并且将包含明文形式的机密值。
2. Windows 或 Linux 环境变量“Fabric_SettingPath”包含可以从中访问 Secure Store Service 机密值所在文件的目录路径。 对于 Windows 承载的网格应用程序，这是“C:\Settings”；对于 Linux 承载的网格应用程序，这是“/var/settings”。

## <a name="deploy-or-use-a-rolling-upgrade-for-mesh-application-to-consume-secret-values"></a>部署或滚动升级网格应用程序以使用机密值
创建机密和/或带有版本的机密/值仅限于资源模型声明的部署。 创建这些资源的唯一方法是使用 **az mesh deployment** 命令传递资源模型 JSON 或 YAML 文件，如下所示：

```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```

## <a name="azure-cli-commands-for-secure-store-service-lifecycle-management"></a>用于管理 Secure Store Service 生命周期的 Azure CLI 命令

### <a name="create-a-new-secrets-resource"></a>创建新的机密资源
```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```
传递 **template-file** 或 **template-uri**（但不能同时传递两者）。

例如：
- az mesh deployment create --c:\MyMeshTemplates\SecretTemplate1.txt
- az mesh deployment create -- https://www.fabrikam.com/MyMeshTemplates/SecretTemplate1.txt

### <a name="show-a-secret"></a>显示机密
返回机密的说明（但不是值）。
```azurecli-interactive
az mesh secret show --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="delete-a-secret"></a>删除机密

- 当机密正在由某个网格应用程序引用时，无法删除机密。
- 删除机密资源会删除所有机密/资源版本。
```azurecli-interactive
az mesh secret delete --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="list-secrets-in-subscription"></a>列出订阅中的机密
```azurecli-interactive
az mesh secret list
```
### <a name="list-secrets-in-resource-group"></a>列出资源组中的机密
```azurecli-interactive
az mesh secret list -g <myResourceGroup>
```
### <a name="list-all-versions-of-a-secret"></a>列出某个机密的所有版本
```azurecli-interactive
az mesh secretvalue list --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="show-secret-version-value"></a>显示机密版本值
```azurecli-interactive
az mesh secretvalue show --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

### <a name="delete-secret-version-value"></a>删除机密版本值
```azurecli-interactive
az mesh secretvalue delete --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

## <a name="next-steps"></a>后续步骤 
若要详细了解 Service Fabric 网格，请阅读概述：
- [Service Fabric 网格概述](service-fabric-mesh-overview.md)
