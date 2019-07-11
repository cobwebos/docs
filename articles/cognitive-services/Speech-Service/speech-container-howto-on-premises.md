---
title: 使用 Kubernetes 部署
titleSuffix: Azure Cognitive Services
description: 使用 Kubernetes 和 Helm 定义语音转文本和文本到语音转换的容器映像，我们将创建 Kubernetes 包。 此包将部署到 Kubernetes 群集的本地。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/10/2019
ms.author: dapine
ms.openlocfilehash: 33d9de956a6d43145fc68f4ec46b09b8e8bf0188
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786243"
---
# <a name="use-kubernetes-on-premises"></a>使用 Kubernetes 部署

使用 Kubernetes 和 Helm 定义语音转文本和文本到语音转换的容器映像，我们将创建 Kubernetes 包。 此包将部署到 Kubernetes 群集的本地。 最后，我们将探讨如何测试已部署的服务和各种配置选项。

## <a name="prerequisites"></a>先决条件

使用语音容器本地之前，必须满足以下先决条件：

|需要|用途|
|--|--|
| Azure 帐户 | 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户][free-azure-account]。 |
| 容器注册表访问权限 | 为了使 Kubernetes 拉取到群集的 docker 映像，它将需要对容器注册表的访问。 你需要[请求对容器注册表][speech-preview-access]第一个。 |
| Kubernetes CLI | [Kubernetes CLI][kubernetes-cli]需要从容器注册表管理共享的凭据。 Helm 是 Kubernetes 包管理器之前，也需要 Kubernetes。 |
| Helm CLI | 作为的一部分[Helm CLI][helm-install] install, you'll also need to initialize Helm which will install [Tiller][tiller-install]。 |
|语音资源 |若要使用这些容器，必须具有：<br><br>一个_语音_Azure 资源以获取对关联的帐单密钥和计费终结点 URI。 这两个值都出现在 Azure 门户**语音**概述和密钥页和是否需要启动该容器。<br><br>**{API_KEY}** ： 资源键<br><br>**{ENDPOINT_URI}** ： 终结点的 URI 示例是： `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>建议的主机计算机配置

请参阅[语音服务容器主计算机][speech-container-host-computer]作为参考的详细信息。 这*helm 图表*自动计算基于多少解码 （并发请求数） 的 CPU 和内存要求用户指定的。 此外，它将调整音频/文本输入的优化是否配置为根据`enabled`。 Helm 图表到的默认值、 两个并发请求和禁用优化。

| 服务 | CPU / 容器 | 内存 / 容器 |
|--|--|--|
| **语音转文本** | 1,150 millicores 至少需要一个解码器。 如果`optimizedForAudioFile`启用，则需 1,950 millicores。 (默认： 两个解码器) | 需要的功能:2 GB<br>限制：4 GB |
| **文本转语音** | 500 millicores 至少需要一个并发请求。 如果`optimizeForTurboMode`启用，则需 1,000 millicores。 (默认： 两个并发请求) | 需要的功能:1 GB<br> 限制：2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>连接到 Kubernetes 群集

在主计算机都必须具有一个可用的 Kubernetes 群集。 查看此教程[部署 Kubernetes 群集](../../aks/tutorial-kubernetes-deploy-cluster.md)概念理解如何将 Kubernetes 群集部署到主机计算机。

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>与 Kubernetes 群集共享 Docker 凭据

若要允许到 Kubernetes 群集`docker pull`从已配置的映像包`containerpreview.azurecr.io`容器注册表，你需要将 docker 凭据传输到群集。 执行[ `kubectl create` ][kubectl-create]以下命令以创建*docker 注册表机密*基于从容器注册表访问系统必备组件提供的凭据。

从所选的命令行界面，运行以下命令。 确保替换`<username>`， `<password>`，和`<email-address>`使用容器注册表凭据。

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> 如果已有权访问`containerpreview.azurecr.io`容器注册表，你可以创建改为使用泛型标志的 Kubernetes 机密。 请考虑以下针对你的 Docker 配置 JSON 执行的命令。
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

已成功创建机密时，以下输出被打印到控制台。

```console
secret "containerpreview" created
```

若要验证是否已创建密钥，请执行[ `kubectl get` ][kubectl-get]与`secrets`标志。

```console
kuberctl get secrets
```

执行`kubectl get secrets`打印所有已配置的机密。

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>配置部署的 Helm 图表值

请访问[Microsoft Helm 中心][ms-helm-hub]由 Microsoft 提供的所有公开可用的 helm 图表。 从 Microsoft Helm 中心，您会发现**认知服务语音的本地图表**。 **认知服务语音在本地**是图，我们将安装，但我们必须先创建`config-values.yaml`的显式配置的文件。 让我们首先将 Microsoft 存储库添加到我们 Helm 实例。

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

接下来，我们将配置我们的 Helm 图表值。 复制并粘贴到名为的文件的以下 YAML `config-values.yaml`。 有关自定义的详细信息**认知服务语音的本地 Helm 图表**，请参阅[自定义 helm 图表](#customize-helm-charts)。 替换`billing`和`apikey`用您自己的值。

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
> 如果`billing`和`apikey`未提供值，服务将在 15 分钟后过期。 同样，验证将失败，因为服务将不可用。

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes 包 （Helm 图表）

*Helm 图表*包含可供抽取的 docker 映像的配置`containerpreview.azurecr.io`容器注册表。

> 一个[Helm 图表][helm-charts]是描述一组相关的 Kubernetes 资源文件的集合。 可能使用的单个图表部署简单，如 memcached pod，或复杂，像这样的与 HTTP 服务器、 数据库、 缓存和等等的完整 web 应用程序堆栈。

提供*Helm 图表*拉取 docker 映像的语音服务，文本转语音和语音文本服务从`containerpreview.azurecr.io`容器注册表。

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>在 Kubernetes 群集上安装 Helm 图表

若要安装*helm 图表*我们将需要执行[ `helm install` ][helm-install-cmd]命令，用`<config-values.yaml>`与相应的路径和文件名称自变量。 `microsoft/cognitive-services-speech-onpremise`上提供了下面引用的 Helm 图表[Microsoft Helm 中心][ms-helm-hub-speech-chart]。

```console
helm install microsoft/cognitive-services-speech-onpremise \
    --version 0.1.0 \
    --values <config-values.yaml> \
    --name onprem-speech
