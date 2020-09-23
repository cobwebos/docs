---
title: '使用 Azure 数据 CLI 创建数据控制器 (azdata) '
description: 在已使用 Azure 数据 CLI (azdata) 创建的典型多节点 Kubernetes 群集上创建 Azure Arc 数据控制器。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 8651921dafd5727f79e4ab3919e188d4b8612465
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934625"
---
# <a name="create-azure-arc-data-controller-using-the-azure-data-cli-azdata"></a>使用 Azure 数据 CLI (azdata 创建 Azure Arc 数据控制器) 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>必备知识

有关概述信息，请参阅 [创建 Azure Arc 数据控制器](create-data-controller.md) 主题。

若要使用 Azure 数据 CLI 创建 Azure Arc 数据控制器，需要安装 Azure 数据 CLI。

   [安装 Azure 数据 CLI](install-client-tools.md)

无论选择哪个目标平台，都需要在创建数据控制器管理员用户之前设置以下环境变量。 您可以根据需要向需要管理员访问数据控制器的其他人员提供这些凭据。

**AZDATA_USERNAME** -为数据控制器管理员用户选择的用户名。 示例： `arcadmin`

**AZDATA_PASSWORD** -为数据控制器管理员用户选择的密码。 密码长度必须至少为8个字符，并且包含以下四个集中的三个：大写字母、小写字母、数字和符号。

### <a name="linux-or-macos"></a>Linux 或 macOS

```console
export AZDATA_USERNAME="<your username of choice>"
export AZDATA_PASSWORD="<your password of choice>"
```

### <a name="windows-powershell"></a>Windows PowerShell

```console
$ENV:AZDATA_USERNAME="<your username of choice>"
$ENV:AZDATA_PASSWORD="<your password of choice>"
```

在开始创建 Azure Arc 数据控制器之前，你将需要连接到 Kubernetes 群集并向其进行身份验证，并选择一个现有的 Kubernetes 上下文。 连接到 Kubernetes 群集或服务的方式各不相同。 请参阅有关如何连接到 Kubernetes API 服务器的 Kubernetes 分布或服务的文档。

可以查看当前的 Kubernetes 连接并使用以下命令确认当前上下文。

```console
kubectl get namespace

kubectl config current-context
```

## <a name="create-the-azure-arc-data-controller"></a>创建 Azure Arc 数据控制器

> [!NOTE]
> `--namespace`在下面的示例中，你可以为 azdata arc dc create 命令的参数使用不同的值，但请确保将该命名空间名称用于 `--namespace parameter` 下面的所有其他命令。

根据目标平台配置创建，请遵循以下相应部分。

