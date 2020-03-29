---
title: 容器支持
titleSuffix: Azure Cognitive Services
description: 了解如何在 Azure CLI 中创建 Azure 容器实例资源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/06/2020
ms.author: dapine
ms.openlocfilehash: 700a04b58c13a9c7fd5301875226ca234cabeb96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "75689467"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>在 Azure CLI 中创建 Azure 容器实例资源

下面的 YAML 定义Azure 容器实例资源。 将内容复制并粘贴到名为 `my-aci.yaml` 的新文件中，并将注释的值替换为自己的值。 请参阅[模板格式][template-format]以获取有效的 YAML。 请参阅[容器存储库和映像][repositories-and-images]，获取可用的映像名称及其相应的存储库。 有关容器实例的 YAML 引用的详细信息，请参阅[YAML 引用：Azure 容器实例][aci-yaml-ref]。

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials: # This is required when pulling a non-public image
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
properties:
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  volumes: # This node, is only required for container instances that pull their model in at runtime, such as LUIS.
  - name: aci-file-share
    azureFile:
      shareName: # < File share name >
      storageAccountName: # < Storage account name>
      storageAccountKey: # < Storage account key >
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> 并非所有位置都具有相同的 CPU 和内存可用性。 有关每个位置和操作系统的容器可用资源的列表，请参阅[位置和资源][location-to-resource]表。

我们将依赖于我们为[`az container create`][azure-container-create]命令创建的 YAML 文件。 从 Azure CLI 中`az container create`，执行`<resource-group>`用您自己的 替换 的命令。 此外，为了在 YAML 部署中保护值，请参阅[安全值][secure-values]。

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

如果命令有效，则命令的输出为 `Running...`。过一段时间后，输出更改为表示新建 ACI 资源的 JSON 字符串。 容器映像很可能会在一段时间内不可用，但现在已部署资源。

> [!TIP]
> 请密切注意公共预览版 Azure 认知服务产品的位置，因为需要根据位置来相应地调整 YAML。

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
