---
title: "快速入门 - 适用于 Linux 的 Azure Docker CE 群集"
description: "快速学习在 Azure 容器服务中使用 Azure CLI 为 Linux 容器创建 Docker CE 群集。"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 08/25/2017
ms.author: nepeters
ms.custom: 
ms.openlocfilehash: c2cf81729a0556484bb0eb6c93ea57f0a6208cb0
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="deploy-docker-ce-cluster"></a>部署 Docker CE 群集

在本快速入门中，将使用 Azure CLI 部署 Docker CE 群集。 然后，在群集上部署和运行包含 Web 前端和 Redis 实例的多容器应用程序。 完成后，即可通过 Internet 访问应用程序。

Azure 容器服务上的 Docker CE 为预览版，不应用于生产工作负荷。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

如果选择在本地安装并使用 CLI，此快速入门教程要求运行 Azure CLI 2.0.4 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑组。

以下示例在“ukwest”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive
az group create --name myResourceGroup --location ukwest
```

输出：

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "ukwest",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>创建 Docker Swarm 群集

使用 [az acs create](/cli/azure/acs#az_acs_create) 命令在 Azure 容器服务中创建 Docker CE 群集。 

以下示例创建名为 mySwarmCluster 的群集，其中包含一个 Linux 主节点和三个 Linux 代理节点。

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type dockerce --resource-group myResourceGroup --generate-ssh-keys
```

在某些情况下（如限时试用），Azure 订阅对 Azure 资源的访问受限。 如果由于可用核心有限而导致部署失败，请将 `--agent-count 1` 添加到 [az acs creat](/cli/azure/acs#az_acs_create) 命令中，以减少默认代理计数。 

几分钟后，该命令完成并返回有关群集的 JSON 格式信息。

## <a name="connect-to-the-cluster"></a>连接至群集

本快速入门自始至终都需要 Docker Swarm 主池和代理池的 FQDN。 运行以下命令即可返回主 FQDN 和代理 FQDN。


```bash
az acs list --resource-group myResourceGroup --query '[*].{Master:masterProfile.fqdn,Agent:agentPoolProfiles[0].fqdn}' -o table
```

输出：

```bash
Master                                                               Agent
-------------------------------------------------------------------  --------------------------------------------------------------------
myswarmcluster-myresourcegroup-d5b9d4mgmt.ukwest.cloudapp.azure.com  myswarmcluster-myresourcegroup-d5b9d4agent.ukwest.cloudapp.azure.com
```

创建到 Swarm 主池的 SSH 隧道。 将 `MasterFQDN` 替换为 Swarm 主池的 FQDN 地址。

```bash
ssh -p 2200 -fNL localhost:2374:/var/run/docker.sock azureuser@MasterFQDN
```

设置 `DOCKER_HOST` 环境变量。 这样可以针对 Docker Swarm 运行 docker 命令，不需指定主机名称。

```bash
export DOCKER_HOST=localhost:2374
```

现在可以在 Docker Swarm 上运行 Docker 服务了。


## <a name="run-the-application"></a>运行应用程序

创建名为 `azure-vote.yaml` 的文件，并将以下内容复制到文件中。


```yaml
version: '3'
services:
  azure-vote-back:
    image: redis
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:redis-v1
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

运行 [docker stack deploy](https://docs.docker.com/engine/reference/commandline/stack_deploy/) 命令，创建 Azure Vote 服务。

```bash
docker stack deploy azure-vote --compose-file azure-vote.yaml
```

输出：

```bash
Creating network azure-vote_default
Creating service azure-vote_azure-vote-back
Creating service azure-vote_azure-vote-front
```

使用 [docker stack ps](https://docs.docker.com/engine/reference/commandline/stack_ps/) 命令，返回应用程序的部署状态。

```bash
docker stack ps azure-vote
```

如果每个服务的 `CURRENT STATE` 为 `Running`，则说明应用程序已就绪。

```bash
ID                  NAME                            IMAGE                                 NODE                               DESIRED STATE       CURRENT STATE                ERROR               PORTS
tnklkv3ogu3i        azure-vote_azure-vote-front.1   microsoft/azure-vote-front:redis-v1   swarmm-agentpool0-66066781000004   Running             Running 5 seconds ago                            
lg99i4hy68r9        azure-vote_azure-vote-back.1    redis:latest                          swarmm-agentpool0-66066781000002   Running             Running about a minute ago
```

## <a name="test-the-application"></a>测试应用程序

浏览到 Swarm 代理池的 FQDN，对 Azure Vote 应用程序进行测试。

![浏览到 Azure Vote 的图像](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>删除群集
如果不再需要群集，可以使用 [az group delete](/cli/azure/group#az_group_delete) 命令删除资源组、容器服务及所有相关资源。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>获取代码

本快速入门使用了预先创建的容器映像来创建 Docker 服务。 GitHub 上提供了相关的应用程序代码、Dockerfile 和 Compose 文件。

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>后续步骤

在本快速入门中，你部署了 Docker Swarm 群集，并向该群集部署了多容器应用程序。

若要了解如何将 Docker Swarm 与 Visual Studio Team Services 集成，请继续学习“将 CI/CD 用于 Docker Swarm 和 VSTS”。

> [!div class="nextstepaction"]
> [将 CI/CD 用于 Docker Swarm 和 VSTS](./container-service-docker-swarm-setup-ci-cd.md)
