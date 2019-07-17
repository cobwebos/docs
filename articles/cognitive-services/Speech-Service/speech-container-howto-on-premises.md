---
title: 与 Kubernetes 和 Helm 一起使用
titleSuffix: Azure Cognitive Services
description: 使用 Kubernetes 和 Helm 定义语音到文本和文本到语音的容器映像, 我们将创建一个 Kubernetes 包。 此包将部署到本地的 Kubernetes 群集。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/16/2019
ms.author: dapine
ms.openlocfilehash: ace519d9be5945754fb79dc4c6fbb244c665d98b
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302610"
---
# <a name="use-with-kubernetes-and-helm"></a>与 Kubernetes 和 Helm 一起使用

使用 Kubernetes 和 Helm 定义语音到文本和文本到语音的容器映像, 我们将创建一个 Kubernetes 包。 此包将部署到本地的 Kubernetes 群集。 最后, 我们将探讨如何测试已部署的服务和各种配置选项。 有关在没有 Kubernetes 业务流程的情况下运行 Docker 容器的详细信息, 请参阅[安装和运行语音服务容器](speech-container-howto.md)。

## <a name="prerequisites"></a>系统必备

在本地使用语音容器之前的先决条件如下:

|必填|用途|
|--|--|
| Azure 帐户 | 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户][free-azure-account]。 |
| 容器注册表访问 | 为了使 Kubernetes 能够将 docker 映像提取到群集中, 需要访问容器注册表。 需要首先请求对[容器注册表的访问权限][speech-preview-access]。 |
| Kubernetes CLI | 从容器注册表管理共享凭据需要[KUBERNETES CLI][kubernetes-cli] 。 Helm 之前还需要 Kubernetes, 这是 Kubernetes 包管理器。 |
| Helm CLI | 作为[HELM CLI][helm-install] install, you'll also need to initialize Helm, which will install [Tiller][tiller-install]的一部分。 |
|语音资源 |若要使用这些容器，必须具有：<br><br>用于获取关联的计费密钥和计费终结点 URI 的_语音_Azure 资源。 Azure 门户的 "**语音**概述" 和 "密钥" 页上都有这两个值, 并且需要启动容器。<br><br>**{API_KEY}** : 资源键<br><br>**{ENDPOINT_URI}** : 终结点 URI 示例为:`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>建议的主机计算机配置

参考[语音服务容器主机][speech-container-host-computer]的详细信息。 此*helm 图*基于用户指定的解码 (并发请求数) 自动计算 CPU 和内存要求。 此外, 它还会根据是否将音频/文本输入的优化配置为`enabled`进行调整。 Helm 图表默认为, 即两个并发请求和禁用优化。

| 服务 | CPU/容器 | 内存/容器 |
|--|--|--|
| **语音转文本** | 一个解码器至少需要1150个 millicores。 `optimizedForAudioFile`如果已启用, 则需要 1950 millicores。 (默认值: 两个解码器) | 需要的功能:2 GB<br>少数4 GB |
| **文本转语音** | 一个并发请求至少需要500个 millicores。 `optimizeForTurboMode`如果已启用, 则需要 1000 millicores。 (默认值: 两个并发请求) | 需要的功能:1 GB<br> 少数2 GB |

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

访问 microsoft [Helm 中心][ms-helm-hub], 了解 microsoft 提供的所有公开发布的 Helm 图表。 从 Microsoft Helm 中心, 你会发现**认知服务语音的本地图表**。 **认知服务语音**是我们将要安装的图表, 但我们必须首先创建一个`config-values.yaml`具有显式配置的文件。 首先, 将 Microsoft 存储库添加到 Helm 实例。

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

接下来, 我们将配置 Helm 图表值。 将以下 YAML 复制并粘贴到名为`config-values.yaml`的文件中。 有关自定义**认知服务语音本地 Helm 图表**的详细信息, 请参阅[自定义 Helm 图表](#customize-helm-charts)。 将和`billing` `apikey`值替换为自己的值。

```yaml
# These settings are deployment specific and users can provide customizations

# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-speech-to-text
    tag: latest
    pullSecrets:
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # < Your billing URL >
      apikey: # < Your API Key >

# text-to-speech configurations
textToSpeech:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForTurboMode: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-text-to-speech
    tag: latest
    pullSecrets:
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # < Your billing URL >
      apikey: # < Your API Key >
