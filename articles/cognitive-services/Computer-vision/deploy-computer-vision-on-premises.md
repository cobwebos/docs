---
title: 使用 Kubernetes 和 Helm 计算机视觉容器
titleSuffix: Azure Cognitive Services
description: 将计算机视觉容器部署到 Azure 容器实例，并在 web 浏览器中对其进行测试。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 7560f2395447e81dcd01e1d3e092b39b129b4ce3
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129827"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>使用 Kubernetes 和 Helm 计算机视觉容器

在本地管理计算机视觉容器的一种方法是使用 Kubernetes 和 Helm。 使用 Kubernetes 和 Helm 定义计算机视觉容器映像，我们将创建一个 Kubernetes 包。 此包将部署到本地的 Kubernetes 群集。 最后，我们将探讨如何测试已部署的服务。 有关在没有 Kubernetes 业务流程的情况下运行 Docker 容器的详细信息，请参阅[安装并运行计算机视觉容器](computer-vision-how-to-install-containers.md)。

## <a name="prerequisites"></a>先决条件

使用本地计算机视觉容器之前的先决条件如下：

|必填|用途|
|--|--|
| Azure 帐户 | 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户][free-azure-account]。 |
| 容器注册表访问 | 为了使 Kubernetes 能够将 docker 映像提取到群集中, 需要访问容器注册表。 需要首先请求对[容器注册表的访问权限][vision-preview-access]。 |
| Kubernetes CLI | 从容器注册表管理共享凭据需要[KUBERNETES CLI][kubernetes-cli] 。 Helm 之前还需要 Kubernetes, 这是 Kubernetes 包管理器。 |
| Helm CLI | 作为[HELM CLI][helm-install]安装的一部分，你还需要初始化 Helm，这将安装[Tiller][tiller-install]。 |
| 计算机视觉资源 |若要使用容器，必须具有：<br><br>Azure**计算机视觉**资源和关联的 API 密钥。 这两个值都可以在资源的“概述”和“密钥”页上找到，并且是启动容器所必需的。<br><br>**{API_KEY}** ："**密钥**" 页上有两个可用的资源键之一<br><br>**{ENDPOINT_URI}** ："**概述**" 页中提供的终结点|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>连接到 Kubernetes 群集

主计算机应具有可用的 Kubernetes 群集。 请参阅本教程, 了解如何[部署 Kubernetes 群集](../../aks/tutorial-kubernetes-deploy-cluster.md), 以了解如何将 Kubernetes 群集部署到主机计算机。

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>通过 Kubernetes 群集共享 Docker 凭据

若要`docker pull` `containerpreview.azurecr.io`从容器注册表中允许 Kubernetes 群集到配置的映像, 需要将 docker 凭据传输到群集。 执行以下命令, 根据容器注册表访问先决条件提供的凭据创建*docker 注册表机密。* [`kubectl create`][kubectl-create]

从所选的命令行界面运行以下命令。 请确保将`<username>`、 `<password>`和`<email-address>`替换为容器注册表凭据。

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> 如果你已有权访问`containerpreview.azurecr.io`容器注册表, 则可以改为使用一般标志创建 Kubernetes 机密。 请考虑以下针对 Docker 配置 JSON 执行的命令。
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

成功创建密钥后, 会将以下输出打印到控制台。

```console
secret "containerpreview" created
```

若要验证是否已创建密钥, 请执行[`kubectl get`][kubectl-get] `secrets`带有标志的。

```console
kuberctl get secrets
```

执行会`kubectl get secrets`打印所有配置的机密。

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>配置用于部署的 Helm 图表值

# <a name="readtabread"></a>[读取](#tab/read)

首先创建一个名为*read*的文件夹，然后将以下 YAML 内容粘贴到名为*docker-compose.override.yml*的新文件中。

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

若要配置 Helm 图表默认值，请将以下 YAML 复制并粘贴到名为`values.yaml`的文件中。 将`# {ENDPOINT_URI}` 和`# {API_KEY}`注释替换为您自己的值。

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
> 如果未提供`apikey`和值, 则服务将在15分钟后过期。 `billing` 同样, 验证将会失败, 因为服务不可用。

在 "*读取*" 目录下创建 "*模板*" 文件夹。 将以下 YAML 复制并粘贴到名为`deployment.yaml`的文件中。 该`deployment.yaml`文件将用作 Helm 模板。

> 模板可生成清单文件，这些文件是 Kubernetes 可以理解的 YAML 格式资源说明。 [-Helm 图表模板指南][chart-template-guide]

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

此模板指定负载平衡器服务，并指定要读取的容器/映像的部署。

# <a name="recognize-texttabrecognize-text"></a>[识别文本](#tab/recognize-text)

