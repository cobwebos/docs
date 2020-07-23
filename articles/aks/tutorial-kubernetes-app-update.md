---
title: Azure 上的 Kubernetes 教程 - 更新应用程序
description: 本 Azure Kubernetes 服务 (AKS) 教程介绍如何使用新版应用程序代码将现有应用程序部署更新到 AKS。
services: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.custom: mvc
ms.openlocfilehash: d5457d790cd3c95bb23ec0c517097b443a2389ed
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "77593370"
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>教程：在 Azure Kubernetes 服务 (AKS) 中更新应用程序

在 Kubernetes 中部署应用程序后，可以指定新的容器映像或映像版本，从而更新应用程序。 更新分阶段进行，因此，只有一部分部署会同时更新。 借助这种暂存更新，可以让应用程序在更新期间继续运行。 如果发生部署故障，还可以利用它的回滚机制。

在本教程的第 6 部分（共 7 部分），便完成了对 Azure Vote 应用示例的更新。 学习如何：

> [!div class="checklist"]
> * 更新前端应用程序代码
> * 创建更新的容器映像
> * 向 Azure 容器注册表推送容器映像
> * 部署更新的容器映像

## <a name="before-you-begin"></a>开始之前

上一教程中，应用程序已打包到容器映像中。 该映像已上传到 Azure容器注册表，同时，你创建了 AKS 群集。 然后，应用程序部署到了 AKS 群集。

此外，还克隆了应用程序存储库，其中包括应用程序源代码和本教程中使用的预创建的 Docker Compose 文件。 验证是否已克隆存储库，并且是否已将目录更改为克隆的目录。 如果尚未完成这些步骤，并且想要逐一完成，请先阅读[教程 1 - 创建容器映像][aks-tutorial-prepare-app]。

此教程需要运行 Azure CLI 2.0.53 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="update-an-application"></a>更新应用程序

让我们更改示例应用程序，然后更新已部署到 AKS 群集的版本。 确保在克隆的 *azure-voting-app-redis* 目录中操作。 可在 *azure-vote* 目录中找到示例应用程序的源代码。 使用编辑器（例如 `vi`）打开 *config_file.cfg* 文件：

```console
vi azure-vote/azure-vote/config_file.cfg
```

将 *VOTE1VALUE* 和 *VOTE2VALUE* 的值更改为不同的值，例如不同的颜色。 以下示例显示更新的值：

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

保存并关闭该文件。 在 `vi` 中，使用 `:wq`。

## <a name="update-the-container-image"></a>更新容器映像

若要重新创建前端映像并测试更新的应用程序，请使用 [docker-compose][docker-compose]。 `--build` 参数用于指示 Docker Compose 重新创建应用程序映像：

```console
docker-compose up --build -d
```

## <a name="test-the-application-locally"></a>在本地测试应用程序

若要验证已更新的容器映像是否显示所做的更改，请打开一个本地 Web 浏览器并访问 `http://localhost:8080`。

![Azure 上的 Kubernetes 群集映像](media/container-service-kubernetes-tutorials/vote-app-updated.png)

在 *config_file.cfg* 文件中提供的已更新值显示在正运行的应用程序中。

## <a name="tag-and-push-the-image"></a>标记并推送映像

若要正确使用已更新的映像，请使用 ACR 注册表的登录服务器名称标记 *azure-vote-front* 映像。 运行 [az acr list](/cli/azure/acr) 命令，获取登录服务器名称：

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

使用 [docker tag][docker-tag] 标记映像。 将 `<acrLoginServer>` 替换为 ACR 登录服务器名称或公共注册表主机名，并将映像版本更新为 *:v2*，如下所示：

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v2
```

现在，请使用 [docker push][docker-push] 将映像上传到注册表。 将 `<acrLoginServer>` 替换为 ACR 登录服务器名称。

> [!NOTE]
> 如果在推送到 ACR 注册表时遇到问题，请确保你仍已登录。 使用在 [创建 Azure 容器注册表](tutorial-kubernetes-prepare-acr.md#create-an-azure-container-registry)步骤中创建的 Azure 容器注册表的名称运行 [az acr login][az-acr-login] 命令。 例如，`az acr login --name <azure container registry name>` 。

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-the-updated-application"></a>部署更新的应用程序

为了提供最长运行时间，必须运行应用程序 Pod 的多个实例。 使用 [kubectl get pods][kubectl-get] 命令验证正在运行的前端实例的数目：

```
$ kubectl get pods

NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

如果没有多个前端 Pod，请缩放 *azure-vote-front* 部署，如下所示：

```console
kubectl scale --replicas=3 deployment/azure-vote-front
```

若要更新应用程序，请使用 [kubectl set][kubectl-set] 命令。 使用容器注册表的登录服务器或主机名更新 `<acrLoginServer>`，并指定 *v2* 应用程序版本：

```console
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

若要监视部署，请使用 [kubectl get pod][kubectl-get] 命令。 部署更新的应用程序时，Pod 终止运行并通过新容器映像重新创建。

```console
kubectl get pods
```

以下示例输出显示，在部署进行时，Pod 正在终止，新实例正在运行：

```
$ kubectl get pods

NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-the-updated-application"></a>测试更新的应用程序

若要查看更新的应用程序，请先获取 `azure-vote-front` 服务的外部 IP 地址：

```console
kubectl get service azure-vote-front
```

现在，请打开本地 Web 浏览器并访问服务的 IP 地址：

![Azure 上的 Kubernetes 群集映像](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>后续步骤

在本教程中，你更新了一个应用程序并向 AKS 群集推出了此更新。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 更新前端应用程序代码
> * 创建更新的容器映像
> * 向 Azure 容器注册表推送容器映像
> * 部署更新的容器映像

请继续学习下一个教程，了解如何将 AKS 群集升级到新版 Kubernetes。

> [!div class="nextstepaction"]
> [升级 Kubernetes][aks-tutorial-upgrade]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[az-acr-login]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
