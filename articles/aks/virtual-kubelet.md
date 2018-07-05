---
title: 在 Azure Kubernetes 服务 (AKS) 群集中运行虚拟 kubelet
description: 使用虚拟 kubelet 在 Azure 容器实例上运行 Kubernetes 容器。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/12/2018
ms.author: iainfou
ms.openlocfilehash: 04fdb1620dc6e7147ed10ae6eeeaeb3eeae14b62
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097353"
---
# <a name="virtual-kubelet-with-aks"></a>带有 AKS 的虚拟 Kubelet

Azure 容器实例 (ACI) 提供托管环境，以便在 Azure 中运行容器。 使用 ACI 时，无需管理基础计算基础结构，Azure 会处理此管理。 在 ACI 中运行容器时，每个正在运行的容器将按秒收费。

在 Azure 容器实例中使用虚拟 Kubelet 提供程序时，可以在容器实例上安排 Linux 和 Windows 容器，就像容器实例是一个标准的 Kubernetes 节点一样。 此配置允许你利用 Kubernetes 的功能以及容器实例的管理价值和成本优势。

> [!NOTE]
> 虚拟 Kubelet 是实验性开放源代码项目，并且应该这样使用。 若要参与问题讨论、提交问题以及阅读有关虚拟 kubelet 的详细信息，请参阅[虚拟 Kubelet GitHub 项目][vk-github]。

本文档将详细介绍如何在 AKS 上为容器实例配置虚拟 Kubelet。

## <a name="prerequisite"></a>先决条件

本文档假定你有 AKS 群集。 如果你需要 AKS 群集，请参阅 [Azure Kubernetes 服务 (AKS) 快速入门][aks-quick-start]。

还需要 Azure CLI 版本 2.0.33 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

为了安装虚拟 Kubelet，还需要 [Helm](https://docs.helm.sh/using_helm/#installing-helm)。

## <a name="installation"></a>安装

使用 [az aks install-connector][aks-install-connector] 命令安装虚拟 Kubelet。 以下示例将部署 Linux 和 Windows 连接器。

```azurecli-interactive
az aks install-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet --os-type Both
```

这些参数可用于 `aks install-connector` 命令。

| 参数： | 说明 | 必选 |
|---|---|:---:|
| `--connector-name` | ACI 连接器的名称。| 是 |
| `--name` `-n` | 托管群集的名称。 | 是 |
| `--resource-group` `-g` | 资源组的名称。 | 是 |
| `--os-type` | 容器实例操作系统类型。 允许的值：Linux、Windows 或两者。 默认值：Linux。 | 否 |
| `--aci-resource-group` | 要在其中创建 ACI 容器组的资源组。 | 否 |
| `--location` `-l` | 要创建 ACI 容器组的位置。 | 否 |
| `--service-principal` | 用于对 Azure API 进行身份验证的服务主体。 | 否 |
| `--client-secret` | 与服务主体关联的机密。 | 否 |
| `--chart-url` | 安装 ACI 连接器的 Helm 图表的 URL。 | 否 |
| `--image-tag` | 虚拟 kubelet 容器映像的映像标记。 | 否 |

## <a name="validate-virtual-kubelet"></a>验证虚拟 Kubelet

若要验证已安装虚拟 Kubelet，请使用 [kubectl get nodes][kubectl-get] 命令返回 Kubernetes 节点的列表。

```console
$ kubectl get nodes

NAME                                    STATUS    ROLES     AGE       VERSION
aks-nodepool1-23443254-0                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-1                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-2                Ready     agent     16d       v1.9.6
virtual-kubelet-virtual-kubelet-linux   Ready     agent     4m        v1.8.3
virtual-kubelet-virtual-kubelet-win     Ready     agent     4m        v1.8.3
```

## <a name="run-linux-container"></a>运行 Linux 容器

创建名为 `virtual-kubelet-linux.yaml` 的文件，并将其复制到以下 YAML 中。 将 `kubernetes.io/hostname` 值替换为 Linux 虚拟 Kubelet 节点的名称。 请注意，正在使用 [nodeSelector][node-selector] 和 [toleration][toleration] 来计划节点上的容器。

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-linux
      tolerations:
      - key: azure.com/aci
        effect: NoSchedule
```

使用 [kubectl create][kubectl-create] 命令运行该应用程序。

```azurecli-interactive
kubectl create -f virtual-kubelet-linux.yaml
```

使用带有 `-o wide` 参数的 [kubectl get pods][kubectl-get] 命令输出具有计划节点的 pod 列表。 请注意，已在 `virtual-kubelet-virtual-kubelet-linux` 节点上计划 `aci-helloworld` pod。

```console
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
aci-helloworld-2559879000-8vmjw     1/1       Running   0          39s       52.179.3.180   virtual-kubelet-virtual-kubelet-linux
```

## <a name="run-windows-container"></a>运行 Windows 容器

创建名为 `virtual-kubelet-windows.yaml` 的文件，并将其复制到以下 YAML 中。 将 `kubernetes.io/hostname` 值替换为 Windows 虚拟 Kubelet 节点的名称。 请注意，正在使用 [nodeSelector][node-selector] 和 [toleration][toleration] 来计划节点上的容器。

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: nanoserver/iis
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-win
      tolerations:
      - key: azure.com/aci
        effect: NoSchedule
```

使用 [kubectl create][kubectl-create] 命令运行该应用程序。

```azurecli-interactive
kubectl create -f virtual-kubelet-windows.yaml
```

使用带有 `-o wide` 参数的 [kubectl get pods][kubectl-get] 命令输出具有计划节点的 pod 列表。 请注意，已在 `virtual-kubelet-virtual-kubelet-win` 节点上计划 `nanoserver-iis` pod。

```console
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
nanoserver-iis-868bc8d489-tq4st     1/1       Running   8         21m       138.91.121.91   virtual-kubelet-virtual-kubelet-win
```

## <a name="remove-virtual-kubelet"></a>删除虚拟 Kubelet

使用 [az aks remove-connector][aks-remove-connector] 命令删除虚拟 Kubelet。 将参数值替换为连接器、AKS 群集和 AKS 群集资源组的名称。

```azurecli-interactive
az aks remove-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet
```

## <a name="next-steps"></a>后续步骤

在[虚拟 Kubelet Github 项目][vk-github]上阅读有关虚拟 Kubelet 的详细信息。

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az_aks_list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
