---
title: 在 Azure 容器实例中装载 Azure 文件卷
description: 了解如何装载 Azure 文件卷以保持 Azure 容器实例的状态
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 07/08/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 25cac6a66baeb1587e4b5ba3f0923ca9c4394706
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "68325489"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>在 Azure 容器实例中装载 Azure 文件共享

默认情况下，Azure 容器实例是无状态的。 如果容器崩溃或停止，其所有状态都会丢失。 若要将状态保持至超过容器寿命，必须从外部存储装载卷。 本文介绍了如何装载使用 [Azure 文件](../storage/files/storage-files-introduction.md)创建的 Azure 文件共享，以便与 Azure 容器实例配合使用。 Azure 文件在云端提供完全托管的文件共享，这些共享项可通过行业标准的服务器消息块 (SMB) 协议进行访问。 将 Azure 文件共享与 Azure 容器实例配合使用可以提供文件共享功能，类似于将 Azure 文件共享与 Azure 虚拟机配合使用。

> [!NOTE]
> 当前只有 Linux 容器能装载 Azure 文件共享。 尽管我们正在努力将所有功能带入 Windows 容器, 但你可以在 "[概述](container-instances-overview.md#linux-and-windows-containers)" 中找到当前的平台差异。

## <a name="create-an-azure-file-share"></a>创建 Azure 文件共享

必须先创建 Azure 文件共享，才能将其用于 Azure 容器实例。 运行以下脚本来创建存储帐户，以托管文件共享和共享本身。 存储帐户名必须是全局唯一的，因此该脚本会向基础字符串添加一个随机值。

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create --name $ACI_PERS_SHARE_NAME --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>获取存储凭据

若要在 Azure 容器实例中将 Azure 文件共享装载为卷，需要 3 个值：存储帐户名、共享名和存储访问密钥。

如果使用了以上脚本，则存储帐户名称存储在 $ACI_PERS_STORAGE_ACCOUNT_NAME 变量中。 若要查看帐户名称，请键入以下命令：

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

共享名已知（在上述脚本中定义为 acishare），因此剩下的就是找到存储帐户密钥，可使用以下命令：

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume---cli"></a>部署容器和装入卷-CLI

若要使用 Azure CLI 将 Azure 文件共享装载为容器中的卷, 请在使用[az container create][az-container-create]创建容器时指定共享和卷装入点。 如果已执行前面的步骤，则可以使用以下命令装载先前创建的共享以创建容器：

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

此`--dns-name-label`值在创建容器实例的 Azure 区域内必须是唯一的。 如果在执行命令时收到 DNS 名称标签错误消息，请更新前一命令中的值。

## <a name="manage-files-in-mounted-volume"></a>管理已装载卷中的文件

启动容器后, 可以使用通过 Microsoft [aci-hellofiles][aci-hellofiles]映像部署的简单 web 应用, 在 Azure 文件共享中创建指定位置的小型文本文件。 用[az container show][az-container-show]命令获取 web 应用的完全限定的域名 (FQDN):

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn --output tsv
```

使用应用保存文本后, 可以使用[Azure 门户][portal]或类似于[Microsoft Azure 存储资源管理器][storage-explorer]的工具来检索和检查写入到文件共享中的文件。

## <a name="deploy-container-and-mount-volume---yaml"></a>部署容器和装入卷-YAML

还可以部署容器组, 并使用 Azure CLI 和[YAML 模板](container-instances-multi-container-yaml.md)在容器中装载卷。 在部署由多个容器组成的容器组时，通过 YAML 模板进行部署是首选方法。

以下 YAML 模板定义容器组, 其中一个容器使用`aci-hellofiles`映像创建。 容器将以前创建的 Azure 文件共享*acishare*装载为卷。 如果指示, 请输入托管文件共享的存储帐户的名称和存储密钥。 

与在 CLI 示例中一样, `dnsNameLabel`此值在创建容器实例的 Azure 区域内必须是唯一的。 如果需要, 请更新 YAML 文件中的值。

```yaml
apiVersion: '2018-10-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

若要使用 YAML 模板进行部署, 请将前面的 YAML 保存到`deploy-aci.yaml`名为的文件, 然后使用`--file`参数执行[az container create][az-container-create]命令:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>部署容器并装入卷-资源管理器

除了 CLI 和 YAML 部署, 还可以使用 Azure[资源管理器模板](/azure/templates/microsoft.containerinstance/containergroups)部署容器组并在容器中装载卷。

首先，在模板的容器组 `properties` 节中填充 `volumes` 数组。 

然后, 对于要在其中装入卷的每个容器, `volumeMounts` `properties`在容器定义的节中填充数组。

以下资源管理器模板定义容器组, 其中一个容器使用`aci-hellofiles`映像创建。 容器将以前创建的 Azure 文件共享*acishare*装载为卷。 如果指示, 请输入托管文件共享的存储帐户的名称和存储密钥。 

如前面的示例所示, `dnsNameLabel`此值在创建容器实例的 Azure 区域中必须是唯一的。 如果需要, 请更新模板中的值。

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

若要使用资源管理器模板进行部署, 请将前面的 JSON 保存到`deploy-aci.json`名为的文件, 然后使用`--template-file`参数执行[az group deployment create][az-group-deployment-create]命令:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>装载多个卷

若要将多个卷装载到容器实例中，必须使用 [Azure 资源管理器模板](/azure/templates/microsoft.containerinstance/containergroups)或 YAML 文件进行部署。 若要使用模板或 YAML 文件, 请`volumes` `properties`在模板的节中填充数组, 提供共享详细信息并定义卷。 

例如, 如果在存储帐户*myStorageAccount*中创建了两个名为*share1*和*share2*的 Azure 文件`volumes`共享, 资源管理器模板中的数组将如下所示:

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

接下来，针对容器组中希望装载卷的每个容器，在容器定义的 `properties` 部分填充 `volumeMounts` 数组。 例如，填充以下内容将装载之前定义的两个卷：myvolume1 和 myvolume2：

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

## <a name="next-steps"></a>后续步骤

了解如何在 Azure 容器实例中装载其他卷类型：

* [在 Azure 容器实例中装载 emptyDir 卷](container-instances-volume-emptydir.md)
* [在 Azure 容器实例中装载 gitRepo 卷](container-instances-volume-gitrepo.md)
* [在 Azure 容器实例中装载机密卷](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create