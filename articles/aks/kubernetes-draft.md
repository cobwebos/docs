---
title: 结合使用 Draft 与 AKS 和 Azure 容器注册表
description: 结合使用 Draft 与 AKS 和 Azure 容器注册表
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: iainfou
ms.openlocfilehash: 5169b8856155df5e62b1e85d291ce6c1b54c8a87
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011941"
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>结合使用 Draft 与 Azure Kubernetes 服务 (AKS)

Draft 是一种开源工具，有助于在 Kubernetes 群集中打包和部署应用程序容器，让你专注于开发周期 - 专注开发的“内部循环”。 在开发代码期间，但尚未将代码提交到版本控制之前，Draft 将会运行。 借助 Draft，可在代码发生更改时快速将应用程序重新部署到 Kubernetes。 有关 Draft 的详细信息，请参阅 [GitHub 上的 Draft 文档][draft-documentation]。

本文介绍如何对 AKS 上的 Kubernetes 群集使用 Draft。

## <a name="prerequisites"></a>必备组件

本文中详述的步骤假设已创建 AKS 群集并已与该群集建立 `kubectl` 连接。 如果需要这些项，请参阅 [AKS 快速入门][aks-quickstart]。

Azure 容器注册表 (ACR) 中需有一个专用 Docker 注册表。 有关创建 ACR 实例的步骤，请参阅 [Azure 容器注册表快速入门][acr-quickstart]。

Helm 也必须安装在 AKS 群集中。 有关如何安装和配置 Helm 的详细信息，请参阅[将 Helm 与 Azure Kubernetes 服务 (AKS) 配合使用][aks-helm]。

