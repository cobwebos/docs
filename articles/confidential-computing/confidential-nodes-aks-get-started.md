---
title: 快速入门：使用 Azure CLI 与机密计算节点一起部署 Azure Kubernetes 服务 (AKS) 群集
description: 了解如何使用机密节点创建 AKS 群集，以及如何使用 Azure CLI 部署 Hello World 应用。
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: c8c64dadebb092d7f376fd2b6590b26f4dde0ee0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "90998309"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-using-azure-cli-preview"></a>快速入门：使用 Azure CLI 通过机密计算节点部署 Azure Kubernetes 服务 (AKS) 群集（预览）

本快速入门面向的是这样的开发人员或群集操作员，他们想要快速创建 AKS 群集并部署应用程序，从而使用 Azure 中的托管 Kubernetes 服务监视应用程序。

## <a name="overview"></a>概述

在本快速入门中，你将了解如何使用 Azure CLI 通过机密计算节点部署 Azure Kubernetes 服务 (AKS) 群集，并在 enclave 中运行 Hello World 应用程序。 AKS 是可用于快速部署和管理群集的托管式 Kubernetes 服务。 在[此处](https://docs.microsoft.com/azure/aks/intro-kubernetes)详细了解 AKS。

> [!NOTE]
> 机密计算 DCsv2 VM 使用定价较高、其可用性受区域限制的专业硬件。 有关详细信息，请查看虚拟机页面了解[可用的 SKU 和受支持的区域](virtual-machine-solutions.md)。

### <a name="deployment-pre-requisites"></a>部署先决条件

1. 具备有效的 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)帐户
1. 在部署计算机上安装并配置 Azure CLI 版本 2.0.64 或更高版本（运行  `az --version` 以查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli)
1. [aks-preview extension](https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview) 最低版本 0.4.62 
1. 订阅中最少有 6 个 DCSv2 内核可供使用。 默认情况下，每个 Azure 订阅的机密计算 VM 内核配额为 8 个内核。 如果你计划预配需要 8 个以上内核的群集，请按照[这些](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)说明创建配额增加票证

### <a name="confidential-computing-node-features"></a>机密计算节点功能

