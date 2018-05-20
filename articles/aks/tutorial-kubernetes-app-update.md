---
title: Azure 上的 Kubernetes 教程 - 更新应用程序
description: AKS 教程 - 更新应用程序
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: df118a2b5bd8e31bd3fe6101d1d3f631092b6f24
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>教程：在 Azure Kubernetes 服务 (AKS) 中更新应用程序

在 Kubernetes 中部署应用程序后，可以指定新的容器映像或映像版本，从而更新应用程序。 这样做时，更新会进行暂存，因此只有一部分部署会同时更新。 借助这种暂存更新，可以让应用程序在更新期间继续运行。 如果发生部署故障，还可以利用它的回滚机制。

在本教程的第 6 部分（共 8 部分），便完成了对 Azure Vote 应用示例的更新。 要完成的任务包括：

> [!div class="checklist"]
> * 更新前端应用程序代码
> * 创建更新的容器映像
> * 将容器映像推送至 Azure 容器注册表
> * 部署更新的容器映像

在后续教程中，会将 Log Analytics 配置为监视 Kubernetes 群集。

## <a name="before-you-begin"></a>开始之前

在前面的教程中，已将应用程度打包到容器映像中，将该映像上传到 Azure 容器注册表，并创建了 Kubernetes 群集。 应用程序随后在 Kubernetes 群集上运行。

还克隆了应用程序存储库，其中包括应用程序源代码和本教程中使用的预创建的 Docker Compose 文件。 验证是否已克隆存储库，并且是否已将目录更改为克隆的目录。 其中包含 `azure-vote` 目录和 `docker-compose.yaml` 文件。

如果尚未完成这些步骤，并且想要逐一完成，请返回到[教程 1 - 创建容器映像][aks-tutorial-prepare-app]。

## <a name="update-application"></a>更新应用程序

本教程将更改应用程序，并将更新后的应用程序部署到 Kubernetes 群集。

应用程序源代码位于 `azure-vote` 目录中。 使用任意代码或文本编辑器打开 `config_file.cfg` 文件。 此示例使用 `vi`。

```console
vi azure-vote/azure-vote/config_file.cfg
```

更改 `VOTE1VALUE` 和 `VOTE2VALUE` 的值，然后保存文件。

```console
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

保存并关闭该文件。

## <a name="update-container-image"></a>更新容器映像

使用 [docker-compose][docker-compose] 重新创建前端映像并运行更新的应用程序。 `--build` 参数用于指示 Docker Compose 重新创建应用程序映像。

```console
docker-compose up --build -d
```

## <a name="test-application-locally"></a>在本地测试应用程序

浏览到 http://localhost:8080 查看更新的应用程序。

![Azure 上的 Kubernetes 群集映像](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>标记和推送映像

使用容器注册表的 loginServer 标记 `azure-vote-front` 映像。

运行 [az acr list](/cli/azure/acr#az_acr_list) 命令，获取登录服务器名称。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

使用 [docker tag][docker-tag] 标记映像。 将 `<acrLoginServer>` 替换为 Azure 容器注册表登录服务器名称或公共注册表主机名。 另请注意，映像版本已更新为 `v2`。

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v2
```

使用 [docker push][docker-push] 将映像上传到注册表。 将 `<acrLoginServer>` 替换为 Azure 容器注册表的登录服务器名称。 如果在推送到 ACR 注册表时遇到问题，请确保已运行 [az acr login][az-acr-login] 命令。

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-update-application"></a>部署更新的应用程序

为了确保最长运行时间，必须运行应用程序 Pod 的多个实例。 使用 [kubectl get pod][kubectl-get] 命令验证此配置。

```
kubectl get pod
```

输出：

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

如果没有多个 Pod 在运行 azure-vote-front 映像，请缩放 `azure-vote-front` 部署。


```azurecli
kubectl scale --replicas=3 deployment/azure-vote-front
```

若要更新应用程序，请使用 [kubectl set][kubectl-set] 命令。 使用登录服务器或容器注册表的主机名更新 `<acrLoginServer>`。

```azurecli
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

若要监视部署，请使用 [kubectl get pod][kubectl-get] 命令。 部署更新的应用程序时，Pod 终止运行并通过新容器映像重新创建。

```azurecli
kubectl get pod
```

输出：

```
NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-updated-application"></a>测试更新的应用程序

获取 `azure-vote-front` 服务的外部 IP 地址。

```azurecli
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

转到下一篇教程，了解如何使用 Log Analytics 监视 Kubernetes。

> [!div class="nextstepaction"]
> [使用 Log Analytics 监视 Kubernetes][aks-tutorial-monitor]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-monitor]: ./tutorial-kubernetes-monitor.md
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az_acr_login