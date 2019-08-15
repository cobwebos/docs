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
ms.openlocfilehash: 2080d283c6cb7466dcb4847a81d76a4c3109217a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012054"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>从 Azure CLI 创建 Azure 容器实例资源

下面的 YAML 定义 Azure 容器实例资源。 将内容复制并粘贴到名为`my-aci.yaml`的新文件中, 并用自己的值替换注释值。 有关有效的 YAML, 请参阅[模板格式][template-format]。 有关可用映像名称及其相应的存储库, 请参阅[容器存储库和映像][repositories-and-images]。

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
> 并非所有位置都具有相同的 CPU 和内存可用性。 有关每个位置和 OS 的容器可用资源列表, 请参阅 "[位置和资源][location-to-resource]" 表。

我们将依赖于为[`az container create`][azure-container-create]命令创建的 YAML 文件。 在 Azure CLI 中, 执行`az container create`命令, 将替换为你自己的。 `<resource-group>` 此外, 为了保护 YAML 部署中的值, 请参阅[安全值][secure-values]。

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

如果在某些情况下输出`Running...`更改为表示新创建的 ACI 资源的 JSON 字符串, 则该命令的输出为有效。 容器映像不太可能在一段时间内不可用, 但现在已部署资源。

> [!TIP]
> 请密切注意公共预览版 Azure 认知服务产品的位置, 因为需要相应地调整 YAML 来匹配位置。

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format

[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