[在 Azure Kubernetes Service 上创建 (AKS) ](#create-on-azure-kubernetes-service-aks)

[在 Azure Stack 集线器上的 AKS 引擎上创建](#create-on-aks-engine-on-azure-stack-hub)

[在 Azure Stack HCI 上的 AKS 上创建](#create-on-aks-on-azure-stack-hci)

[在 Azure Red Hat 上创建 OpenShift (ARO) ](#create-on-azure-red-hat-openshift-aro)

[在 Red Hat OpenShift 容器平台上创建 (OCP) ](#create-on-red-hat-openshift-container-platform-ocp)

[在开源上创建-上游 Kubernetes (kubeadm) ](#create-on-open-source-upstream-kubernetes-kubeadm)

[在 AWS 弹性 Kubernetes Service (EKS 上创建) ](#create-on-aws-elastic-kubernetes-service-eks)

[在 Google Cloud Kubernetes Engine Service 上创建 (GKE) ](#create-on-google-cloud-kubernetes-engine-service-gke)

### <a name="create-on-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service 上创建 (AKS) 

默认情况下，AKS 部署配置文件使用 `managed-premium` 存储类。 `managed-premium`仅当你有使用具有高级磁盘的 VM 映像部署的 vm 时，存储类才适用。

如果要使用 `managed-premium` 作为存储类，则可以运行以下命令来创建数据控制器。 将命令中的占位符替换为资源组名称、订阅 ID 和 Azure 位置。

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

如果你不确定要使用哪个存储类，则应使用支持的 `default` 存储类，而不管你使用的是哪种 VM 类型。 它不会提供最快的性能。

如果要使用 `default` 存储类，则可以运行以下命令：

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

运行该命令后，请继续 [监视创建状态](#monitoring-the-creation-status)。

### <a name="create-on-aks-engine-on-azure-stack-hub"></a>在 Azure Stack 集线器上的 AKS 引擎上创建

默认情况下，部署配置文件使用 `managed-premium` 存储类。 `managed-premium`仅当你的辅助 vm 使用 Azure Stack 集线器上具有高级磁盘的 VM 映像进行部署时，存储类才适用。

可以运行以下命令，使用托管高级存储类创建数据控制器：

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

如果你不确定要使用哪个存储类，则应使用支持的 `default` 存储类，而不管你使用的是哪种 VM 类型。 在 Azure Stack 中心，高级磁盘和标准磁盘由同一存储基础结构支持。 因此，它们应提供相同的常规性能，但不同的 IOPS 限制。

如果要使用 `default` 存储类，则可以运行此命令。

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

运行该命令后，请继续 [监视创建状态](#monitoring-the-creation-status)。

### <a name="create-on-aks-on-azure-stack-hci"></a>在 Azure Stack HCI 上的 AKS 上创建

默认情况下，部署配置文件使用名为的存储类 `default` 和服务类型 `LoadBalancer` 。

您可以运行以下命令，以使用 `default` 存储类和服务类型创建数据控制器 `LoadBalancer` 。

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

运行该命令后，请继续 [监视创建状态](#monitoring-the-creation-status)。


### <a name="create-on-azure-red-hat-openshift-aro"></a>在 Azure Red Hat 上创建 OpenShift (ARO) 

若要在 Azure Red Hat OpenShift 上创建数据控制器，需要针对群集执行以下命令，以放宽安全约束。 这是未来将删除的临时要求。
> [!NOTE]
>   在此处和下面的命令中使用相同的命名空间 `azdata arc dc create` 。 示例为 `arc` 。

首先，从 [GitHub](https://raw.githubusercontent.com/microsoft/azure_arc/master/data_services/deployment/yaml/arc-data-scc.yaml) 下载自定义安全上下文约束 (SCC) ，并将其应用到群集。

您可以运行以下命令来创建数据控制器：
> [!NOTE]
>   在此处和上述命令中使用相同的命名空间 `oc adm policy add-scc-to-user` 。 示例为 `arc` 。

```console
azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example
#azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

运行该命令后，请继续 [监视创建状态](#monitoring-the-creation-status)。

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>在 Red Hat OpenShift 容器平台上创建 (OCP) 


> [!NOTE]
> 如果在 Azure 上使用 Red Hat OpenShift 容器平台，建议使用最新的可用版本。

若要在 Red Hat OpenShift 容器平台上创建数据控制器，需要针对群集执行以下命令，以放宽安全约束。 这是未来将删除的临时要求。
> [!NOTE]
>   在此处和下面的命令中使用相同的命名空间 `azdata arc dc create` 。 示例为 `arc` 。

```console
oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
```

还需要通过运行以下命令来确定要使用的存储类。

```console
kubectl get storageclass
```

首先，请通过运行以下命令，从 openshift 部署配置文件开始创建一个新的自定义部署配置文件。 此命令将 `custom` 在当前工作目录和该目录中的自定义部署配置文件中创建一个目录 `control.json` 。

使用 `azure-arc-openshift` OpenShift 容器平台的配置文件。

```console
azdata arc dc config init --source azure-arc-openshift --path ./custom
```
使用 `azure-arc-azure-openshift` Azure RedHat Open Shift 的配置文件。

```console
azdata arc dc config init --source azure-arc-azure-openshift --path ./custom
```

现在，通过 `<storageclassname>` 在下面的命令中将替换为你要使用的存储类的名称，并通过运行上述命令来设置所需的存储类 `kubectl get storageclass` 。

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

默认情况下，openshift 部署配置文件使用 `NodePort` 作为服务类型。 如果你使用的是与负载均衡器集成的 OpenShift 群集，则可以使用以下命令将配置更改为使用 LoadBalancer 服务类型：

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

当使用 OpenShift 时，你可能想要使用 OpenShift 中的默认安全策略运行，或者想要通常比典型的环境锁定环境。 您可以选择禁用某些功能，以最大程度地减少部署时所需的权限，并运行以下命令。

此命令禁用有关 pod 的指标收集。 如果禁用了此功能，将无法在 Grafana 仪表板中查看有关 pod 的指标。 默认值为 true。

```console
azdata arc dc config replace -p ./custom/control.json --json-values spec.security.allowPodMetricsCollection=false
```

此命令禁用有关节点的度量值集合。 如果禁用了此功能，将无法在 Grafana 仪表板中查看有关节点的指标。 默认值为 true。

```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowNodeMetricsCollection=false
```

此命令禁用出于故障排除目的而使用内存转储的能力。
```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowDumps=false
```

现在，可以使用以下命令创建数据控制器了。
> [!NOTE]
>   在此处和上述命令中使用相同的命名空间 `oc adm policy add-scc-to-user` 。 示例为 `arc` 。

> [!NOTE]
>   `--path`参数应指向包含文件上的 control.js的_目录_，而不是指向文件本身的 control.js。


```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

运行该命令后，请继续 [监视创建状态](#monitoring-the-creation-status)。

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>在开源上创建-上游 Kubernetes (kubeadm) 

默认情况下，kubeadm 部署配置文件使用名为 `local-storage` 和服务类型的存储类 `NodePort` 。 如果可以接受，则可以跳过下面的说明，这些说明设置所需的存储类和服务类型并立即运行 `azdata arc dc create` 下面的命令。

如果要自定义部署配置文件以指定特定的存储类和/或服务类型，请首先运行以下命令，基于 kubeadm 部署配置文件创建新的自定义部署配置文件文件。 此命令将 `custom` 在当前工作目录和该目录中的自定义部署配置文件中创建一个目录 `control.json` 。

```console
azdata arc dc config init --source azure-arc-kubeadm --path ./custom
```

可以通过运行以下命令查找可用的存储类。

```console
kubectl get storageclass
```

现在，通过 `<storageclassname>` 在下面的命令中将替换为你要使用的存储类的名称，并通过运行上述命令来设置所需的存储类 `kubectl get storageclass` 。

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

默认情况下，kubeadm 部署配置文件使用 `NodePort` 作为服务类型。 如果你使用的是与负载均衡器集成的 Kubernetes 群集，则可以使用以下命令更改配置。

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

现在，可以使用以下命令创建数据控制器了。

```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

运行该命令后，请继续 [监视创建状态](#monitoring-the-creation-status)。

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>在 AWS 弹性 Kubernetes Service (EKS 上创建) 

默认情况下，EKS 存储类为 `gp2` ，而服务类型为 `LoadBalancer` 。

运行以下命令，以使用提供的 EKS 部署配置文件创建数据控制器。

```console
azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

运行该命令后，请继续 [监视创建状态](#monitoring-the-creation-status)。

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>在 Google Cloud Kubernetes Engine Service 上创建 (GKE) 

默认情况下，GKE 存储类为 `standard` ，而服务类型为 `LoadBalancer` 。

运行以下命令，以使用提供的 GKE 部署配置文件创建数据控制器。

```console
azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

运行该命令后，请继续 [监视创建状态](#monitoring-the-creation-status)。

## <a name="monitoring-the-creation-status"></a>监视创建状态

创建控制器需要几分钟才能完成。 可以通过以下命令在另一个终端窗口中监视进度：

> [!NOTE]
>  下面的示例命令假设你创建了一个具有名称的数据控制器和 Kubernetes 命名空间 `arc` 。 如果使用了不同的命名空间/数据控制器名称，则可以将替换 `arc` 为您的名称。

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

还可以通过运行如下命令来检查任何特定 pod 的创建状态。 这对于解决任何问题特别有用。

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>创建问题疑难解答

如果在创建 troubles 时遇到任何问题，请参阅 [故障排除指南](troubleshoot-guide.md)。