首先创建一个名为 "*文本识别器*" 的文件夹，将以下 YAML 内容复制并粘贴到名为`Chart.yml`的新文件中。

```yaml
apiVersion: v1
name: text-recognizer
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-recognize-text to a Kubernetes cluster
```

若要配置 Helm 图表默认值，请将以下 YAML 复制并粘贴到名为`values.yaml`的文件中。 将`# {ENDPOINT_URI}` 和`# {API_KEY}`注释替换为您自己的值。

```yaml
# These settings are deployment specific and users can provide customizations

recognizeText:
  enabled: true
  image:
    name: cognitive-services-recognize-text
    registry: containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-recognize-text
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> 如果未提供`apikey`和值, 则服务将在15分钟后过期。 `billing` 同样, 验证将会失败, 因为服务不可用。

在*文本识别器*目录下创建*模板*文件夹。 将以下 YAML 复制并粘贴到名为`deployment.yaml`的文件中。 该`deployment.yaml`文件将用作 Helm 模板。

> 模板可生成清单文件，这些文件是 Kubernetes 可以理解的 YAML 格式资源说明。 [-Helm 图表模板指南][chart-template-guide]

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: text-recognizer
spec:
  template:
    metadata:
      labels:
        app: text-recognizer-app
    spec:
      containers:
      - name: {{.Values.recognizeText.image.name}}
        image: {{.Values.recognizeText.image.registry}}{{.Values.recognizeText.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.recognizeText.image.args.eula}}
        - name: billing
          value: {{.Values.recognizeText.image.args.billing}}
        - name: apikey
          value: {{.Values.recognizeText.image.args.apikey}}
      imagePullSecrets:
      - name: {{.Values.recognizeText.image.pullSecret}}

--- 
apiVersion: v1
kind: Service
metadata:
  name: text-recognizer
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: text-recognizer-app
```

此模板指定负载平衡器服务以及用于文本识别的容器/映像的部署。

***

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes 包 (Helm 图)

*Helm 图表*包含要从`containerpreview.azurecr.io`容器注册表中请求的 docker 映像的配置。

> [Helm 图][helm-charts]是描述一组相关 Kubernetes 资源的文件的集合。 单个图表可用于部署一些简单的操作, 例如 memcached pod 或复杂的内容, 如使用 HTTP 服务器、数据库、缓存等的完整 web 应用堆栈。

提供的*Helm 图表*将从`containerpreview.azurecr.io`容器注册表中提取计算机视觉服务的 docker 映像和相应的服务。

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>在 Kubernetes 群集上安装 Helm 图表

# <a name="readtabread"></a>[读取](#tab/read)

若要安装*helm 图表*，需要执行[`helm install`][helm-install-cmd]命令。 确保从`read`文件夹上方的目录执行安装命令。

```console
helm install read --name read
```

下面是你可能希望从成功的安装执行中看到的示例输出:

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

Kubernetes 部署可能需要几分钟才能完成。 若要确认是否正确部署了 pod 和服务, 请执行以下命令:

```console
kubectl get all
```

应会看到类似于以下输出的内容:

```console
λ kubectl get all
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

# <a name="recognize-texttabrecognize-text"></a>[识别文本](#tab/recognize-text)

若要安装*helm 图表*，需要执行[`helm install`][helm-install-cmd]命令。 确保从`text-recognizer`文件夹上方的目录执行安装命令。

```console
helm install text-recognizer --name text-recognizer
```

下面是你可能希望从成功的安装执行中看到的示例输出:

```console
NAME:   text-recognizer
LAST DEPLOYED: Thu Aug 22 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                              READY  STATUS             RESTARTS  AGE
text-recognizer-57cb76bcf7-45sdh  0/1    ContainerCreating  0         0s

==> v1/Service
NAME             TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
text-recognizer  LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME             READY  UP-TO-DATE  AVAILABLE  AGE
text-recognizer  0/1    1           0          0s
```

Kubernetes 部署可能需要几分钟才能完成。 若要确认是否正确部署了 pod 和服务, 请执行以下命令:

```console
kubectl get all
```

应会看到类似于以下输出的内容:

```console
λ kubectl get all
NAME                                   READY   STATUS    RESTARTS   AGE
pod/text-recognizer-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes        ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/text-recognizer   LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                              READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/text-recognizer   1/1     1            1           17s

NAME                                         DESIRED   CURRENT   READY   AGE
replicaset.apps/text-recognizer-57cb76bcf7   1         1         1       17s
```

***

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>后续步骤

若要详细了解如何在 Azure Kubernetes Service (AKS) 中安装具有 Helm 的应用程序, 请[访问此处][installing-helm-apps-in-aks]。

> [!div class="nextstepaction"]
> [认知服务容器][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/helm/#helm-install
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/developing_charts
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-preview-access]: computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
