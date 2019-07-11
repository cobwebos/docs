---
title: 容器支持
titleSuffix: Azure Cognitive Services
description: 了解如何从 Azure CLI 创建 Azure 容器实例资源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 5e7a3d849f726ae4dbbd559d541464404e427775
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717070"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>从 Azure CLI 创建 Azure 容器实例资源

以下 YAML 定义 Azure 容器实例资源。 复制并将内容粘贴到新文件，名为`my-aci.yaml`和注释的值将替换为自己。 请参阅 [模板 format] [模板共振峰] 的有效 YAML。 请参阅[容器存储库和映像][repositories-and-images]可用映像名称和其相应的存储库。

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials:
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
> 并非所有位置都有相同的 CPU 和内存可用性。 请参阅[位置和资源][location-to-resource]每个位置和 OS 的容器的可用资源的列表中的表。

我们将依赖于为我们创建的 YAML 文件[ `az container create` ][azure-container-create]命令。 从 Azure CLI 中，执行`az container create`命令替换`<resource-group>`用您自己。 此外，有关保护 YAML 中的值的部署请参阅[保护值][secure-values]。

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

该命令的输出是`Running...`如果有效，一段时间后输出更改为一个表示新创建的 ACI 资源的 JSON 字符串。 容器映像的多个可能不可用的一段时间，但现已部署资源。

> [!TIP]
> 请特别注意到公共预览版 Azure 认知服务产品/服务的位置根据 YAML 将需要相应地调整以匹配位置。

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format

[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values