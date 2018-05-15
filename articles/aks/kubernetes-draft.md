---
title: 结合使用 Draft 与 AKS 和 Azure 容器注册表
description: 结合使用 Draft 与 AKS 和 Azure 容器注册表
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/29/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: f474921acbd3a5082afea03dd982bcfa9c52dcda
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>结合使用 Draft 与 Azure Kubernetes 服务 (AKS)

Draft 是一种开源工具，有助于在 Kubernetes 群集中包含和部署这些容器，让用户专注于开发周期 -- 专注开发的“内部循环”。 在开发代码期间，但尚未将代码提交到版本控制之前，Draft 将会运行。 借助 Draft，可在代码发生更改时快速将应用程序重新部署到 Kubernetes。 有关 Draft 的详细信息，请参阅 [GitHub 上的 Draft 文档][draft-documentation]。

本文档详细介绍了如何将 Draft 与 AKS 上的 Kubernetes 群集结合使用。

## <a name="prerequisites"></a>先决条件

本文档详述的步骤假设你已创建 AKS 群集并已通过该群集建立 kubectl 连接。 如果需要这些项，请参阅 [AKS 快速入门][aks-quickstart]。

还需在 Azure 容器注册表 (ACR) 中创建专用 Docker 注册表。 有关部署 ACR 实例的说明，请参阅 [Azure 容器注册表快速入门][acr-quickstart]。

Helm 也必须安装在 AKS 群集中。 有关安装 Helm 的详细信息，请参阅[将 Helm 与 Azure Kubernetes 服务 (AKS) 配合使用][aks-helm]。

