---
title: "结合使用 Draft 与 AKS 和 Azure 容器注册表"
description: "结合使用 Draft 与 AKS 和 Azure 容器注册表"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: df5614d8a708b49ee1368c4d7983f45d29920fd8
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2017
---
# <a name="use-draft-with-azure-container-service-aks"></a>结合使用 Draft 与 Azure 容器服务 (AKS)

Draft 是一种开源工具，有助于在 Kubernetes 群集中打包和运行代码。 Draft 针对开发迭代周期；因为代码正处于开发阶段，尚未提交到版本控制。 借助 Draft，可在代码发生更改时快速将应用程序重新部署到 Kubernetes。 有关 Draft 的详细信息，请参阅 [GitHub 上的 Draft 文档](https://github.com/Azure/draft/tree/master/docs)。

本文档详细介绍了如何将 Draft 与 AKS 上的 Kubernetes 群集结合使用。

## <a name="prerequisites"></a>先决条件

本文档详述的步骤假设你已创建 AKS 群集并已通过该群集建立 kubectl 连接。 如果需要这些项，请参阅 [AKS 快速入门](./kubernetes-walkthrough.md)。

还需在 Azure 容器注册表 (ACR) 中创建专用 Docker 注册表。 有关部署 ACR 实例的说明，请参阅 [Azure 容器注册表快速入门](../container-registry/container-registry-get-started-azure-cli.md)。

## <a name="install-helm"></a>安装 Helm

Helm CLI 是一个在开发系统上运行的客户端，可让你使用 Helm 图表启动、停止和管理应用程序。

若要在 Mac 上安装 Helm CLI，请使用 `brew`。 有关更多安装选项，请参阅[安装 Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md)。

```console
brew install kubernetes-helm
```

输出：

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="install-draft"></a>安装 Draft

Draft CLI 是一个在开发系统上运行的客户端，可让你将代码快速部署到 Kubernetes 群集中。

若要在 Mac 上安装 Draft CLI，请使用 `brew`。 有关更多安装选项，请参阅 [Draft 安装指南](https://github.com/Azure/draft/blob/master/docs/install.md)。

```console
brew install draft
```

输出：

```
==> Installing draft from azure/draft
==> Downloading https://azuredraft.blob.core.windows.net/draft/draft-v0.7.0-darwin-amd64.tar.gz
Already downloaded: /Users/neilpeterson/Library/Caches/Homebrew/draft-0.7.0.tar.gz
==> /usr/local/Cellar/draft/0.7.0/bin/draft init --client-only
🍺  /usr/local/Cellar/draft/0.7.0: 6 files, 61.2MB, built in 1 second
```

## <a name="configure-draft"></a>配置 Draft

配置 Draft 时，需指定容器注册表。 本示例使用 Azure 容器注册表。

运行以下命令，获取 ACR 实例的名称和登录服务器名称。 使用包含 ACR 实例的资源组名称更新命令。

```console
az acr list --resource-group <resource group> --query "[].{Name:name,LoginServer:loginServer}" --output table
```

还需要 ACR 实例密码。

运行以下命令即可返回 ACR 密码。 使用 ACR 实例名称更新命令。

```console
az acr credential show --name <acr name> --query "passwords[0].value" --output table
```

使用 `draft init` 命令初始化 Draft。

```console
draft init
```

在此过程中，系统将提示输入容器注册表凭据。 使用 Azure 容器注册表时，注册表 URL 为 ACR 登录服务器名称，用户名为 ACR 实例名称，密码为 ACR 密码。

```console
1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): <ACR Login Server>
2. Enter your username: <ACR Name>
3. Enter your password: <ACR Password>
```

完成后，Draft 将配置到 Kubernetes 群集且随时可用。

```
Draft has been installed into your Kubernetes Cluster.
Happy Sailing!
```

## <a name="run-an-application"></a>运行应用程序

Draft 存储库包括几个可用于演示 Draft 的示例应用程序。 创建存储库的克隆副本。

```console
git clone https://github.com/Azure/draft
```

更改为 Java 示例目录。

```console
cd draft/examples/java/
```

使用 `draft create` 命令启动进程。 此命令创建可用于在 Kubernetes 群集中运行应用程序的项目。 这些项包括一个 Dockerfile，一个 Helm 图表以及一个 `draft.toml` 文件（即 Draft 配置文件）。

```console
draft create
```

输出：

```
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

若要在 Kubernetes 群集上运行应用程序，请使用 `draft up` 命令。 此命令将应用程序代码和配置文件上传到 Kubernetes 群集。 然后运行 Dockerfile 以创建容器映像、将映像推送到容器注册表，最后运行 Helm 图表启动应用程序。

```console
draft up
```

输出：

```
Draft Up Started: 'open-jaguar'
open-jaguar: Building Docker Image: SUCCESS ⚓  (28.0342s)
open-jaguar: Pushing Docker Image: SUCCESS ⚓  (7.0647s)
open-jaguar: Releasing Application: SUCCESS ⚓  (4.5056s)
open-jaguar: Build ID: 01BW3VVNZYQ5NQ8V1QSDGNVD0S
```

## <a name="test-the-application"></a>测试应用程序

若要测试应用程序，请使用 `draft connect` 命令。 此命令将代理与 Kubernetes Pod 的连接，以实现安全的本地连接。 完成后，可在提供的 URL 上访问应用程序。

在某些情况下，下载容器映像并启动应用程序可能需要几分钟时间。 如果在访问应用程序时收到错误，请重试连接。

```console
draft connect
```

输出：

```
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

应用程序测试完成后，请使用 `Control+C` 停止代理连接。

## <a name="expose-application"></a>公开应用程序

在 Kubernetes 中测试应用程序时，可能需要通过 Internet 访问应用程序。 这通过使用类型为 [LoadBalancer](https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer) 的 Kubernetes 服务或[入口控制器](https://kubernetes.io/docs/concepts/services-networking/ingress/)即可实现。 本文档详述如何使用 Kubernetes 服务。


首先，需要更新 Draft 包以指定应创建类型为 `LoadBalancer` 的服务。 为此，请更新 `values.yaml` 文件中的服务类型。

```console
vi chart/java/values.yaml
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
deadly-squid-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

当 EXTERNAL-IP 地址从 `pending` 变成 `IP address` 后，立即运行 `Control+C` 停止 kubectl 监视进程。

```
deadly-squid-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
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
        get("/", (req, res) -> "Hello World, I'm Java - Draft Rocks!");
    }
}
```

运行 `draft up` 命令以重新部署应用程序。

```console
draft up
```

输出

```
Draft Up Started: 'deadly-squid'
deadly-squid: Building Docker Image: SUCCESS ⚓  (18.0813s)
deadly-squid: Pushing Docker Image: SUCCESS ⚓  (7.9394s)
deadly-squid: Releasing Application: SUCCESS ⚓  (6.5005s)
deadly-squid: Build ID: 01BWK8C8X922F5C0HCQ8FT12RR
```

最后，查看应用程序即可看到更新。

```console
curl 52.175.224.118
```

输出：

```
Hello World, I'm Java - Draft Rocks!
```

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 Draft，请参阅 GitHub 上的 Draft 文档。

> [!div class="nextstepaction"]
> [Draft 文档](https://github.com/Azure/draft/tree/master/docs)