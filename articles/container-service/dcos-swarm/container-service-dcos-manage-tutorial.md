---
title: （已弃用）Azure 容器服务教程 - 管理 DC/OS
description: Azure 容器服务教程 - 管理 DC/OS
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4212277dbdf29705152832f3830692b43b8d1297
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "78402811"
---
# <a name="deprecated-azure-container-service-tutorial---manage-dcos"></a>（已弃用）Azure 容器服务教程 - 管理 DC/OS

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS 提供了一个用于运行现代和容器化应用程序的分布式平台。 通过 Azure 容器服务，使预配生产就绪的 DC/OS 群集变得简单快捷。 此快速入门详细介绍了部署 DC/OS 群集和运行基本工作负荷所需的基本步骤。

> [!div class="checklist"]
> * 创建 ACS DC/OS 群集
> * 连接到群集
> * 安装 DC/OS CLI
> * 将应用程序部署到群集
> * 缩放群集上的应用程序
> * 缩放 DC/OS 群集节点
> * 基本 DC/OS 管理
> * 删除 DC/OS 群集

本教程需要 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。 

## <a name="create-dcos-cluster"></a>创建 DC/OS 群集

首先，使用 [az group create](/cli/azure/group#az-group-create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在“westeurope”位置创建名为“myResourceGroup”的资源组。

```azurecli
az group create --name myResourceGroup --location westeurope
```

接下来，使用 [az acs create](/cli/azure/acs#az-acs-create) 命令创建 DC/OS 群集。

下面的示例创建一个名为 myDCOSCluster  的 DC/OS 群集，并且在不存在 SSH 密钥时创建这些密钥。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。  

```azurecli
az acs create \
  --orchestrator-type dcos \
  --resource-group myResourceGroup \
  --name myDCOSCluster \
  --generate-ssh-keys
```

几分钟后，该命令完成并返回有关部署的信息。

## <a name="connect-to-dcos-cluster"></a>连接到 DC/OS 群集

创建 DC/OS 群集后，可以通过 SSH 隧道进行访问。 运行以下命令，以返回主 DC/OS 的公共 IP 地址。 该 IP 地址存储在一个变量中，并将在下一步使用。

```azurecli
ip=$(az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-master')].[ipAddress]" -o tsv)
```

要创建 SSH 隧道，请运行以下命令并按照屏幕上的说明进行操作。 如果正在使用端口 80，则该命令将失败。 将隧道端口更新为未在用的端口，例如 `85:localhost:80`。 

```console
sudo ssh -i ~/.ssh/id_rsa -fNL 80:localhost:80 -p 2200 azureuser@$ip
```

## <a name="install-dcos-cli"></a>安装 DC/OS CLI

使用 [az acs dcos install-cli](/cli/azure/acs/dcos#az-acs-dcos-install-cli) 命令安装 DC/OS cli。 如果使用的是 Azure CloudShell，则 DC/OS CLI 已安装。 如果在 macOS 或 Linux 上运行 Azure CLI，则可能需要将该命令与 sudo 一起运行。

```azurecli
az acs dcos install-cli
```

在 CLI 可用于群集之前，必须将它配置为使用 SSH 隧道。 为此，请运行以下命令，并根据需要调整端口。

```console
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>运行应用程序

ACS DC/OS 群集的默认计划机制为 Marathon。 Marathon 用于启动应用程序和管理 DC/OS 群集上的应用程序状态。 若要通过 Marathon 计划应用程序，则请创建一个名为 marathon app.json 的文件  ，并将以下内容复制到其中。 

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

运行以下命令以计划要在 DC/OS 群集上运行的应用程序。

```console
dcos marathon app add marathon-app.json
```

若要查看应用的部署状态，请运行以下命令。

```console
dcos marathon app list
```

当“TASKS”  列值从“0/1”  切换到“1/1”  时，应用部署已完成。

```output
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     0/1    ---       ---      False      DOCKER   None
```

## <a name="scale-marathon-application"></a>缩放 Marathon 应用程序

在上一个示例中，已创建单个实例应用程序。 要更新此部署，以使应用程序的三个实例可用，请打开“marathon-app.json”  文件，并将实例属性更新为 3。

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 3,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

使用 `dcos marathon app update` 命令更新应用程序。

```console
dcos marathon app update demo-app-private < marathon-app.json
```

若要查看应用的部署状态，请运行以下命令。

```console
dcos marathon app list
```

当“TASKS”  列值从“1/3”  切换到“3/1”  时，应用程序部署已完成。

```output
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/3    ---       ---      False      DOCKER   None
```

## <a name="run-internet-accessible-app"></a>运行的 Internet 可访问的应用

ACS DC/OS 群集包括两个节点集，一个是可通过 Internet 访问的公共节点集，一个是无法通过 Internet 访问的专用节点集。 默认集是专用节点集，将在最后一个示例中使用。

若要通过 Internet 访问应用程序，则需要将其部署到公共节点集。 若要执行此操作，请为 `acceptedResourceRoles` 对象赋值 `slave_public`。

创建名为 nginx-public.json  的文件，并将以下内容复制到其中。

```json
{
  "id": "demo-app",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  },
  "acceptedResourceRoles": [
    "slave_public"
  ]
}
```

运行以下命令以计划要在 DC/OS 群集上运行的应用程序。

```console
dcos marathon app add nginx-public.json
```

获取 DC/OS 公共群集代理的公共 IP 地址。

```azurecli
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

浏览到此地址将返回默认 NGINX 站点。

![NGINX](./media/container-service-dcos-manage-tutorial/nginx.png)

## <a name="scale-dcos-cluster"></a>缩放 DC/OS 群集

在前面的示例中，应用程序已缩放到多个实例。 此外，还可以缩放 DC/OS 基础结构，以提供或多或少的计算能力。 这可通过 [az acs scale](/cli/azure/acs#az-acs-scale) 命令完成。 

若要查看 DC/OS 代理的当前计数，请使用 [az acs show](/cli/azure/acs#az-acs-show) 命令。

```azurecli
az acs show --resource-group myResourceGroup --name myDCOSCluster --query "agentPoolProfiles[0].count"
```

若要将计数增加到 5，请使用 [az acs scale](/cli/azure/acs#az-acs-scale) 命令。 

```azurecli
az acs scale --resource-group myResourceGroup --name myDCOSCluster --new-agent-count 5
```

## <a name="delete-dcos-cluster"></a>删除 DC/OS 群集

如果不再需要资源组、DC/OS 群集和所有相关的资源，则可以使用 [az group delete](/cli/azure/group#az-group-delete) 命令将其删除。

```azurecli
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了包括以下内容在内的基本 DC/OS 管理任务。 

> [!div class="checklist"]
> * 创建 ACS DC/OS 群集
> * 连接到群集
> * 安装 DC/OS CLI
> * 将应用程序部署到群集
> * 缩放群集上的应用程序
> * 缩放 DC/OS 群集节点
> * 删除 DC/OS 群集

继续学习下一个教程，了解 Azure 的 DC/OS 中的负载均衡应用程序。 

> [!div class="nextstepaction"]
> [使应用程序负载均衡](container-service-load-balancing.md)