最后，必须安装 [Docker](https://www.docker.com)。

## <a name="install-draft"></a>安装 Draft

Draft CLI 是一个在开发系统上运行的客户端，可让你将代码快速部署到 Kubernetes 群集中。

> [!NOTE]
> 如果已安装低于 0.12 版本的 Draft，应该先使用 `helm delete --purge draft` 从群集中删除 Draft，然后运行 `rm -rf ~/.draft` 删除本地配置。 如果在 MacOS 上操作，请运行 `brew upgrade draft`。

若要在 Mac 上安装 Draft CLI，请使用 `brew`。 有关更多安装选项，请参阅 [Draft 安装指南][install-draft]。

```console
brew tap azure/draft
brew install draft
```

现在，使用 `draft init` 命令初始化 Draft。

```console
draft init
```

## <a name="configure-draft"></a>配置 Draft

Draft 在本地生成容器映像，然后从本地注册表部署这些映像（适用于 Minikube），或者由用户指定要使用的映像注册表。 本示例使用 Azure 容器注册表 (ACR)，因此，必须在 AKS 群集与 ACR 注册表之间建立信任关系，并将 Draft 配置为向 ACR 推送容器。

### <a name="create-trust-between-aks-cluster-and-acr"></a>在 AKS 群集与 ACR 之间创建信任

若要在 AKS 群集与 ACR 注册表之间建立信任，请将“参与者”角色添加到用于 AKS 的 Azure Active Directory 服务主体并指定 ACR 存储库的范围，以修改该服务主体。 为此，请运行以下命令，并将 _&lt;aks-rg-name&gt;_ 和 _&lt;aks-cluster-name&gt;_ 替换为 AKS 群集的资源组和名称，将 _&lt;acr-rg-nam&gt;_ 和 _&lt;acr-repo-name&gt;_ 替换为要与其建立信任关系的 ACR 存储库的资源组和存储库名称。

```console
export AKS_SP_ID=$(az aks show -g <aks-rg-name> -n <aks-cluster-name> --query "servicePrincipalProfile.clientId" -o tsv)
export ACR_RESOURCE_ID=$(az acr show -g <acr-rg-name> -n <acr-repo-name> --query "id" -o tsv)
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

（[使用 ACR 进行身份验证](../container-registry/container-registry-auth-aks.md)中介绍了这些步骤和用于访问 ACR 的其他身份验证机制。）

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>将 Draft 配置为推送到 ACR 并从中部署

在 AKS 与 ACR 之间建立信任关系后，执行以下步骤，从 AKS 群集使用 ACR。
1. 运行 `draft config set registry <registry name>.azurecr.io` 来设置 Draft 配置值 `registry`，其中，_&lt;registry name&lt;_ 是 ACR 注册表的名称。
2. 运行 `az acr login -n <registry name>` 登录到 ACR 注册表。

由于现已在本地登录到 ACR，并在 AKS 与 ACR 之间建立了信任关系，因此，无需密码或机密即可推送到 AKS，或者从 ACR 提取到 AKS。 身份验证使用 Azure Active Directory 在 Azure 资源管理器级别发生。

## <a name="run-an-application"></a>运行应用程序

Draft 存储库包括几个可用于演示 Draft 的示例应用程序。 创建存储库的克隆副本。

```console
git clone https://github.com/Azure/draft
```

更改为 Java 示例目录。

```console
cd draft/examples/example-java/
```

使用 `draft create` 命令启动进程。 此命令创建可用于在 Kubernetes 群集中运行应用程序的项目。 这些项包括一个 Dockerfile，一个 Helm 图表以及一个 `draft.toml` 文件（即 Draft 配置文件）。

```console
draft create
```

输出：

```console
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

若要在 Kubernetes 群集上运行应用程序，请使用 `draft up` 命令。 此命令生成 Dockerfile 以创建容器映像、将映像推送到 ACR，最后安装 Helm 图表以启动 AKS 中的应用程序。

首次运行此命令时，推送和提取容器映像可能需要一段时间；缓存基本层后，所用的时间会大大减少。

```console
draft up
```

输出：

```console
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (3.0007s)
example-java: Releasing Application: SUCCESS ⚓  (0.9322s)
example-java: Build ID: 01C9NPDYQQH2CZENDMZW7ESJAM
Inspect the logs with `draft logs 01C9NPDYQQH2CZENDMZW7ESJAM`
```

## <a name="test-the-application"></a>测试应用程序

若要测试应用程序，请使用 `draft connect` 命令。 此命令将代理与 Kubernetes Pod 的连接，以实现安全的本地连接。 完成后，可在提供的 URL 上访问应用程序。

在某些情况下，下载容器映像并启动应用程序可能需要几分钟时间。 如果在访问应用程序时收到错误，请重试连接。

```console
draft connect
```

输出：

```console
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

现在，可以通过浏览到 http://localhost:46143 （适用于前面的示例；你的端口可能与此不同）来测试应用程序。 应用程序测试完成后，请使用 `Control+C` 停止代理连接。

> [!NOTE]
> 还可以使用 `draft up --auto-connect` 命令来生成和部署应用程序，并立即连接到第一个运行的容器，以进一步加快迭代周期。

## <a name="expose-application"></a>公开应用程序

在 Kubernetes 中测试应用程序时，可能需要通过 Internet 访问应用程序。 这通过使用类型为 [LoadBalancer][kubernetes-service-loadbalancer] 的 Kubernetes 服务或[入口控制器][kubernetes-ingress]即可实现。 本文档详述如何使用 Kubernetes 服务。


首先，需要更新 Draft 包以指定应创建类型为 `LoadBalancer` 的服务。 为此，请更新 `values.yaml` 文件中的服务类型。

```console
vi charts/java/values.yaml
```

找到 `service.type` 属性，将值从 `ClusterIP` 更新为 `LoadBalancer`。

```yaml
replicaCount: 2
image:
  repository: openjdk
  tag: 8-jdk-alpine
  pullPolicy: IfNotPresent
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
  ```

运行 `draft up` 以重新运行应用程序。

```console
draft up
```

服务可能需要几分钟才能返回公共 IP 地址。 若要监视进度，请使用带有监视的 `kubectl get service` 命令。

```console
kubectl get service -w
```

服务的 EXTERNAL-IP 一开始显示为 `pending`。

```
example-java-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

当 EXTERNAL-IP 地址从 `pending` 变成 `IP address` 后，立即运行 `Control+C` 停止 kubectl 监视进程。

```
example-java-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
```

若要查看应用程序，请浏览到外部 IP 地址。

```console
curl 52.175.224.118
```

输出：

```
Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>循环访问应用程序

已配置 Draft 并且应用程序正在 Kubernetes 中运行，现在应设置代码迭代。 每当要测试更新的代码时，请运行 `draft up` 命令以更新运行的应用程序。

本示例更新 Java Hello World 应用程序。

```console
vi src/main/java/helloworld/Hello.java
```

更新 Hello World 文本。

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
}
```

在 pod 准备好响应后，立即运行 `draft up --auto-connect` 命令以重新部署应用程序。

```console
draft up --auto-connect
```

输出

```
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (4.0010s)
example-java: Releasing Application: SUCCESS ⚓  (1.1336s)
example-java: Build ID: 01C9NPMJP6YM985GHKDR2J64KC
Inspect the logs with `draft logs 01C9NPMJP6YM985GHKDR2J64KC`
Connect to java:4567 on localhost:39249
Your connection is still active.
Connect to java:4567 on localhost:39249
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567

```

最后，查看应用程序即可看到更新。

```console
curl 52.175.224.118
```

输出：

```
Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 Draft，请参阅 GitHub 上的 Draft 文档。

> [!div class="nextstepaction"]
> [Draft 文档][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[install-draft]: https://github.com/Azure/draft/blob/master/docs/install.md
[kubernetes-ingress]: ./ingress.md
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[aks-quickstart]: ./kubernetes-walkthrough.md
