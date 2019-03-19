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
ms.openlocfilehash: 5ed6e0b21b00ede3f78a102fd004e5706ae3cea5
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57571212"
---
# <a name="using-openfaas-on-aks"></a>在 AKS 上使用 OpenFaaS

[OpenFaaS] [ open-faas]是用于构建无服务器函数通过使用容器的框架。 作为一个开源项目，它在社区中大规模采用。 本文档详细介绍了如何在 Azure Kubernetes 服务 (AKS) 群集上安装和使用 OpenFaas。

## <a name="prerequisites"></a>先决条件

为了完成本文中的步骤，需要具备以下各项。

* 基本了解 Kubernetes。
* 已在开发系统上配置了 Azure Kubernetes 服务 (AKS) 群集和 AKS 凭据。
* 已在开发系统上安装 Azure CLI。
* 已在系统上安装 Git 命令行工具。

## <a name="add-the-openfaas-helm-chart-repo"></a>将 OpenFaaS helm 图表存储库添加

OpenFaaS 维护其自己的 helm 图表，以保持最新的所有最新更改。

```azurecli-interactive
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>部署 OpenFaaS

作为一种良好做法，OpenFaaS 和 OpenFaaS 函数应分别存储在其自己的 Kubernetes 命名空间中。

创建的 OpenFaaS 系统和函数的命名空间：

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

为 OpenFaaS UI 门户和 REST API 生成密码：

```azurecli-interactive
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

你可以获取与机密的值`echo $PASSWORD`。

我们在这里创建的密码将 helm 图表，用于启用基本身份验证在 OpenFaaS 网关，它通过云负载均衡器向 Internet 公开。

克隆的存储库中包括了 OpenFaaS 的一个 Helm chart。 可以使用此图表将 OpenFaaS 部署到 AKS 群集。

```azurecli-interactive
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
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

输出。

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

若要测试 OpenFaaS 系统，请在端口 8080 上浏览到外部 IP 地址，在本例中为 `http://52.186.64.52:8080`。 系统将提示您登录。 若要提取你的密码，输入`echo $PASSWORD`。

![OpenFaaS UI](media/container-service-serverless/openfaas.png)

最后，安装 OpenFaaS CLI。 此示例使用的是 brew。有关更多选项，请参阅 [OpenFaaS CLI 文档][open-faas-cli]。

```console
brew install faas-cli
```

设置`$OPENFAAS_URL`到上面找到的公共 IP。

Azure CLI 登录：

```azurecli-interactive
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>创建第一个函数

现在，OpenFaaS 已可运行，使用 OpenFaas 门户创建一个函数。

单击“部署新函数”并搜索 Figlet。 选择 Figlet 函数，然后单击“部署”。

![Figlet](media/container-service-serverless/figlet.png)

使用 curl 来调用该函数。 将下例中的 IP 地址替换为 OpenFaas 网关的 IP 地址。

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

部署一个 `MongoDB` 类型的 CosmosDB 实例。 此实例需要一个唯一的名称，请将 `openfaas-cosmos` 更新为环境中唯一的某个名称。

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

获取 Cosmos 数据库连接字符串并将其存储在一个变量中。

将 `--resource-group` 参数的值更新为资源组名，将 `--name` 参数的值更新为 Cosmos DB 的名称。

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

运行以下命令来创建函数。 将 `-g` 参数的值更新为 OpenFaaS 网关地址。

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

您可以继续使用通过一系列动手实验，涵盖了主题，如如何创建你自己的 GitHub 机器人，OpenFaaS 研讨会了解使用机密，查看度量值，并自动缩放。

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