1. 仅限支持 Linux 容器的 Linux 工作器节点
1. Ubuntu 第 2 代 18.04 虚拟机
1. 基于 Intel SGX 的 CPU，其中具有加密页高速缓存 (EPC)。 在[此处](https://docs.microsoft.com/azure/confidential-computing/faq)了解详细信息
1. Kubernetes 版本 1.16 及更高版本
1. 预安装的 Intel SGX DCAP 驱动程序。 在[此处](https://docs.microsoft.com/azure/confidential-computing/faq)了解详细信息
1. 预览期间基于 CLI 的部署


## <a name="installing-the-cli-pre-requisites"></a>安装 CLI 必备组件

若要安装 aks-preview 0.4.62 扩展或更高版本，请运行以下 Azure CLI 命令：

```azurecli-interactive
az extension add --name aks-preview
az extension list
```
若要更新 aks-preview CLI 扩展，请使用以下 Azure CLI 命令：

```azurecli-interactive
az extension update --name aks-preview
```

注册 Gen2VMPreview：

```azurecli-interactive
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```
状态可能需要几分钟才显示为“已注册”。 可使用“az feature list”命令来检查注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```
当状态显示为“已注册”时，使用“az provider register”命令刷新 Microsoft.ContainerService 资源提供程序的注册：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="creating-an-aks-cluster"></a>创建 AKS 群集

如果已有满足上述要求的 AKS 群集，请[跳到现有群集部分](#existing-cluster)，添加新的机密计算节点池。

首先，使用 az group create 命令为群集创建资源组。 以下示例会在 westus2 区域创建一个名为 myResourceGroup 的资源组 ：

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

现在，使用 az aks create 命令创建 AKS 群集。 以下示例会创建具有一个节点（大小为 `Standard_DC2s_v2`）的群集。 可从[此处](https://docs.microsoft.com/azure/virtual-machines/dcv2-series)选择其他受支持的 DCsv2 SKU 的列表：

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_DC2s_v2 \
    --node-count 3 \
    --enable-addon confcom \
    --network-plugin azure \
    --vm-set-type VirtualMachineScaleSets \
    --aks-custom-headers usegen2vm=true
```
上述命令应会预配一个具有 DCSv2 节点池的新的 AKS 群集，并自动安装 2 个守护程序集（[SGX 设备插件](confidential-nodes-aks-overview.md#sgx-plugin) & [SGX 引用帮助程序](confidential-nodes-aks-overview.md#sgx-quote)）

使用 az aks get-credentials 命令获取 AKS 群集的凭据：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
使用 kubectl get pods & nodes 命令验证是否正确创建节点以及与 SGX 相关的守护程序集是否在 DCSv2 节点池上运行，如下所示：

```console
$ kubectl get pods --all-namespaces

output
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
如果输出与上述项匹配，则 AKS 群集现已准备好运行机密应用程序。

转到[来自 Enclave 的 Hello World](#hello-world) 部署部分以测试 enclave 中的应用。 或者，按照以下说明在 AKS 上添加其他节点池（AKS 支持将 SGX 节点池和非 SGX 节点池混搭使用）

>如果 DCSv2 节点池上未安装与 SGX 相关的守护程序集，请运行以下命令。

```azurecli-interactive
az aks update --enable-addons confcom --resource-group myResourceGroup --name myAKSCluster
```

## <a name="adding-confidential-computing-node-to-existing-aks-cluster"></a>将机密计算节点添加到现有 AKS 群集<a id="existing-cluster"></a>

本部分假定你已运行的 AKS 群集满足先决条件部分中列出的条件。

首先，在现有群集上启用与机密计算相关的 AKS 加载项：

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```
接下来，将 DCSv2 节点池添加到群集中

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2 --aks-custom-headers usegen2vm=true

output node pool added

Verify

az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

```console
kubectl get nodes
```
输出应会显示 AKS 群集上新添加的 confcompool1。

```console
$ kubectl get pods --all-namespaces

output (you may also see other daemonsets along SGX daemonsets as below)
kube-system     sgx-device-plugin-xxxx     1/1     Running
kube-system     sgx-quote-helper-xxxx      1/1     Running
```
如果输出与上述项匹配，则 AKS 群集现已准备好运行机密应用程序。

## <a name="hello-world-from-isolated-enclave-application"></a>来自独立 enclave 应用程序的 Hello World <a id="hello-world"></a>
创建一个名为 hello-world-enclave.yaml 的文件，并粘贴以下 YAML 清单。 可在 [Open Enclave 项目](https://github.com/openenclave/openenclave/tree/master/samples/helloworld)中找到这个基于 Open Enclave 的示例应用程序代码。

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 5 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
  ```

现在，使用 kubectl apply 命令创建一个将在 secure enclave 中启动的示例作业，如以下示例输出所示：

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

可运行以下命令来确认工作负载已成功创建了受信任的执行环境 (Enclave)：

```console
$ kubectl get jobs -l app=sgx-test
```

```console
$ kubectl get jobs -l app=sgx-test
NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test
```

```console
$ kubectl get pods -l app=sgx-test
NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test
```

```console
$ kubectl logs -l app=sgx-test
Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>清理资源

若要删除关联的节点池或删除 AKS 群集，请使用以下命令：

删除 AKS 群集
``````azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```
Removing the confidential computing node pool

``````azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
``````

## <a name="next-steps"></a>后续步骤

通过机密容器以机密的方式运行 Python 和 Node 等应用程序，方式是通过访问[机密容器示例](https://github.com/Azure-Samples/confidential-container-samples)。

通过访问 [Enclave 感知 Azure 容器示例](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/)来运行 Enclave 感知应用程序。



