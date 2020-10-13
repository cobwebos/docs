---
title: 使用 Helm 在 Azure Kubernetes 服务 (AKS) 上进行开发
description: 结合使用 Helm 与 AKS 和 Azure 容器注册表，打包和运行群集中的应用程序容器。
services: container-service
author: zr-msft
ms.topic: article
ms.date: 07/28/2020
ms.author: zarhoads
ms.openlocfilehash: 0ca2d7ccc863e2208db1212ef3d3f10fa709d069
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87407109"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>快速入门：使用 Helm 在 Azure Kubernetes 服务 (AKS) 上进行开发

[Helm][helm] 是一种开放源打包工具，有助于安装和管理 Kubernetes 应用程序的生命周期。 与诸如 *APT* 和 *Yum* 的 Linux 包管理器类似，Helm 用于管理 Kubernetes 图表，这些图表是预配置的 Kubernetes 资源包。

本文介绍如何使用 Helm 在 AKS 中打包和运行应用程序。 有关使用 Helm 安装现有应用程序的详细信息，请参阅[在 AKS 中通过 Helm 安装现有应用程序][helm-existing]。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free)。
* [已安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。
* [已安装 Helm v3][helm-install]。

## <a name="create-an-azure-container-registry"></a>创建 Azure 容器注册表
若要使用 Helm 在 AKS 群集中运行应用程序，需要使用 Azure 容器注册表来存储容器映像。 以下示例使用 [az acr create][az-acr-create] 在位于“基本”SKU 的 MyResourceGroup 资源组中创建名为 MyHelmACR 的 ACR  。 你应提供自己的唯一注册表名称。 注册表名称在 Azure 中必须唯一，并且包含 5-50 个字母数字字符。 “基本”SKU 是用于开发目的的成本优化入口点，可在存储与吞吐量之间实现平衡。

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

输出类似于以下示例。 记下 ACR 的 *loginServer* 值，因为稍后的步骤会用到它。 在下面的示例中， *myhelmacr.azurecr.io*是*myhelmacr*的*loginServer* 。

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="create-an-azure-kubernetes-service-cluster"></a>创建 Azure Kubernetes 服务群集

创建 AKS 群集。 以下命令将创建名为 MyAKS 的 AKS 群集并附加 MyHelmACR。

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

AKS 群集需要访问 ACR 来提取并运行容器映像。 上述命令还授予 MyAKS 群集对 MyHelmACR ACR 的访问权 。

## <a name="connect-to-your-aks-cluster"></a>连接到 AKS 群集

若要从本地计算机连接到 Kubernetes 群集，请使用 [kubectl][kubectl]（Kubernetes 命令行客户端）。

如果使用的是 Azure Cloud Shell，则 `kubectl` 已安装。 也可使用 [az aks install-cli][] 命令在本地安装它：

```azurecli
az aks install-cli
```

若要将 `kubectl` 配置为连接到 Kubernetes 群集，请使用 [az aks get-credentials][] 命令。 以下示例获取 *MyResourceGroup* 中名为 *MyAKS* 的 AKS 群集的凭据：

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="download-the-sample-application"></a>下载示例应用程序

本快速入门使用[来自 Azure Dev Spaces 示例存储库的示例 Node.js 应用程序][example-nodejs]。 从 GitHub 克隆该应用程序，然后导航到 `dev-spaces/samples/nodejs/getting-started/webfrontend` 目录。

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>创建 Dockerfile

使用以下命令创建新的 Dockerfile 文件：

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>生成并将示例应用程序推送到 ACR

使用前面的 Dockerfile 通过 [az acr build][az-acr-build] 命令生成映像并将其推送到注册表。 命令末尾处的 `.` 设置 Dockerfile 的位置（在本例中为当前目录）。

```azurecli
az acr build --image webfrontend:v1 \
  --registry MyHelmACR \
  --file Dockerfile .
```

## <a name="create-your-helm-chart"></a>创建 Helm 图表

使用 `helm create` 命令生成 Helm 图表。

```console
helm create webfrontend
```

对 webfrontend/values.yaml 进行以下更新。 替换前面步骤中记下的注册表的 loginServer，例如 *myhelmacr.azurecr.io*：

* 将 `image.repository` 更改为 `<loginServer>/webfrontend`
* 将 `service.type` 更改为 `LoadBalancer`

例如：

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: *myhelmacr.azurecr.io*/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

在 webfrontend/Chart.yaml 中将 `appVersion` 更新为 `v1`。 例如

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>运行 Helm 图表

使用 `helm install` 命令安装使用 Helm 图表的应用程序。

```console
helm install webfrontend webfrontend/
```

服务可能需要几分钟才能返回公共 IP 地址。 若要监视进度，请结合 *watch* 参数使用 `kubectl get service` 命令：

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

在浏览器中使用 `<EXTERNAL-IP>` 导航到应用程序的负载均衡器，以查看示例应用程序。

## <a name="delete-the-cluster"></a>删除群集

如果不再需要本教程中创建的群集，请使用[az group delete][az-group-delete] 命令删除资源组、AKS 群集、容器注册表和其中存储的容器映像，以及所有相关资源。

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> 删除群集时，AKS 群集使用的 Azure Active Directory 服务主体不会被删除。 有关如何删除服务主体的步骤，请参阅 [AKS 服务主体的注意事项和删除][sp-delete]。 如果你使用了托管标识，则该标识由平台托管，不需要删除。

## <a name="next-steps"></a>后续步骤

有关使用 Helm 的详细信息，请参阅 Helm 文档。

> [!div class="nextstepaction"]
> [Helm 文档][helm-documentation]

[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
