---
title: 在 Service Fabric 网格应用程序中使用基于 Azure 文件的卷 | Microsoft Docs
description: 了解如何使用 Azure CLI，通过将基于 Azure 文件的卷装载到服务，在 Azure Service Fabric 网格应用程序中存储状态。
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 9f21ad737fdcd0bcdc77394096308e47a4fb5a00
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371122"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>在 Service Fabric 网格应用程序中装载基于 Azure 文件的卷 

本文介绍了如何将基于 Azure 文件的卷装载到 Service Fabric 网格应用程序的服务中。  Azure 文件卷驱动程序是将 Azure 文件共享装载到容器的 Docker 卷驱动程序，可使用它来保存服务状态。 卷提供常规用途文件存储，并允许使用正常磁盘 I/O 文件 API 读取/写入文件。  有关卷和应用程序数据存储选项的详细信息，请参阅[存储状态](service-fabric-mesh-storing-state.md)。

若要将卷装载到服务，需在 Service Fabric 网格应用程序中创建卷资源，然后在服务中引用该卷。  可在[基于 YAML 的资源文件](#declare-a-volume-resource-and-update-the-service-resource-yaml)或[基于 JSON 的部署模板](#declare-a-volume-resource-and-update-the-service-resource-json)中完成声明该卷资源并在服务资源中引用它。 必须先创建 Azure 存储帐户和 [Azure 文件共享](/azure/storage/files/storage-how-to-create-file-share)，然后才能装载此卷。

## <a name="prerequisites"></a>系统必备
> [!NOTE]
> **Windows RS5 开发计算机上的部署的已知问题:** RS5 Windows 计算机上的 Powershell cmdlet SmbGlobalMapping 的公开 bug 阻止装载 Azurefile 卷。 下面是在本地开发计算机上装载基于 AzureFile 的卷时遇到的示例错误。
```
Error event: SourceId='System.Hosting', Property='CodePackageActivation:counterService:EntryPoint:131884291000691067'.
There was an error during CodePackage activation.System.Fabric.FabricException (-2147017731)
Failed to start Container. ContainerName=sf-2-63fc668f-362d-4220-873d-85abaaacc83e_6d6879cf-dd43-4092-887d-17d23ed9cc78, ApplicationId=SingleInstance_0_App2, ApplicationName=fabric:/counterApp. DockerRequest returned StatusCode=InternalServerError with ResponseBody={"message":"error while mounting volume '': mount failed"}
```
此问题的解决方法是: 1) 以 Powershell 管理员身份运行以下命令, 2) 重新启动计算机。
```powershell
PS C:\WINDOWS\system32> Mofcomp c:\windows\system32\wbem\smbwmiv2.mof
```

可以使用 Azure Cloud Shell 或 Azure CLI 的本地安装完成本文的内容。 

若要在本文中使用本地 Azure CLI，请确保 `az --version` 至少返回 `azure-cli (2.0.43)`。  遵照这些[说明](service-fabric-mesh-howto-setup-cli.md)安装（或更新）Azure Service Fabric 网格 CLI 扩展模块。

登录到 Azure 并设置订阅：

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>创建存储帐户和文件共享（可选）
装载 Azure 文件卷需要具备存储帐户和文件共享。  可以使用现有的 Azure 存储帐户和文件共享，也可以创建资源：

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>获取存储帐户名称和密钥，以及文件共享名称
在以下部分中，存储帐户名称、存储帐户密钥和文件共享名称引用为 `<storageAccountName>`、`<storageAccountKey>` 和 `<fileShareName>`。 

列出存储帐户，并获取想要使用的文件共享的存储帐户名称：
```azurecli-interactive
az storage account list
```

获取文件共享的名称：
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

获取存储帐户密钥（“密钥 1”）：
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

