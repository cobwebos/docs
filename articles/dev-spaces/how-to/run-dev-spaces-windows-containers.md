---
title: 与 Windows 容器交互
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: 了解如何使用 Windows 容器在现有群集上运行 Azure 开发人员空间
keywords: Azure 开发空间、开发空间、Docker、库伯奈斯、Azure、AKS、Azure 库伯奈斯服务、容器、Windows 容器
ms.openlocfilehash: 0b3f221c9e62343a02ba8742e4cf988c7cf26c12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240480"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>使用 Azure 开发空间与 Windows 容器交互

您可以在新的 Kubernetes 命名空间上启用 Azure 开发空间。 Azure 开发空间将运行并检测在 Linux 容器上运行的服务。 这些服务还可以与在同一命名空间中的 Windows 容器上运行的应用程序进行交互。 本文介绍如何使用开发人员空间在现有 Windows 容器的命名空间中运行服务。 此时，您无法调试或附加到具有 Azure 开发空间的 Windows 容器。

## <a name="set-up-your-cluster"></a>设置群集

本文假定您已经有一个同时具有 Linux 和 Windows 节点池的群集。 如果需要创建具有 Linux 和 Windows 节点池的群集，可以[按照此处][windows-container-cli]的说明操作。

使用库贝内特斯命令行客户端[库布埃特尔][kubectl]连接到群集。 若要将 `kubectl` 配置为连接到 Kubernetes 群集，请使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令将下载凭据，并将 Kubernetes CLI 配置为使用这些凭据。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

若要验证到群集的连接，请使用 [kubectl get][kubectl-get] 命令返回群集节点的列表。

```azurecli-interactive
kubectl get nodes
```

以下示例输出显示了同时具有 Windows 和 Linux 节点的群集。 在继续操作之前，请确保每个节点的状态*已就绪*。

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

对 Windows 节点应用[污点][using-taints]。 Windows 节点上的污点阻止开发人员空间将 Linux 容器安排在 Windows 节点上运行。 以下命令示例命令对上一个示例中的*aksnpwin987654* Windows 节点应用污点。

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> 将污点应用于节点时，必须在服务的部署模板中配置匹配的容忍，才能在该节点上运行服务。 示例应用程序已配置了与上一命令中配置的污点[匹配的容忍][sample-application-toleration-example]。

## <a name="run-your-windows-service"></a>运行 Windows 服务

在 AKS 群集上运行 Windows 服务，并验证其处于 *"正在运行"* 状态。 本文使用[示例应用程序][sample-application]来演示群集上运行的 Windows 和 Linux 服务。

从 GitHub 克隆示例应用程序并导航到`dev-spaces/samples/existingWindowsBackend/mywebapi-windows`目录中：

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

示例应用程序使用[Helm 3][helm-installed]在群集上运行 Windows 服务。 导航到`charts`目录并使用 Helm 运行 Windows 服务：

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

上述命令使用 Helm 在*开发命名*空间中运行 Windows 服务。 如果没有名为*dev*的命名空间，它将创建它。

使用`kubectl get pods`命令验证您的 Windows 服务在群集中运行。 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>启用 Azure 开发空间

在用于运行 Windows 服务的相同命名空间中启用开发空间。 以下命令支持*开发空间中的开发*空间：

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>更新开发空间的 Windows 服务

默认情况下，当您在现有命名空间上启用 Dev Spaces 时，该容器已运行，默认情况下，开发人员空间将尝试并检测该命名空间中运行的任何新容器。 开发空间还将尝试检测为已在命名空间中运行的服务创建的任何新容器。 为防止开发人员空间检测在命名空间中运行的容器，请将*非代理*标头添加到 。 `deployment.yaml`

添加到`azds.io/no-proxy: "true"``existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml`文件：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

用于`helm list`列出 Windows 服务的部署：

```cmd
$ helm list --namespace dev
NAME              REVISION  UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
windows-service 1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

在上面的示例中，部署的名称是*窗口服务*。 使用 更新 Windows 服务，使用`helm upgrade`：

```cmd
helm upgrade windows-service . --namespace dev
```

由于您更新了`deployment.yaml`，开发空间将不会尝试并检测您的服务。

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>使用 Azure 开发空间运行 Linux 应用程序

导航到目录`webfrontend`，并使用`azds prep`和`azds up`命令在群集上运行 Linux 应用程序。

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

该`azds prep --enable-ingress`命令为您的应用程序生成 Helm 图表和 Dockerfile。

> [!TIP]
> Azure Dev Spaces 使用项目的 [Dockerfile 和 Helm 图表](../how-dev-spaces-works-prep.md#prepare-your-code)来生成和运行代码，但是如果要更改项目的生成和运行方式，则可以修改这些文件。

该`azds up`命令在命名空间中运行服务。

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

您可以通过打开公共 URL 来查看服务运行，该 URL 显示在 azds up 命令的输出中。 在此示例中，公共 URL 为 `http://dev.webfrontend.abcdef0123.eus.azds.io/`。 在浏览器中导航到服务，然后单击"*关于*顶部"。 验证您看到来自*mywebapi*服务的消息，其中包含容器正在使用的 Windows 版本。

![示例应用，显示来自 mywebapi 的 Windows 版本](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>后续步骤

了解 Azure Dev Spaces 如何帮助开发跨多个容器的更复杂应用程序，以及如何通过在不同的空间中使用不同的代码版本或分支来简化协作式开发。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 中的团队开发][team-development-qs]

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[team-development-qs]: ../quickstart-team-development.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[team-development]: ../team-development-netcore.md
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md
