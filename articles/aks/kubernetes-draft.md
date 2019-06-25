---
title: 在使用 Draft 的 Azure Kubernetes 服务 (AKS) 上进行开发
description: 结合使用 Draft 与 AKS 和 Azure 容器注册表
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: bd099b9d76e17eda36be1650ef5081e5aaa7e53a
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303548"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>快速入门：在使用 Draft 的 Azure Kubernetes 服务 (AKS) 上进行开发

Draft 是一种开放源代码工具，可帮助包并在 Kubernetes 群集中运行应用程序容器。 借助 Draft，您可以快速重新部署到 Kubernetes 应用程序而无需将更改提交到版本控制发生代码更改。 有关 Draft 的详细信息，请参阅[GitHub 上的 Draft 文档][draft-documentation]。

本文介绍如何使用 Draft 包和 AKS 上运行的应用程序。


## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free)。
* [已安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。
* 安装和配置 docker。 Docker 提供的包配置 Docker [Mac][docker-for-mac], [Windows][docker-for-windows]，或[Linux][docker 的 linux]系统。
* [安装 helm](https://github.com/helm/helm/blob/master/docs/install.md)。
* [安装 draft][draft-documentation]。

## <a name="create-an-azure-kubernetes-service-cluster"></a>创建 Azure Kubernetes 服务群集

创建 AKS 群集。 以下命令创建了名为 MyResourceGroup 调用 MyAKS AKS 群集的资源组。

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>创建 Azure 容器注册表
若要结合使用 Draft 在 AKS 群集中运行应用程序，需要 Azure 容器注册表来存储容器映像。 下面的示例中使用[az acr 创建][az-acr-create]创建名为 ACR *MyDraftACR*中*MyResourceGroup*资源组与*基本*SKU。 应提供你自己唯一的注册表的名称。 注册表名称在 Azure 中必须唯一，并且包含 5-50 个字母数字字符。 “基本”SKU 是一个针对成本优化的入口点，适用于可以对存储和吞吐量进行均衡考虑的开发目的。 

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

输出类似于以下示例。 请记下的*loginServer*为 ACR 因为将在稍后的步骤中使用它的值。 在以下示例中， *mydraftacr.azurecr.io*是*loginServer*有关*MyDraftACR*。

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


对于要使用的 ACR 实例的草稿，您必须首先登录。 使用[az acr login][az-acr-login]命令进行登录。 下面的示例将登录到名为 ACR *MyDraftACR*。

```azurecli
az acr login --name MyDraftACR
```

完成后，该命令会返回“登录成功”  消息。

## <a name="create-trust-between-aks-cluster-and-acr"></a>在 AKS 群集与 ACR 之间创建信任

在 AKS 群集还需要访问 ACR 来提取容器映像并运行它们。 你允许访问 ACR 从 AKS 通过建立信任。 若要在 AKS 群集与 ACR 注册表之间建立信任，请为 AKS 群集使用的 Azure Active Directory 服务主体授予 ACR 注册表的访问权限。 以下命令授予权限的服务主体*MyAKS*中的群集*MyResourceGroup*到*MyDraftACR* ACR 中的*MyResourceGroup*。

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>连接到 AKS 群集

若要从本地计算机连接到 Kubernetes 群集，请使用[kubectl][kubectl]，Kubernetes 命令行客户端。

如果使用的是 Azure Cloud Shell，则 `kubectl` 已安装。 也可使用 [az aks install-cli][] 命令在本地安装它：

```azurecli
az aks install-cli
```

若要将 `kubectl` 配置为连接到 Kubernetes 群集，请使用 [az aks get-credentials][] 命令。 下面的示例获取名为 AKS 群集凭据*MyAKS*中*MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Helm 为创建服务帐户

在已启用 RBAC 的 AKS 群集中部署 Helm 之前，需要 Tiller 服务的服务帐户和角色绑定。 有关保护 Helm 的详细信息 / RBAC 中的 Tiller 启用群集，请参阅[Tiller、 命名空间和 RBAC][tiller-rbac]。 如果你的 AKS 群集未启用 RBAC，跳过此步骤。

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
若要将基本 Tiller 部署到 AKS 群集，请使用[helm init][helm-init]命令。 如果你的群集未启用 RBAC，删除`--service-account`参数和值。

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>配置 Draft

如果尚未在本地计算机上配置 Draft，运行`draft init`:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

此外需要将 Draft 配置为使用*loginServer*的 ACR。 下面的命令使用`draft config set`若要使用`mydraftacr.azurecr.io`为注册表。

```console
draft config set registry mydraftacr.azurecr.io
```

你已配置 Draft 来使用 ACR，并且草稿可将容器映像推送到 ACR。 当草稿在 AKS 群集中运行你的应用程序时，没有密码或机密都需要推送到或从 ACR 注册表拉取。 由于在 AKS 群集与 ACR 之间创建信任关系，在 Azure 资源管理器级别，使用 Azure Active Directory 执行身份验证。

## <a name="download-the-sample-application"></a>下载示例应用程序

本快速入门使用[草稿 GitHub 存储库中的示例 java 应用程序][example-java]。 克隆 GitHub 提供的应用程序并导航到`draft/examples/example-java/`目录。

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>使用 Draft 运行示例应用程序

使用`draft create`命令准备应用程序。

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

此命令生成 Dockerfile 以创建容器映像、 将映像推送到 ACR，并安装 Helm 图表在 AKS 中启动应用程序。 推送和拉取容器映像第一次运行此命令时，可能需要一些时间。 缓存基本层后，部署应用程序所需的时间会大大减少。

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>连接到运行示例应用程序从本地计算机

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

导航到在浏览器中使用应用程序`localhost`url 即可查看示例应用程序。 在上述示例中，url 是`http://localhost:49804`。 停止连接使用`Ctrl+c`。

## <a name="access-the-application-on-the-internet"></a>在 Internet 上访问应用程序

上一步骤与 AKS 群集中的应用程序 pod 创建了代理连接。 开发和测试应用程序时，可能需要通过 Internet 访问应用程序。 若要公开在 internet 上的应用程序，可以创建 Kubernetes 服务具有的类型[负载均衡器][kubernetes-service-loadbalancer]。

更新`charts/example-java/values.yaml`来创建*负载均衡器*服务。 更改的值*service.type*从*群集 Ip*到*负载均衡器*。

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

保存所做的更改，关闭该文件，并运行`draft up`重新运行该应用程序。

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

导航到你的应用程序在浏览器中使用的负载均衡器*外部 IP*若要查看示例应用程序。 在上述示例中，因为 IP 是`52.175.224.118`。

## <a name="iterate-on-the-application"></a>循环访问应用程序

可通过在本地更改，重新运行循环访问你的应用程序`draft up`。

更新返回的消息[行 7 src/main/java/helloworld/Hello.java][example-java-hello-l7]

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

若要查看更新的应用程序，请再次导航到你的负载均衡器的 IP 地址并验证显示了所做的更改。

## <a name="delete-the-cluster"></a>删除群集

当不再需要群集时，使用[az 组删除][az-group-delete]命令删除资源组，AKS 群集中，容器注册表、 容器映像存储在此处以及所有相关资源。

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> 删除群集时，AKS 群集使用的 Azure Active Directory 服务主体不会被删除。 有关如何删除服务主体的步骤，请参阅[AKS 服务主体的注意事项和删除][sp-delete]。

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
