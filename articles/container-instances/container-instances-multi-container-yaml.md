---
title: 使用 Azure CLI 和 YAML 在 Azure 容器实例中部署多容器组
description: 了解如何使用 Azure CLI 和 YAML 文件在 Azure 容器实例中部署包含多个容器的容器组。
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: marsma
ms.openlocfilehash: 5dfee15e978d2dba0f50d1dc4b78953698389950
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34851075"
---
# <a name="deploy-a-multi-container-container-group-with-yaml"></a>使用 YAML 部署多容器容器组

Azure 容器实例支持使用[容器组](container-instances-container-groups.md)将多个容器部署到单台主机上。 当生成应用程序 sidecar 以用于日志记录、监视或用于某些其他配置（其中服务需要第二个附加进程）时，多容器容器组很有用。

使用 Azure CLI 部署多容器组的方法有以下两种：

* YAML 文件部署（本文）
* [资源管理器模板部署](container-instances-multi-container-group.md)

由于 YAML 格式更简洁，因此，当部署仅包括容器实例时，建议使用 YAML 文件进行部署。 如果需要在部署容器实例时部署其他 Azure 服务资源（例如，Azure 文件共享），建议采用资源管理器模板部署。

> [!NOTE]
> 多容器组当前仅限于 Linux 容器。 我们正致力于为 Windows 容器提供全部功能，你可在 [Azure 容器实例的配额和区域可用性](container-instances-quotas.md)中了解当前的平台差异。

## <a name="configure-the-yaml-file"></a>配置 YAML 文件

若要在 Azure CLI 中使用 [az container create][az-container-create] 命令部署多容器容器组，必须在 YAML 文件中指定容器组配置，然后将该 YAML 文件作为参数传递给命令。

首先将以下 YAML 复制到一个名为 **deploy-aci.yaml** 的新文件中。

该 YAML 文件定义了一个容器组，其中包含两个容器、一个公用 IP 地址和两个公开的端口。 该组中的第一个容器运行面向 Internet 的 Web 应用程序。 第二个容器 sidecar 定期通过容器组的本地网络向在第一个容器中运行的 Web 应用程序发出 HTTP 请求。

```YAML
apiVersion: 2018-06-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: microsoft/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: microsoft/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
    - protocol: tcp
      port: '8080'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

## <a name="deploy-the-container-group"></a>部署容器组

使用 [az group create][az-group-create] 命令创建资源组：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az container create][az-container-create] 命令部署容器组并传递 YAML 文件作为参数：

```azurecli-interactive
az container create --resource-group myResourceGroup --name myContainerGroup -f deploy-aci.yaml
```

将在几秒钟内收到来自 Azure 的初始响应。

## <a name="view-deployment-state"></a>查看部署状态

若要查看部署状态，请运行下面的 [az container show][az-container-show] 命令：

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

若要查看正在运行的应用程序，请在浏览器中转到它的 IP 地址。 例如，在此示例输出中，IP 为 `52.168.26.124`：

```bash
Name              ResourceGroup    ProvisioningState    Image                                                           IP:ports               CPU/Memory       OsType    Location
----------------  ---------------  -------------------  --------------------------------------------------------------  ---------------------  ---------------  --------  ----------
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-logs"></a>查看日志

使用 [az container logs][az-container-logs] 命令查看容器的日志输出。 `--container-name` 参数指定从中拉取日志的容器。 在此示例中，指定第一个容器。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

输出：

```console
listening on port 80
::1 - - [09/Jan/2018:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

若要查看 side-car 容器的日志，请运行指定第二个容器名的相同命令。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

输出：

```console
Every 3s: curl -I http://localhost                          2018-01-09 23:25:11

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Tue, 09 Jan 2018 23:25:11 GMT
Connection: keep-alive
```

如你所见，sidecar 通过该组的本地网络定期向主 Web 应用程序发出 HTTP 请求，确保其正在运行。 如果此 sidecar 示例收到的 HTTP 响应代码不是 200 OK，则可将其扩展以触发警报。

## <a name="deploy-from-private-registry"></a>从专用注册表部署

若要使用专用容器映像注册表，请包括具有针对你的环境修改的值的以下 YAML：

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

例如，以下 YAML 部署一个容器组，其中包含单个容器，该容器的映像是从名为“myregistry”的专用 Azure 容器注册表中拉取的：

```YAML
apiVersion: 2018-06-01
location: eastus
name: myContainerGroup2
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: myregistry.azurecr.io/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
  imageRegistryCredentials:
  - server: myregistry.azurecr.io
    username: myregistry
    password: REGISTRY_PASSWORD
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

## <a name="export-container-group-to-yaml"></a>将容器组导出到 YAML

可以使用 Azure CLI 命令 [az container export][az-container-export] 将现有容器组的配置导出到 YAML 文件。

导出允许在“配置即代码”的版本控制中存储容器组配置，对于保留容器组的配置很有用。 还可以将导出的文件用作使用 YAML 开发新配置时的起点。

通过发出以下 [az container export][az-container-export] 命令导出之前创建的容器组的配置：

```azurecli-interactive
az container export --resource-group rg604 --name myContainerGroup --file deployed-aci.yaml
```

如果命令成功，则不会显示输出，但可以查看文件的内容来查看结果。 例如，通过 `head` 查看前几行：

```console
$ head deployed-aci.yaml
apiVersion: 2018-02-01-preview
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: microsoft/aci-helloworld:latest
      ports:
```

> [!NOTE]
> 从 Azure CLI 版本 2.0.34 开始，有一个[已知问题][cli-issue-6525]：导出的容器组指定了一个较旧的 API 版本 **2018-02-01-preview**（参见前面的 JSON 输出示例）。 如果要使用导出的 YAML 文件重新部署，可以放心地将导出的 YAML 文件中的 `apiVersion` 值更新为 **2018-06-01**。

## <a name="next-steps"></a>后续步骤

本文档介绍部署多容器 Azure 容器实例所需的步骤。 若要从头到尾体验 Azure 容器实例，包括使用专用的 Azure 容器注册表，请参阅 Azure 容器实例教程。

> [!div class="nextstepaction"]
> [Azure 容器实例教程][aci-tutorial]

<!-- LINKS - External -->
[cli-issue-6525]: https://github.com/Azure/azure-cli/issues/6525

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-export]: /cli/azure/container#az-container-export
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