也可以在 [Azure 门户](https://portal.azure.com)中找到这些值：
* `<storageAccountName>` - 位于“存储帐户”下，它是在创建文件共享时使用的存储帐户的名称。
* `<storageAccountKey>` - 在“存储帐户”下选择自己的存储帐户，然后选择“访问密钥”并使用“密钥 1”下面的值。
* `<fileShareName>` - 在“存储帐户”下选择自己的存储帐户，然后选择“文件”。 要使用的名称是创建的文件共享的名称。

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>声明卷资源并更新服务资源 (JSON)

添加在上一步中找到的 `<fileShareName>`、`<storageAccountName>` 和 `<storageAccountKey>` 值的参数。 

创建卷资源，以作为应用程序资源的对等。 指定名称和提供程序（“SFAzureFile”，以使用基于 Azure 文件的卷）。 在 `azureFileParameters` 中，指定在上一步中找到的 `<fileShareName>`、`<storageAccountName>` 和 `<storageAccountKey>` 值的参数。

将 `volumeRefs` 添加到服务的 `codePackages` 元素中，以将卷装载到服务中。  `name` 是卷的资源 ID（或卷资源的部署模板参数）及在 volume.yaml 资源文件中声明的卷的名称。  `destinationPath` 是卷要装载到的本地目录。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "defaultValue": "EastUS",
      "type": "String",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "fileShareName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure Files file share that provides the volume for the container."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure storage account that contains the file share."
      }
    },
    "storageAccountKey": {
      "type": "securestring",
      "metadata": {
        "description": "Access key for the Azure storage account that contains the file share."
      }
    },
    "stateFolderName": {
      "type": "string",
      "defaultValue": "TestVolumeData",
      "metadata": {
        "description": "Folder in which to store the state. Provide an empty value to create a unique folder for each container to store the state. A non-empty value will retain the state across deployments, however if more than one applications are using the same folder, the counter may update more frequently."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "VolumeTest",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/VolumeTestNetwork",
        "Microsoft.ServiceFabricMesh/volumes/testVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "VolumeTestService",
            "properties": {
              "description": "VolumeTestService description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "VolumeTestService",
                  "image": "volumetestservice:dev",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]",
                      "destinationPath": "C:\\app\\data"
                    }
                  ],
                  "environmentVariables": [
                    {
                      "name": "ASPNETCORE_URLS",
                      "value": "http://+:20003"
                    },
                    {
                      "name": "STATE_FOLDER_NAME",
                      "value": "[parameters('stateFolderName')]"
                    }
                  ],
                  ...
                }
              ],
              ...
            }
          }
        ],
        "description": "VolumeTest description."
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "testVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for the test application.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
    ...
  ]
}
```

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>声明卷资源并更新服务资源 (YAML)

在应用程序的“应用资源”目录中添加新的 volume.yaml 文件。  指定名称和提供程序（“SFAzureFile”，以使用基于 Azure 文件的卷）。 `<fileShareName>`、`<storageAccountName>` 和 `<storageAccountKey>` 是在上一步中找到的值。

```yaml
volume:
  schemaVersion: 1.0.0-preview2
  name: testVolume
  properties:
    description: Azure Files storage volume for counter App.
    provider: SFAzureFile
    azureFileParameters: 
        shareName: <fileShareName>
        accountName: <storageAccountName>
        accountKey: <storageAccountKey>
```

更新“服务资源”目录中的 service.yaml 文件，以将卷装载到服务中。  将 `volumeRefs` 元素添加到 `codePackages` 元素。  `name` 是卷的资源 ID（或卷资源的部署模板参数）及在 volume.yaml 资源文件中声明的卷的名称。  `destinationPath` 是卷要装载到的本地目录。

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: VolumeTest
  properties:
    services:
      - name: VolumeTestService
        properties:
          description: VolumeTestService description.
          osType: Windows
          codePackages:
            - name: VolumeTestService
              image: volumetestservice:dev
              volumeRefs:
                - name: "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]"
                  destinationPath: C:\app\data
              endpoints:
                - name: VolumeTestServiceListener
                  port: 20003
              environmentVariables:
                - name: ASPNETCORE_URLS
                  value: http://+:20003
                - name: STATE_FOLDER_NAME
                  value: TestVolumeData
              resources:
                requests:
                  cpu: 0.5
                  memoryInGB: 1
          replicaCount: 1
          networkRefs:
            - name: VolumeTestNetwork
```

## <a name="next-steps"></a>后续步骤

- 在 [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) 上查看 Azure 文件卷示例应用程序。
- 要详细了解 Service Fabric 资源模型，请参阅 [Service Fabric 网格资源模型](service-fabric-mesh-service-fabric-resources.md)。
- 若要详细了解 Service Fabric 网格，请阅读 [Service Fabric 网格概述](service-fabric-mesh-overview.md)。
