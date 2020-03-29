---
title: 将计算机视觉容器与库伯奈斯和赫尔姆一起使用
titleSuffix: Azure Cognitive Services
description: 将计算机视觉容器部署到 Azure 容器实例，并在 Web 浏览器中测试它。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: 126060875c09d70b8680447d78b7cf6ccdd782af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458012"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>将计算机视觉容器与库伯奈斯和赫尔姆一起使用

本地管理计算机视觉容器的一个选项是使用库伯内特斯和赫尔姆。 使用 Kubernetes 和 Helm 定义计算机视觉容器映像，我们将创建一个库伯奈斯包。 此包将部署到本地 Kubernetes 群集。 最后，我们将探讨如何测试已部署的服务。 有关在没有 Kubernetes 业务流程的情况下运行 Docker 容器的详细信息，请参阅[安装和运行计算机视觉容器](computer-vision-how-to-install-containers.md)。

## <a name="prerequisites"></a>先决条件

在本地使用计算机视觉容器之前，以下先决条件：

| 必选 | 目的 |
|----------|---------|
| Azure 帐户 | 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户][free-azure-account]。 |
| Kubernetes CLI | 需要使用 [Kubernetes CLI][kubernetes-cli] 来管理容器注册表中的共享凭据。 在安装 Helm（Kubernetes 包管理器）之前，也需要有 Kubernetes。 |
| Helm CLI | 安装[Helm CLI][helm-install]，用于安装掌舵图（容器包定义）。 |
| 计算机视觉资源 |若要使用容器，必须具有：<br><br>Azure**计算机视觉**资源和关联的 API 键端点 URI。 这两个值都可以在资源的“概述”和“密钥”页上找到，并且是启动容器所必需的。<br><br>**[API_KEY]**：**密钥**页上的两个可用资源键之一<br><br>**[ENDPOINT_URI]**：**概述**页上提供的终结点|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>连接到 Kubernetes 群集

主机预期有一个可用的 Kubernetes 群集。 请参阅这篇有关[部署 Kubernetes 群集](../../aks/tutorial-kubernetes-deploy-cluster.md)的教程，对如何将 Kubernetes 群集部署到主机有一个概念性的了解。

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>与 Kubernetes 群集共享 Docker 凭据

若要允许 Kubernetes 群集对 `containerpreview.azurecr.io` 容器注册表中配置的映像执行 `docker pull`，需将 Docker 凭据传输到群集中。 执行下面的[`kubectl create`][kubectl-create]命令，根据从容器注册表访问先决条件提供的凭据创建*Docker 注册表密钥*。

在所选的命令行接口中运行以下命令。 请务必将 `<username>`、`<password>` 和 `<email-address>` 替换为容器注册表凭据。

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> 如果你已有权访问 `containerpreview.azurecr.io` 容器注册表，则可以改用常规标志创建 Kubernetes 机密。 考虑针对 Docker 配置 JSON 执行以下命令。
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

成功创建机密后，控制台中会列显以下输出。

```console
secret "containerpreview" created
```

要验证已创建机密，请使用[`kubectl get`][kubectl-get]`secrets`标志执行 。

```console
kubectl get secrets
```

执行 `kubectl get secrets` 会列显所有已配置的机密。

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>配置用于部署的 Helm 图表值

首先创建名为*read*的文件夹，然后将以下 YAML 内容粘贴到名为*Chart.yml*的新文件中。

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

要配置 Helm 图表默认值，请将以下 YAML 复制并粘贴到名为`values.yaml`的文件中。 请将 `# {ENDPOINT_URI}` 和 `# {API_KEY}` 注释替换为自己的值。

```yaml
# These settings are deployment specific and users can provide customizations

read:
  enabled: true
  image:
    name: cognitive-services-read
    registry: containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-read
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> 如果未提供 `billing` 和 `apikey` 值，服务将在 15 分钟后过期。 同样，由于服务不可用，验证将会失败。

在*读取*目录下创建*模板*文件夹。 将以下 YAML 复制并粘贴到名为 `deployment.yaml` 的文件。 该文件`deployment.yaml`将用作 Helm 模板。

> 模板生成清单文件，这是 Kubernetes 可以理解的 YAML 格式的资源描述。 [- 头盔图模板指南][chart-template-guide]

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: read
spec:
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}

--- 
apiVersion: v1
kind: Service
metadata:
  name: read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

该模板指定负载均衡器服务以及用于读取的容器/映像的部署。

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes 包（Helm 图表）

Helm 图表包含要从 `containerpreview.azurecr.io` 容器注册表提取的 Docker 映像的配置。**

> [Helm 图表][helm-charts]是描述一组相关 Kubernetes 资源的文件集合。 单个图表既可用于部署简单的资源（例如 Memcached Pod），也可用于部署复杂的资源（例如，包含 HTTP 服务器、数据库、缓存等的完整 Web 应用堆栈）。

提供的*Helm 图表*从`containerpreview.azurecr.io`容器注册表中提取计算机视觉服务的 Docker 映像和相应的服务。

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>在 Kubernetes 群集上安装 Helm 图表

要安装*掌舵图*，我们需要执行该[`helm install`][helm-install-cmd]命令。 确保从`read`文件夹上方的目录中执行安装命令。

```console
helm install read ./read
```

下面是成功执行安装命令后预期会看到的示例输出：

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

Kubernetes 部署可能需要几分钟才能完成。 若要确认 Pod 和服务是否已正确部署且可用，请执行以下命令：

```console
kubectl get all
```

预期会看到类似于以下输出的内容：

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```
<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>后续步骤

有关在 Azure Kubernetes 服务 (AKS) 中使用 Helm 安装应用程序的更多详细信息，请[访问此页][installing-helm-apps-in-aks]。

> [!div class="nextstepaction"]
> [认知服务容器][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
