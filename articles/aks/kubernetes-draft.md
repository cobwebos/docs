---
title: 在 Azure Kubernetes Service (AKS) 上进行开发, 草稿
description: 结合使用 Draft 与 AKS 和 Azure 容器注册表
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: bd099b9d76e17eda36be1650ef5081e5aaa7e53a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "67303548"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>快速入门：在 Azure Kubernetes Service (AKS) 上进行开发, 草稿

草稿是一种开源工具, 有助于在 Kubernetes 群集中打包和运行应用程序容器。 使用草稿, 可以在代码发生更改时快速将应用程序重新部署到 Kubernetes, 而无需将所做的更改提交到版本控制。 有关草稿的详细信息, 请参阅[GitHub 上的草稿文档][draft-documentation]。

本文介绍如何使用草稿包并在 AKS 上运行应用程序。


## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free)。
* [已安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。
* 已安装和配置 Docker。 Docker 提供的包在[Mac][docker-for-mac]、 [Windows][docker-for-windows]或[Linux][docker-for-linux]系统上配置 Docker。
* [已安装 Helm](https://github.com/helm/helm/blob/master/docs/install.md)。
* [已安装草稿][draft-documentation]。

## <a name="create-an-azure-kubernetes-service-cluster"></a>创建 Azure Kubernetes 服务群集

创建 AKS 群集。 以下命令将创建名为 MyResourceGroup 的资源组和名为 MyAKS 的 AKS 群集。

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>创建 Azure 容器注册表
若要使用草稿在 AKS 群集中运行应用程序, 需要使用 Azure 容器注册表来存储容器映像。 下面的示例使用[az acr create][az-acr-create]在*MyResourceGroup*资源组中创建名为*MyDraftACR*的 acr, 其中包含*基本*SKU。 应该提供自己的唯一注册表名称。 注册表名称在 Azure 中必须唯一，并且包含 5-50 个字母数字字符。 “基本”SKU 是一个针对成本优化的入口点，适用于可以对存储和吞吐量进行均衡考虑的开发目的。

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

输出类似于以下示例。 记下 ACR 的*loginServer*值, 因为稍后的步骤会用到它。 在下面的示例中, *mydraftacr.azurecr.io*是*mydraftacr*的*loginServer* 。

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyDraftACR",
  "location": "eastus",
  "loginServer": "mydraftacr.azurecr.io",
  "name": "MyDraftACR",
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


要使草稿使用 ACR 实例, 你必须首先登录。 使用[az acr login][az-acr-login]命令登录。 下面的示例将登录名为*MyDraftACR*的 ACR。

```azurecli
az acr login --name MyDraftACR
```

完成后，该命令会返回“登录成功”消息。

## <a name="create-trust-between-aks-cluster-and-acr"></a>在 AKS 群集与 ACR 之间创建信任

你的 AKS 群集还需要访问你的 ACR 以拉取容器映像并运行它们。 通过建立信任, 允许从 AKS 访问 ACR。 若要在 AKS 群集与 ACR 注册表之间建立信任，请为 AKS 群集使用的 Azure Active Directory 服务主体授予 ACR 注册表的访问权限。 以下命令将对*MyResourceGroup*中*MyAKS*群集的服务主体的权限授予*MyResourceGroup*中的*MyDraftACR* ACR。

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>连接到 AKS 群集

若要从本地计算机连接到 Kubernetes 群集，请使用 [kubectl][kubectl]（Kubernetes 命令行客户端）。

如果使用的是 Azure Cloud Shell，则 `kubectl` 已安装。 也可使用 [az aks install-cli][] 命令在本地安装它：

```azurecli
az aks install-cli
```

若要将 `kubectl` 配置为连接到 Kubernetes 群集，请使用 [az aks get-credentials][] 命令。 以下示例获取*MyResourceGroup*中名为*MyAKS*的 AKS 群集的凭据:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>为 Helm 创建服务帐户

在已启用 RBAC 的 AKS 群集中部署 Helm 之前，需要 Tiller 服务的服务帐户和角色绑定。 有关在启用 RBAC 的群集中保护 Helm/Tiller 的详细信息，请参阅 [Tiller、命名空间和 RBAC][tiller-rbac]。 如果 AKS 群集未启用 RBAC, 请跳过此步骤。

创建名为 `helm-rbac.yaml` 的文件，并将其复制到以下 YAML 中：

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

使用 `kubectl apply` 命令创建服务帐户和角色绑定：

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>配置 Helm
若要将基本 Tiller 部署到 AKS 群集，请使用 [helm init][helm-init] 命令。 如果你的`--service-account`群集未启用 RBAC, 请删除参数和值。

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>配置 Draft

如果你尚未在本地计算机上配置草稿, 请`draft init`运行:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

还需要将草稿配置为使用 ACR 的*loginServer* 。 以下命令`draft config set` `mydraftacr.azurecr.io`使用将用作注册表。

```console
draft config set registry mydraftacr.azurecr.io
```

已将草稿配置为使用 ACR, 草稿可以将容器映像推送到 ACR。 当草稿在 AKS 群集中运行应用程序时, 无需密码或密码即可从 ACR 注册表推送到或从其拉取。 由于在 AKS 群集和 ACR 之间创建了信任, 因此会使用 Azure Active Directory 在 Azure 资源管理器级别进行身份验证。

## <a name="download-the-sample-application"></a>下载示例应用程序

本快速入门使用[草稿 GitHub 存储库中的示例 java 应用程序][example-java]。 从 GitHub 克隆应用程序并导航到该`draft/examples/example-java/`目录。

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>运行带草稿的示例应用程序

`draft create`使用命令来准备应用程序。

```console
draft create
```

此命令创建可用于在 Kubernetes 群集中运行应用程序的项目。 这些项包括 Dockerfile、Helm 图表和 *draft.toml* 文件（Draft 配置文件）。

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

若要在 AKS 群集中运行该示例应用程序，请使用 `draft up` 命令。

```console
draft up
```

此命令生成 Dockerfile 以创建容器映像, 将该映像推送到 ACR, 并安装 Helm 图表以在 AKS 中启动该应用程序。 首次运行此命令时, 推送和拉取容器映像可能需要一段时间。 缓存基本层后，部署应用程序所需的时间会大大减少。

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>从本地计算机连接到正在运行的示例应用程序

若要测试应用程序，请使用 `draft connect` 命令。

```console
draft connect
```

此命令将代理与 Kubernetes pod 之间的安全连接。 完成后，可在提供的 URL 上访问应用程序。

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

使用`localhost` url 在浏览器中导航到应用程序, 以查看示例应用程序。 在上面的示例中, url 为`http://localhost:49804`。 使用`Ctrl+c`停止连接。

## <a name="access-the-application-on-the-internet"></a>在 Internet 上访问应用程序

上一步骤与 AKS 群集中的应用程序 pod 创建了代理连接。 开发和测试应用程序时，可能需要通过 Internet 访问应用程序。 若要在 internet 上公开应用程序, 可以创建一个类型为[LoadBalancer][kubernetes-service-loadbalancer]的 Kubernetes 服务。

更新`charts/example-java/values.yaml`以创建一个*LoadBalancer*服务。 将*群集 ip*的值从 "*类型*" 更改为 " *LoadBalancer*"。

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

保存你所做的更改, 关闭文件, `draft up`然后运行重新运行应用程序。

```console
draft up
```

服务可能需要几分钟才能返回公共 IP 地址。 若要监视进度，请结合 *watch* 参数使用 `kubectl get service` 命令：

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

使用*外部 IP*在浏览器中导航到应用程序的负载均衡器, 查看示例应用程序。 在上面的示例中, IP 为`52.175.224.118`。

## <a name="iterate-on-the-application"></a>循环访问应用程序

你可以通过在本地进行更改并重新运行`draft up`来循环访问应用程序。

更新[src/main/java/helloworld/Hello .java 第7行][example-java-hello-l7]返回的消息。

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

运行 `draft up` 命令以重新部署应用程序：

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

若要查看已更新的应用程序, 请再次导航到负载均衡器的 IP 地址, 并验证是否显示了所做的更改。

## <a name="delete-the-cluster"></a>删除群集

如果不再需要群集, 请使用[az group delete][az-group-delete]命令删除资源组、AKS 群集、容器注册表、存储在该处的容器映像以及所有相关资源。

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> 删除群集时，AKS 群集使用的 Azure Active Directory 服务主体不会被删除。 有关如何删除服务主体的步骤，请参阅 [AKS 服务主体的注意事项和删除][sp-delete]。

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 Draft，请参阅 GitHub 上的 Draft 文档。

> [!div class="nextstepaction"]
> [Draft 文档][draft-documentation]


[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-login
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[kubernetes-ingress]: ./ingress-basic.md

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[example-java]: https://github.com/Azure/draft/tree/master/examples/example-java
[example-java-hello-l7]: https://github.com/Azure/draft/blob/master/examples/example-java/src/main/java/helloworld/Hello.java#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[helm-init]: https://docs.helm.sh/helm/#helm-init
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