```

> [!IMPORTANT]
> 如果未提供`apikey`和值, 则服务将在15分钟后过期。 `billing` 同样, 验证将会失败, 因为服务不可用。

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes 包 (Helm 图)

*Helm 图表*包含要从`containerpreview.azurecr.io`容器注册表中请求的 docker 映像的配置。

> [Helm 图][helm-charts]是描述一组相关 Kubernetes 资源的文件的集合。 单个图表可用于部署一些简单的操作, 例如 memcached pod 或复杂的内容, 如使用 HTTP 服务器、数据库、缓存等的完整 web 应用堆栈。

提供的*Helm 图表*从`containerpreview.azurecr.io`容器注册表中提取语音服务的 docker 图像, 包括文本到语音和语音到文本服务。

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>在 Kubernetes 群集上安装 Helm 图表

若要安装*helm 图表*, 我们需要执行[`helm install`][helm-install-cmd] `<config-values.yaml>`命令, 并将替换为相应的路径和文件名参数。 `microsoft/cognitive-services-speech-onpremise` [Microsoft Helm Hub][ms-helm-hub-speech-chart]上提供了下面引用的 Helm 图表。

```console
helm install microsoft/cognitive-services-speech-onpremise \
    --version 0.1.0 \
    --values <config-values.yaml> \
    --name onprem-speech
```

下面是你可能希望从成功的安装执行中看到的示例输出:

```console
NAME:   onprem-speech
LAST DEPLOYED: Tue Jul  2 12:51:42 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                             READY  STATUS             RESTARTS  AGE
speech-to-text-7664f5f465-87w2d  0/1    Pending            0         0s
speech-to-text-7664f5f465-klbr8  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-4jtzh  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-frwxf  0/1    Pending            0         0s

==> v1/Service
NAME            TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
speech-to-text  LoadBalancer  10.0.252.106  <pending>    80:31811/TCP  1s
text-to-speech  LoadBalancer  10.0.125.187  <pending>    80:31247/TCP  0s

==> v1beta1/PodDisruptionBudget
NAME                                MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
speech-to-text-poddisruptionbudget  N/A            20%              0                    1s
text-to-speech-poddisruptionbudget  N/A            20%              0                    1s

==> v1beta2/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
speech-to-text  0/2    2           0          0s
text-to-speech  0/2    2           0          0s

==> v2beta2/HorizontalPodAutoscaler
NAME                       REFERENCE                  TARGETS        MINPODS  MAXPODS  REPLICAS  AGE
speech-to-text-autoscaler  Deployment/speech-to-text  <unknown>/50%  2        10       0         0s
text-to-speech-autoscaler  Deployment/text-to-speech  <unknown>/50%  2        10       0         0s


NOTES:
cognitive-services-speech-onpremise has been installed!
Release is named onprem-speech
```

Kubernetes 部署可能需要几分钟才能完成。 若要确认是否正确部署了 pod 和服务, 请执行以下命令:

```console
kubectl get all
```

应会看到类似于以下输出的内容:

```console
NAME                                  READY     STATUS    RESTARTS   AGE
pod/speech-to-text-7664f5f465-87w2d   1/1       Running   0          34m
pod/speech-to-text-7664f5f465-klbr8   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-4jtzh   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-frwxf   1/1       Running   0          34m

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
service/kubernetes       ClusterIP      10.0.0.1       <none>           443/TCP        3h
service/speech-to-text   LoadBalancer   10.0.252.106   52.162.123.151   80:31811/TCP   34m
service/text-to-speech   LoadBalancer   10.0.125.187   65.52.233.162    80:31247/TCP   34m

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/speech-to-text   2         2         2            2           34m
deployment.apps/text-to-speech   2         2         2            2           34m

NAME                                        DESIRED   CURRENT   READY     AGE
replicaset.apps/speech-to-text-7664f5f465   2         2         2         34m
replicaset.apps/text-to-speech-56f8fb685b   2         2         2         34m

NAME                                                            REFERENCE                   TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/speech-to-text-autoscaler   Deployment/speech-to-text   1%/50%    2         10        2          34m
horizontalpodautoscaler.autoscaling/text-to-speech-autoscaler   Deployment/text-to-speech   0%/50%    2         10        2          34m
```

### <a name="verify-helm-deployment-with-helm-tests"></a>验证 Helm 部署和 Helm 测试

已安装的 Helm 图表定义*Helm 测试*, 用作验证的便利。 这些测试将验证服务准备情况。 若要验证**语音到文本**和**文本到语音转换**服务, 请执行[Helm test][helm-test]命令。

```console
helm test onprem-speech
```

> [!IMPORTANT]
> 如果 POD 状态不`Running`是或在`AVAILABLE`列下未列出部署, 这些测试将失败。 请耐心等待, 因为这可能需要10分钟才能完成。

这些测试将输出各种状态结果:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

作为执行*helm 测试*的替代方法, 你可以从`kubectl get all`命令收集*外部 IP*地址和相应的端口。 使用 IP 和端口, 打开 web 浏览器并导航到`http://<external-ip>:<port>:/swagger/index.html`以查看 API swagger 页面。

## <a name="customize-helm-charts"></a>自定义 Helm 图表

Helm 图表是分层的。 层次结构允许进行图表继承, 它还适用于明确的概念, 即, 更具体的重写继承规则的设置。

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

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
[speech-preview-access]: https://aka.ms/speechcontainerspreview
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
