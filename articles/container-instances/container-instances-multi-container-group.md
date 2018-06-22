---
title: 在 Azure 容器实例中部署多容器组
description: 了解如何在 Azure 容器实例中部署包含多个容器的容器组。
services: container-instances
author: neilpeterson
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: db3f616d85c21f01c751fd82532289593a6e7e45
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850563"
---
# <a name="deploy-a-container-group"></a>部署容器组

Azure 容器实例支持使用[容器组](container-instances-container-groups.md)将多个容器部署到单台主机上。 当生成应用程序 sidecar 以用于日志记录、监视或用于某些其他配置（其中服务需要第二个附加进程）时，这种方法很有用。

使用 Azure CLI 部署多容器组的方法有以下两种：

* 资源管理器模板部署（本文）
* [YAML 文件部署](container-instances-multi-container-yaml.md)

如果需要在部署容器实例时部署其他 Azure 服务资源（例如，共享 Azure 文件），建议采用资源管理器模板部署方法。 由于 YAML 格式更简洁，因此建议在仅部署容器实例时，采用 YAML 文件部署方法。

> [!NOTE]
> 多容器组当前仅限于 Linux 容器。 我们正致力于为 Windows 容器提供全部功能，你可在 [Azure 容器实例的配额和区域可用性](container-instances-quotas.md)中了解当前的平台差异。

## <a name="configure-the-template"></a>配置模板

本文中的各部分逐步介绍了如何通过部署 Azure 资源管理器模板来运行简单的多容器 sidecar 配置。

首先，创建名为 `azuredeploy.json` 的文件，再将以下 JSON 复制到其中。

此资源管理器模板定义的容器组包含两个容器、一个公共 IP 地址和两个公开端口。 该组中的第一个容器运行面向 Internet 的应用程序。 第二个容器 sidecar 通过该组的本地网络向主要 Web 应用程序发出 HTTP 请求。

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "microsoft/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "microsoft/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-04-01",
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
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
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
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

若要使用专用容器映像注册表，请采用以下格式向 JSON 文档添加对象。 有关此配置的示例实现，请参阅 [ACI 资源管理器模板引用][template-reference]文档。

```JSON
"imageRegistryCredentials": [
  {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
  }
]
```

## <a name="deploy-the-template"></a>部署模板

使用 [az group create][az-group-create] 命令创建资源组。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az group deployment create][az-group-deployment-create] 命令部署模板。

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
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
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     westus
```

## <a name="view-logs"></a>查看日志

使用 [az container logs][az-container-logs] 命令查看容器的日志输出。 `--container-name` 参数指定从中拉取日志的容器。 在此示例中，指定第一个容器。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

输出：

```bash
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

```bash
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

## <a name="next-steps"></a>后续步骤

本文档介绍部署多容器 Azure 容器实例所需的步骤。 若要从头到尾体验 Azure 容器实例，请参阅 Azure 容器实例教程。

> [!div class="nextstepaction"]
> [Azure 容器实例教程][aci-tutorial]

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-group-deployment-create]: /cli/azure/group/deployment#az_group_deployment_create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