最后，必须安装 [Docker](https://www.docker.com)。

## <a name="install-draft"></a>安装 Draft

Draft CLI 是一个在开发系统上运行的客户端，可将代码部署到 Kubernetes 群集中。 若要在 Mac 上安装 Draft CLI，请使用 `brew`。 有关其他安装选项，请参阅 [Draft 安装指南][draft-documentation]。

> [!NOTE]
> 如果已安装低于 0.12 版本的 Draft，请使用 `helm delete --purge draft` 从群集中删除 Draft，然后运行 `rm -rf ~/.draft` 删除本地配置。 如果在 MacOS 上操作，请运行 `brew upgrade draft`。

```console
brew tap azure/draft
brew install draft
```

现在，使用 `draft init` 命令初始化 Draft：

```console
draft init
```

## <a name="configure-draft"></a>配置 Draft

Draft 在本地生成容器映像，然后从本地注册表部署这些映像（例如使用 Minikube），或使用指定的映像注册表。 本文使用 Azure 容器注册表 (ACR)，因此，必须在 AKS 群集与 ACR 注册表之间建立信任关系，然后将 Draft 配置为向 ACR 推送容器映像。

### <a name="create-trust-between-aks-cluster-and-acr"></a>在 AKS 群集与 ACR 之间创建信任

若要在 AKS 群集与 ACR 注册表之间建立信任，请为 AKS 群集使用的 Azure Active Directory 服务主体授予 ACR 注册表的访问权限。 在以下命令中，请提供自己的 `<resourceGroupName>`，将 `<aksName>` 替换为 AKS 群集的名称，将 `<acrName>` 替换为 ACR 注册表的名称：

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group <resourceGroupName> --name <aksName> --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group <resourceGroupName> --name <acrName> --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

有关访问 ACR 的步骤的详细信息，请参阅[使用 ACR 进行身份验证](../container-registry/container-registry-auth-aks.md)。

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>将 Draft 配置为推送到 ACR 并从中部署

在 AKS 与 ACR 之间建立信任关系后，从 AKS 群集启用 ACR。

1. 设置 Draft 配置注册表值。 在以下命令中，请将 `<acrName>` 替换为 ACR 注册表的名称：

    ```console
    draft config set registry <acrName>.azurecr.io
    ```

1. 使用 [az acr login][az-acr-login] 登录到 ACR 注册表：

    ```azurecli
    az acr login --name <acrName>
    ```

由于已在 AKS 与 ACR 之间创建信任，因此，无需密码或机密即可在 ACR 注册表中推送或提取数据。 身份验证使用 Azure Active Directory 在 Azure 资源管理器级别发生。

## <a name="run-an-application"></a>运行应用程序

为了了解 Draft 的运作方式，让我们部署 [Draft 存储库][draft-repo]中的一个示例应用程序。 首先克隆存储库：

```console
git clone https://github.com/Azure/draft
```

切换到 Java 示例目录：

```console
cd draft/examples/example-java/
```

使用 `draft create` 命令启动进程。 此命令创建可用于在 Kubernetes 群集中运行应用程序的项目。 这些项包括 Dockerfile、Helm 图表和 *draft.toml* 文件（Draft 配置文件）。

```
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

若要在 AKS 群集中运行该示例应用程序，请使用 `draft up` 命令。 此命令生成 Dockerfile 以创建容器映像、将映像推送到 ACR，最后安装 Helm 图表以启动 AKS 中的应用程序。

首次运行此命令时，推送和提取容器映像可能需要一段时间。 缓存基本层后，部署应用程序所需的时间会大大减少。

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

如果在推送 Docker 映像时遇到问题，请确保使用 [az acr login][az-acr-login] 成功登录到 ACR 注册表，然后重试 `draft up` 命令。

## <a name="test-the-application-locally"></a>在本地测试应用程序

若要测试应用程序，请使用 `draft connect` 命令。 此命令将代理与 Kubernetes pod 之间的安全连接。 完成后，可在提供的 URL 上访问应用程序。

> [!NOTE]
> 下载容器映像并启动应用程序可能需要几分钟时间。 如果在访问应用程序时收到错误，请重试连接。

```
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

若要访问你的应用程序，请打开 web 浏览器的地址和端口中指定`draft connect`输出，如`http://localhost:49804`。 

![使用 Draft 运行的示例 Java 应用](media/kubernetes-draft/sample-app.png)

使用 `Control+C` 停止代理连接。

> [!NOTE]
> 还可以使用 `draft up --auto-connect` 命令来生成和部署应用程序，然后立即连接到第一个运行的容器。

## <a name="access-the-application-on-the-internet"></a>在 Internet 上访问应用程序

上一步骤与 AKS 群集中的应用程序 pod 创建了代理连接。 开发和测试应用程序时，可能需要通过 Internet 访问应用程序。 若要在 Internet 上公开应用程序，请创建类型为 [LoadBalancer][kubernetes-service-loadbalancer] 的 Kubernetes 服务，或创建[入口控制器][kubernetes-ingress]。 让我们创建一个 *LoadBalancer* 服务。

首先，更新 *values.yaml* Draft 包，以指定应创建类型为 *LoadBalancer* 的服务：

```console
vi charts/java/values.yaml
```

找到 *service.type* 属性，并将值从 *ClusterIP* 更新为 *LoadBalancer*，如以下精简示例中所示：

```yaml
[...]
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
[...]
```

保存并关闭该文件，然后使用 `draft up` 重新运行该应用程序：

```console
draft up
```

服务可能需要几分钟才能返回公共 IP 地址。 若要监视进度，请结合 *watch* 参数使用 `kubectl get service` 命令：

```console
kubectl get service --watch
```

服务的 *EXTERNAL-IP* 最初显示为 *pending*：

```
NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
```

EXTERNAL-IP 地址从 *pending* 更改为为某个 IP 地址后，请使用 `Control+C` 停止 `kubectl` 监视进程：

```
NAME                TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

若要查看应用程序，请使用 `curl` 浏览到负载均衡器的外部 IP 地址：

```
$ curl 52.175.224.118

Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>循环访问应用程序

已配置 Draft 并且应用程序正在 Kubernetes 中运行，现在应设置代码迭代。 每当要测试更新的代码时，请运行 `draft up` 命令来更新正在运行的应用程序。

此示例更新 Java 示例应用程序以更改显示文本。 打开 *Hello.java* 文件：

```console
vi src/main/java/helloworld/Hello.java
```

更新要显示的输出文本 *Hello World, I'm Java in AKS!*：

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
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

若要查看更新的应用程序，请再次 curl 负载均衡器的 IP 地址：

```
$ curl 52.175.224.118

Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 Draft，请参阅 GitHub 上的 Draft 文档。

> [!div class="nextstepaction"]
> [Draft 文档][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[draft-repo]: https://github.com/Azure/draft

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[kubernetes-ingress]: ./ingress-basic.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[az-acr-login]: /cli/azure/acr#az-acr-login
