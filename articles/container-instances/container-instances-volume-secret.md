---
title: 在 Azure 容器实例中装载机密卷
description: 了解如何装载机密卷以存储供容器实例访问的敏感信息
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 07/19/2018
ms.author: danlep
ms.openlocfilehash: 2be640c8c7773ebd1fb5c83e67e3f0762d011e85
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657576"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>在 Azure 容器实例中装载机密卷

可以使用机密  卷向容器组中的容器提供敏感信息。 机密  卷将机密存储在该卷内的文件中，然后容器组中的容器可以访问这些机密。 将机密存储在机密  卷中，可以避免将敏感数据（例如，SSH 密钥或数据库凭据）添加到应用程序代码中。

所有*机密*卷受[tmpfs][tmpfs]，支持 RAM 的文件系统; 其内容永远不会写入到非易失性存储。

> [!NOTE]
> 机密  卷目前仅限于 Linux 容器。 [设置环境变量](container-instances-environment-variables.md)介绍了如何为 Windows 和 Linux 容器传递安全环境变量。 我们致力于为 Windows 容器提供的所有功能，可以找到在当前的平台差异[概述](container-instances-overview.md#linux-and-windows-containers)。

## <a name="mount-secret-volume---azure-cli"></a>装载机密卷 - Azure CLI

若要使用 Azure CLI 部署具有一个或多个机密的容器，包括`--secrets`并`--secrets-mount-path`中的参数[az 容器创建][az-container-create]命令。 此示例在 `/mnt/secrets` 处装载一个机密  卷，其中包含两个机密 “mysecret1”和“mysecret2”：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

以下[az 容器 exec][az-container-exec]输出显示在正在运行的容器中打开一个外壳，列出的文件中的机密的卷，然后显示其内容：

```console
$ az container exec --resource-group myResourceGroup --name secret-volume-demo --exec-command "/bin/sh"
/usr/src/app # ls -1 /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>装载机密卷 - YAML

还可以使用 Azure CLI 和 [YAML 模板](container-instances-multi-container-yaml.md)部署容器组。 在部署由多个容器组成的容器组时，通过 YAML 模板进行部署是首选方法。

使用 YAML 模板进行部署时，模板中的机密值必须已进行 **Base64 编码**。 但是，机密值会以明文形式显示在容器的文件中。

以下 YAML 模板定义了一个容器组，其中包含一个容器，该容器在 `/mnt/secrets` 处装载了一个机密  卷。 机密卷有两个机密：“mysecret1”和“mysecret2”。

```yaml
apiVersion: '2018-10-01'
location: eastus
name: secret-volume-demo
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /mnt/secrets
        name: secretvolume1
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: secretvolume1
    secret:
      mysecret1: TXkgZmlyc3Qgc2VjcmV0IEZPTwo=
      mysecret2: TXkgc2Vjb25kIHNlY3JldCBCQVIK
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

若要部署使用 YAML 模板，请将上述 YAML 保存到名为的文件`deploy-aci.yaml`，然后执行[az 容器创建][az-container-create]命令`--file`参数：

```azurecli-interactive
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>装载机密卷 - 资源管理器

除了 CLI 和 YAML 部署外，还可以使用 Azure [资源管理器模板](/azure/templates/microsoft.containerinstance/containergroups)部署容器组。

首先，在模板的容器组 `properties` 节中填充 `volumes` 数组。 使用资源管理器模板进行部署时，模板中的机密值必须已进行 **Base64 编码**。 但是，机密值会以明文形式显示在容器的文件中。

接下来，针对容器组中希望装载*机密*卷的每个容器，在容器定义的 `properties` 节中填充 `volumeMounts` 数组。

以下资源管理器模板定义了一个容器组，其中包含一个容器，该容器在 `/mnt/secrets` 处装载了一个机密  卷。 机密卷有两个机密：“mysecret1”和“mysecret2”。

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

若要使用资源管理器模板部署，请将前面的 JSON 保存到名为的文件`deploy-aci.json`，然后执行[az 组部署创建][az-group-deployment-create]命令`--template-file`参数：

```azurecli-interactive
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```

## <a name="next-steps"></a>后续步骤

### <a name="volumes"></a>卷

了解如何在 Azure 容器实例中装载其他卷类型：

* [在 Azure 容器实例中装载 Azure 文件共享](container-instances-volume-azure-files.md)
* [在 Azure 容器实例中装载 emptyDir 卷](container-instances-volume-emptydir.md)
* [在 Azure 容器实例中装载 gitRepo 卷](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>安全环境变量

向容器（包括 Windows 容器）提供敏感信息的另一种方法是通过使用[安全环境变量](container-instances-environment-variables.md#secure-values)。

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
