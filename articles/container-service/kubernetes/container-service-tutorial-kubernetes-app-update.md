---
title: "Azure 容器服务教程 - 更新应用程序 | Microsoft Docs"
description: "Azure 容器服务教程 - 更新应用程序"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 容器, 微服务, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 72cdbfe2fe65e5152ca748cbb3989e3ef980ee50
ms.contentlocale: zh-cn
ms.lasthandoff: 07/25/2017

---

# <a name="update-an-application-in-kubernetes"></a>更新 Kubernetes 中的应用程序

在 Kubernetes 中部署应用程序后，可以通过指定新的容器映像或映像版本进行更新。 更新应用程序时，分步推出更新，因此只有一部分部署同时更新。 分步更新使得应用程序在更新期间保持运行，并在部署失败时提供回退机制。 

在本教程的第 6 部分（共 7 部分），便完成了对 Azure Vote 应用示例的更新。 要完成的任务包括：

> [!div class="checklist"]
> * 更新前端应用程序代码
> * 创建更新的容器映像
> * 将容器映像推送至 Azure 容器注册表
> * 部署更新的容器映像

在后续教程中，会将 Operations Management Suite 配置为监视 Kubernetes 群集。

## <a name="before-you-begin"></a>开始之前

在前面的教程中，已将应用程度打包到容器映像中，将该映像上传到 Azure 容器注册表，并创建了 Kubernetes 群集。 应用程序随后在 Kubernetes 群集上运行。 

如果尚未完成这些步骤，并且想要逐一完成，请返回到[教程 1：创建容器映像](./container-service-tutorial-kubernetes-prepare-app.md)。 

## <a name="update-application"></a>更新应用程序

若要完成本教程中的步骤，必须克隆 Azure Vote 应用程序的副本。 如有必要，请使用以下命令创建此克隆的副本：

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

使用任意代码或文本编辑器打开 `config_file.cfg` 文件。 可以在克隆存储库的以下目录中找到此文件。

```bash
 /azure-voting-app-redis/azure-vote/azure-vote/config_file.cfg
```

更改 `VOTE1VALUE` 和 `VOTE2VALUE` 的值，然后保存文件。

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

使用 [docker-compose](https://docs.docker.com/compose/) 重新创建前端映像并运行更新的应用程序。

```bash
docker-compose -f ./azure-voting-app-redis/docker-compose.yaml up --build -d
```

## <a name="test-application-locally"></a>在本地测试应用程序

浏览到 `http://localhost:8080` 查看更新的应用程序。

![Azure 上的 Kubernetes 群集映像](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>标记和推送映像

使用容器注册表的 loginServer 标记 azure-vote-front 映像。

如果正在使用 Azure 容器注册表，通过 [az acr list](/cli/azure/acr#list) 命令获取登录服务器名称。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

使用 [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) 标记映像。 将 `<acrLoginServer>` 替换为 Azure 容器注册表登录服务器名称或公共注册表主机名。

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 将映像上传到注册表。 将 `<acrLoginServer>` 替换为 Azure 容器注册表登录服务器名称或公共注册表主机名。

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## <a name="deploy-update-application"></a>部署更新的应用程序

为了确保最长运行时间，必须运行应用程序 Pod 的多个实例。 使用 [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 命令验证此配置。

```bash
kubectl get pod
```

输出：

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

如果没有多个 Pod 运行 azure-vote-front 映像，请缩放 *azure-vote-front* 部署。


```azurecli-interactive
kubectl scale --replicas=3 deployment/azure-vote-front
```

若要更新应用程序，请使用 [kubectl set](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#set) 命令。 使用登录服务器或容器注册表的主机名更新 `<acrLoginServer>`。

```azurecli-interactive
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

若要监视部署，请使用 [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 命令。 部署更新的应用程序时，Pod 终止运行并通过新容器映像重新创建。

```azurecli-interactive
kubectl get pod
```

输出：

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>测试更新的应用程序

获取 azure-vote-front 服务的外部 IP 地址。

```azurecli-interactive
kubectl get service azure-vote-front
```

浏览到该 IP 地址，查看更新的应用程序。

![Azure 上的 Kubernetes 群集映像](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>后续步骤

在本教程中，更新了应用程序并向 Kubernetes 群集推出了此更新。 完成了以下任务：

> [!div class="checklist"]
> * 更新前端应用程序代码
> * 创建更新的容器映像
> * 将容器映像推送到 Azure 容器注册表
> * 部署更新的应用程序

进入下一教程，了解如何使用 Operations Management Suite 监视 Kubernetes。

> [!div class="nextstepaction"]
> [使用 OMS 监视 Kubernetes](./container-service-tutorial-kubernetes-monitor.md)
