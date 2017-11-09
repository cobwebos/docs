---
title: "将 ACR 与 Azure DC/OS 群集配合使用 | Microsoft Docs"
description: "在 Azure 容器服务中将 Azure 容器注册表与 DC/OS 群集配合使用"
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service, acr, azure-container-registry
keywords: "Docker, 容器, 微服务, Mesos, Azure, 文件共享, cifs"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: 36e57bb6ebf9f55d42c526a361fed33b4238b313
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2017
---
# <a name="use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>将 ACR 与 DC/OS 群集配合使用，部署应用程序

在本文中，我们将探讨如何将 Azure 容器注册表与 DC/OS 群集配合使用。 使用 ACR 可私下存储和管理容器映像。 本教程涵盖以下任务：

> [!div class="checklist"]
> * 部署 Azure 容器注册表（必要时）
> * 在 DC/OS 群集上配置 ACR 身份验证
> * 将映像上传到 Azure 容器注册表
> * 从 Azure 容器注册表运行容器映像

需要 ACS DC/OS 群集来完成本教程中的步骤。 必要时，[此脚本示例](./../kubernetes/scripts/container-service-cli-deploy-dcos.md)可为你创建一个。

本教程需要 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-azure-container-registry"></a>部署 Azure 容器注册表

必要时，请使用 [az acr create](/cli/azure/acr#create) 命令创建 Azure 容器注册表。 

以下示例将创建具有随机生成名称的注册表。 然后还使用 `--admin-enabled` 参数为该注册表配置了管理员帐户。

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry$RANDOM --sku Basic
```

创建注册表后，Azure CLI 输出与下方类似的数据。 请记下 `name` 和 `loginServer`，后续步骤中将使用这些参数。

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T03:40:56.511597+00:00",
  "id": "/subscriptions/f2799821-a08a-434e-9128-454ec4348b10/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry23489",
  "location": "eastus",
  "loginServer": "mycontainerregistry23489.azurecr.io",
  "name": "myContainerRegistry23489",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr034017"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

使用 [az acr credential show](/cli/azure/acr/credential) 命令获取容器注册表凭据。 将 `--name` 替换为上一步中记下的参数。 记下一个密码，后续步骤中可能需要使用。

```azurecli-interactive
az acr credential show --name myContainerRegistry23489
```

有关 Azure 容器注册表的详细信息，请参阅[私有 Docker 容器注册表简介](../../container-registry/container-registry-intro.md)。 

## <a name="manage-acr-authentication"></a>管理 ACR 身份验证

推送映像和从专用注册表请求映像的传统方法是，首先使用注册表进行身份验证。 为此，需要对访问专用注册表的任何客户端使用 `docker login` 命令。 因为 DC/OS 群集可包含许多节点，所有节点均需使用 ACR 进行身份验证，因此在每个节点自动执行此过程就十分有用。 

### <a name="create-shared-storage"></a>创建共享存储

此过程使用已在群集中每个节点上装载的 Azure 文件共享。 如果尚未设置共享存储，请参阅[在 DC/OS 群集内设置文件共享](container-service-dcos-fileshare.md)。

### <a name="configure-acr-authentication"></a>配置 ACR 身份验证

首先，获取 DC/OS 主节点的 FQDN，并将其存储在变量中。

```azurecli-interactive
FQDN=$(az acs list --resource-group myResourceGroup --query "[0].masterProfile.fqdn" --output tsv)
```

与基于 DC/OS 的集群的主节点（或第一个主节点）建立 SSH 连接。 如果创建群集时使用了非默认值，请更新用户名。

```azurecli-interactive
ssh azureuser@$FQDN
```

运行以下命令登录到 Azure 容器注册表。 将 `--username` 替换为容器注册表名称，将 `--password` 替换为提供的密码之一。 将示例中的最后一个参数 mycontainerregistry.azurecr.io 替换为容器注册表的 loginServer 名称。 

此命令在本地 `~/.docker` 路径下存储身份验证值。

```azurecli-interactive
docker -H tcp://localhost:2375 login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry.azurecr.io
```

创建包含容器注册表身份验证值的压缩文件。

```azurecli-interactive
tar czf docker.tar.gz .docker
```

将此文件复制到群集共享存储。 此步骤将使该文件在 DC/OS 群集的所有节点上均可用。

```azurecli-interactive
cp docker.tar.gz /mnt/share/dcosshare
```

## <a name="upload-image-to-acr"></a>将映像上传到 ACR

现在，在开发计算机或任何已安装 Docker 的其他系统中，请创建映像并将其上传到 Azure 容器注册表。

通过 Ubuntu 映像创建容器。

```azurecli-interactive
docker run ubuntu --name base-image
```

现在，将容器捕获到新映像。 映像名称需要包括容器注册表的 `loginServer` 名称，格式为 `loginServer/imageName`。

```azurecli-interactive
docker -H tcp://localhost:2375 commit base-image mycontainerregistry30678.azurecr.io/dcos-demo
````

登录 Azure 容器注册表。 将名称替换为 loginServer 名称，将 --username 替换为容器注册表的名称，而将 --password 替换为提供的密码之一。

```azurecli-interactive
docker login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry2675.azurecr.io
```

最后，将映像上传到 ACR 注册表。 此示例将上传名为 dcos-demo 的映像。

```azurecli-interactive
docker push mycontainerregistry30678.azurecr.io/dcos-demo
```

## <a name="run-an-image-from-acr"></a>从 ACR 运行映像

若要通过 ACR 注册表使用映像，创建文件名 acrDemo.json 并将以下文本复制到其中。 将映像名称替换为容器注册表 loginServer 名称和映像名称，例如 `loginServer/imageName`。 记下 `uris` 属性。 此属性包含容器注册表身份验证文件的位置，在这种情况下，此位置为在 DC/OS 群集的每个节点上装载的 Azure 文件共享。

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "mycontainerregistry30678.azurecr.io/dcos-demo",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "uris":  [
       "file:///mnt/share/dcosshare/docker.tar.gz"
   ]
}
```

使用 DC/OC CLI 部署应用程序。

```azurecli-interactive
dcos marathon app add acrDemo.json
```

## <a name="next-steps"></a>后续步骤

在本教程中，你将配置 DC/OS 使用 Azure 容器注册表，其中包含以下任务：

> [!div class="checklist"]
> * 部署 Azure 容器注册表（必要时）
> * 在 DC/OS 群集上配置 ACR 身份验证
> * 将映像上传到 Azure 容器注册表
> * 从 Azure 容器注册表运行容器映像