```

下面是你可能希望看到成功安装执行的示例输出：

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

Kubernetes 部署可能需要几分钟时间才能完成。 若要确认 pod 和服务已正确部署并且可用，请执行以下命令：

```console
kubectl get all
```

你应该会看到类似于以下输出内容：

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

### <a name="verify-helm-deployment-with-helm-tests"></a>使用 Helm 测试验证 Helm 部署

安装的 Helm 图表定义*Helm 测试*，它作为方便了验证。 这些测试验证服务就绪。 若要验证这两**语音到文本**并**文本到语音转换**services，我们将执行[Helm 测试][helm-test]命令。

```console
helm test onprem-speech
```

> [!IMPORTANT]
> 如果 POD 状态不是，这些测试将失败`Running`或如果未列出部署`AVAILABLE`列。 请耐心等待，因为这可能需要超过 10 分钟才能完成。

这些测试将各种状态结果输出：

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

作为执行的替代方法*helm 测试*，可收集*外部 IP*地址和相应的端口从`kubectl get all`命令。 使用的 IP 和端口，打开 web 浏览器并导航到`http://<external-ip>:<port>:/swagger/index.html`到 API swagger 页面的视图。

## <a name="customize-helm-charts"></a>自定义 Helm 图表

Helm 图表是分层的。 在层次结构允许对图表继承，它还适用于特异性排序，其中更具体的设置将覆盖继承的规则的概念。

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

## <a name="next-steps"></a>后续步骤

有关使用 Helm 在 Azure Kubernetes 服务 (AKS) 中，安装应用程序的详细信息[访问此处][installing-helm-apps-in-aks]。

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
