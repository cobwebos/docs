---
title: 结合使用 OpenFaaS 与 Azure Kubernetes 服务 (AKS)
description: 部署 OpenFaaS 并将其与 Azure Kubernetes 服务 (AKS) 配合使用
services: container-service
author: justindavies
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: e26f1c298b05153736edd2b2efd0f1b27162bc3d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="using-openfaas-on-aks"></a>在 AKS 上使用 OpenFaaS

[OpenFaaS][open-faas] 是一个用于基于容器构建无服务器函数的框架。 作为一个开源项目，它在社区中获得了大规模的采用。 本文档详细介绍了如何在 Azure Kubernetes 服务 (AKS) 群集上安装和使用 OpenFaas。

## <a name="prerequisites"></a>先决条件

为了完成本文中的步骤，需要具备以下各项。

* 基本了解 Kubernetes。
* 已在开发系统上配置了 Azure Kubernetes 服务 (AKS) 群集和 AKS 凭据。
* 已在开发系统上安装 Azure CLI。
* 已在系统上安装 Git 命令行工具。

## <a name="get-openfaas"></a>获取 OpenFaaS

将 OpenFaaS 项目存储库克隆到开发系统。

```azurecli-interactive
git clone https://github.com/openfaas/faas-netes
```

更改到已克隆的存储库的目录。

```azurecli-interactive
cd faas-netes
```

## <a name="deploy-openfaas"></a>部署 OpenFaaS

作为一种良好做法，OpenFaaS 和 OpenFaaS 函数应分别存储在其自己的 Kubernetes 命名空间中。

为 OpenFaaS 系统创建一个命名空间。

```azurecli-interactive
kubectl create namespace openfaas
```

为 OpenFaaS 函数创建另一个命名函数。

```azurecli-interactive
kubectl create namespace openfaas-fn
```

克隆的存储库中包括了 OpenFaaS 的一个 Helm chart。 可以使用此图表将 OpenFaaS 部署到 AKS 群集。

```azurecli-interactive
helm install --namespace openfaas -n openfaas \
  --set functionNamespace=openfaas-fn, \
  --set serviceType=LoadBalancer, \
  --set rbac=false chart/openfaas/
```

输出：

```
NAME:   openfaas
LAST DEPLOYED: Wed Feb 28 08:26:11 2018
NAMESPACE: openfaas
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                 DATA  AGE
prometheus-config    2     20s
alertmanager-config  1     20s

{snip}

NOTES:
To verify that openfaas has started, run:

  kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

创建了一个用于访问 OpenFaaS 网关的公用 IP 地址。 若要检索此 IP 地址，请使用 [kubectl get service][kubectl-get] 命令。 将 IP 地址分配到服务可能需要一分钟时间。

```console
kubectl get service -l component=gateway --namespace openfaas
```

输出：

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

若要测试 OpenFaaS 系统，请在端口 8080 上浏览到外部 IP 地址，在本例中为 `http://52.186.64.52:8080`。

![OpenFaaS UI](media/container-service-serverless/openfaas.png)

最后，安装 OpenFaaS CLI。 此示例使用了 brew，有关更多选项，请参阅 [OpenFaaS CLI 文档][open-faas-cli]。

```console
brew install faas-cli
```

## <a name="create-first-function"></a>创建第一个函数

现在，OpenFaaS 已可运行，使用 OpenFaas 门户创建一个函数。

单击“部署新函数”并搜索 **Figlet**。 选择 Figlet 函数，然后单击“部署”。

![Figlet](media/container-service-serverless/figlet.png)

使用 curl 来调用该函数。 将下例中的 IP 地址替换为你的 OpenFaas 网关的 IP 地址。

```azurecli-interactive
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

输出：

```console
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>创建第二个函数

现在，创建另一个函数。 此示例将使用 OpenFaaS CLI 进行部署，包括一个自定义容器映像并从 Cosmos DB 检索数据。 在创建函数之前，需要配置多个项。

首先，为 Cosmos DB 创建一个新的资源组。

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

部署一个 `MongoDB` 类型的 CosmosDB 实例。 此实例需要一个唯一的名称，请将 `openfaas-cosmos` 更新为在你的环境中唯一的某个名称。

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

获取 Cosmos 数据库连接字符串并将其存储在一个变量中。

将 `--resource-group` 参数的值更新为你的资源组的名称，将 `--name` 参数的值更新为你的 Cosmos DB 的名称。

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

现在，使用测试数据填充 Cosmos 数据库。 创建一个名为 `plans.json` 的文件，并将其复制到以下 json 中。

```json
{
    "name" : "two_person",
    "friendlyName" : "Two Person Plan",
    "portionSize" : "1-2 Person",
    "mealsPerWeek" : "3 Unique meals per week",
    "price" : 72,
    "description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
    "__v" : 0
}
```

使用 *mongoimport* 工具加载具有数据的 CosmosDB 实例。

如果需要，安装 MongoDB 工具。 以下示例使用 brew 安装这些工具，有关其他选项，请参阅 [MongoDB 文档][install-mongo]。

```azurecli-interactive
brew install mongodb
```

将数据加载到数据库中。

```azurecli-interactive
mongoimport --uri=$COSMOS -c plans < plans.json
```

输出：

```console
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

运行以下命令来创建函数。 将 `-g` 参数的值更新为你的 OpenFaaS 网关地址。

```azurecli-interctive
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

在部署后，应当会看到为函数新创建的 OpenFaaS 终结点。

```console
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

使用 curl 测试函数。 将 IP 地址更新为 OpenFaaS 网关地址。

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

输出：

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

还可以在 OpenFaaS UI 中测试函数。

![替换文字](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>后续步骤

需要针对 OpenFaaS 网关和函数锁定 OpenFaas 的默认部署。 [Alex Ellis 的博客文章](https://blog.alexellis.io/lock-down-openfaas/)介绍了有关安全的配置选项的更多详细信息。

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli