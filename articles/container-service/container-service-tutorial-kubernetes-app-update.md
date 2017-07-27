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
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: ce4a88a7958116890ec17eb2405ba809487b9c0f
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017

---

# <a name="update-an-application-in-kubernetes"></a>更新 Kubernetes 中的应用程序

在部署 Kubernetes 中的应用程序以后，可以通过指定新的容器映像或映像版本来更新。 更新应用程序时，分步推出更新，因此只有一部分部署同时更新。 

分步更新使得应用程序在更新期间保持运行，并在部署失败时提供回退机制。 

在本教程中，示例 Azure Vote 应用已更新。 要完成的任务包括：

> [!div class="checklist"]
> * 更新前端应用程序代码。
> * 创建更新的容器映像。
> * 将容器映像推送至 Azure 容器注册表。
> * 部署更新的应用程序。

## <a name="before-you-begin"></a>开始之前

在上一教程中，我们已将应用程序打包到容器映像中、将映像更新到 Azure 容器注册表并且创建了 Kubernetes 群集。 然后，我们在 Kubernetes 群集上运行了应用程序。 

如果尚未执行这些步骤，并想要立即尝试执行，请返回到[教程 1 – 创建容器映像](./container-service-tutorial-kubernetes-prepare-app.md)。 

本教程至少需要应用程序正在其中运行的 Kubernetes 群集。

## <a name="update-application"></a>更新应用程序

若要完成本教程中的步骤，必须克隆 Azure Vote 应用程序的副本。 如有必要，请使用以下命令创建此克隆的副本：

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

使用任意代码或文本编辑器打开 `config_file.cfg` 文件。 可以在克隆存储库的以下目录中找到此文件。

```bash
 /azure-voting-app/azure-vote/azure-vote/config_file.cfg
```

更改 `VOTE1VALUE` 和 `VOTE2VALUE` 的值，然后保存文件。

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Half Full'
VOTE2VALUE = 'Half Empty'
SHOWHOST = 'false'
```

使用 `docker build` 重新创建前端映像。

```bash
docker build --no-cache ./azure-voting-app/azure-vote -t azure-vote-front:v2
```

## <a name="test-application"></a>测试应用程序

创建 Docker 网络。 此网络用于容器之间的通信。  

```bash
docker network create azure-vote
```

使用 `docker run` 命令运行后端容器映像的实例。

```bash
docker run -p 3306:3306 --name azure-vote-back -d --network azure-vote -e MYSQL_ROOT_PASSWORD=Password12 -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote azure-vote-back 
```

运行前端容器映像的实例。

```bash
docker run -d -p 8080:80 --name azure-vote-front --network=azure-vote -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote -e MYSQL_HOST=azure-vote-back azure-vote-front:v2
```

转到 `http://localhost:8080` 查看更新的应用程序。 应用程序初始化需要几秒钟。 如果遇到错误，请重试。

![Azure 上的 Kubernetes 群集映像](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>标记和推送映像

使用容器注册表的 loginServer 标记 azure-vote-front 映像。

如果正在使用 Azure 容器注册表，通过 [az acr list](/cli/azure/acr#list) 命令获取登录服务器名称。

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

使用 [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) 标记映像，确保使用 Azure 容器注册表登录服务器或公共注册表主机名更新命令。

```bash
docker tag azure-vote-front:v2 <acrLoginServer>/azure-vote-front:v2
```

将映像推送到注册表。 将 `<acrLoginServer>` 替换为 Azure 容器注册表登录服务器名称或公共注册表主机名。

```bash
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-update-to-kubernetes"></a>将更新部署到 Kubernetes

### <a name="verify-multiple-pod-replicas"></a>验证多个 POD 副本

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


```bash
kubectl scale --replicas=3 deployment/azure-vote-front
```

### <a name="update-application"></a>更新应用程序

若要更新应用程序，请运行以下命令。 使用登录服务器或容器注册表的主机名更新 `<acrLoginServer>`。

```bash
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

若要监视部署，请使用 [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 命令。 部署更新的应用程序时，Pod 终止运行并通过新容器映像重新创建。

```bash
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

```bash
kubectl get service
```

转到 IP 地址，查看更新的应用程序。

![Azure 上的 Kubernetes 群集映像](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>后续步骤

在本教程中，更新了应用程序并向 Kubernetes 群集推出了此更新。 完成了以下任务：  

> [!div class="checklist"]
> * 已更新前端应用程序代码。
> * 已创建更新的容器映像。
> * 已将容器映像推送到 Azure 容器注册表。
> * 已部署更新的应用程序。

进入下一教程，了解如何使用 Operations Management Suite 监视 Kubernetes。

> [!div class="nextstepaction"]
> [使用 OMS 监视 Kubernetes](./container-service-tutorial-kubernetes-monitor.md)
