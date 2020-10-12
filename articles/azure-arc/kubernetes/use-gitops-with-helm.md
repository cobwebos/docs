---
title: '使用启用了 GitOps on Arc 的 Kubernetes 群集 (预览部署 Helm 图表) '
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: 将 GitOps 和 Helm 配合用于启用了 Azure Arc 的群集配置（预览版）
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes 服务, 容器
ms.openlocfilehash: eea81d458ac6631c4a023134b3198e4cdb04526e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541605"
---
# <a name="deploy-helm-charts-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>使用启用了 GitOps on Arc 的 Kubernetes 群集 (预览部署 Helm 图表) 

Helm 是一种开放源打包工具，有助于安装和管理 Kubernetes 应用程序的生命周期。 与 Linux 包管理器（例如 APT 和 Yum）类似，Helm 用于管理 Kubernetes 图表，这些图表是预配置的 Kubernetes 资源包。

本文介绍了如何配置 Helm 并将其用于启用了 Azure Arc 的 Kubernetes。

## <a name="before-you-begin"></a>开始之前

本文假设你已有一个启用了 Azure Arc 的 Kubernetes 已连接群集。 如果你需要一个已连接的群集，请参阅[连接群集快速入门](./connect-cluster.md)。

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>将 GitOps 和 Helm 与启用了 Azure Arc 的 Kubernetes 一起使用概述

 Helm Operator 提供了 Flux 的一个扩展，用于自动完成 Helm 图表发布。 图表发布是通过名为 HelmRelease 的 Kubernetes 自定义资源描述的。 Flux 将这些资源从 git 同步到群集， Helm Operator 确保根据资源中的指定发布 Helm 图表。

 本文档中使用的 [示例存储库](https://github.com/Azure/arc-helm-demo) 的结构如下所示：

```console
├── charts
│   └── azure-arc-sample
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── app.yaml
```

在 git 存储库中，我们有两个目录，一个包含 Helm 图表，另一个包含版本配置。在 `releases` 目录中， `app.yaml` 包含如下所示的 HelmRelease 配置：

```yaml
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: azure-arc-sample
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-arc-sample
  values:
    serviceName: arc-k8s-demo
```

Helm 发布配置包含以下字段：

- `metadata.name` 是必需的，需要遵循 Kubernetes 命名约定
- `metadata.namespace` 是可选的，用于确定发布创建位置
- `spec.releaseName` 是可选的，如果未提供，则发布名称将是 $namespace-$name
- `spec.chart.path` 是包含图表的目录，指定为相对于存储库根目录的路径
- `spec.values` 是图表本身中默认参数值的用户自定义设置

HelmRelease spec.values 中指定的选项将替代图表源中的 values.yaml 中指定的选项。

可以在官方 [Helm Operator 文档](https://docs.fluxcd.io/projects/helm-operator/en/stable/)中详细了解 HelmRelease

## <a name="create-a-configuration"></a>创建配置

让我们使用适用于 `k8sconfiguration` 的 Azure CLI 扩展，将已连接的群集链接到示例 git 存储库。 我们将此配置命名为 `azure-arc-sample`，并在 `arc-k8s-demo` 命名空间中部署 Flux Operator。

```console
az k8sconfiguration create --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name flux --operator-namespace arc-k8s-demo --operator-params='--git-readonly --git-path=releases' --enable-helm-operator --helm-operator-version='0.6.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>配置参数

若要自定义配置创建，请[了解可以使用的其他参数](./use-gitops-connected-cluster.md#additional-parameters)。

## <a name="validate-the-configuration"></a>验证配置

使用 Azure CLI 验证 `sourceControlConfiguration` 是否已成功创建。

```console
az k8sconfiguration show --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

`sourceControlConfiguration`资源将更新为符合性状态、消息和调试信息。

**输出：**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "enableHelmOperator": "True",
  "helmOperatorProperties": {
    "chartValues": "--set helm.versions=v3",
    "chartVersion": "0.6.0"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-arc-sample",
  "name": "azure-arc-sample",
  "operatorInstanceName": "flux",
  "operatorNamespace": "arc-k8s-demo",
  "operatorParams": "--git-readonly --git-path=releases",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>验证应用程序

运行以下命令并 `localhost:8080` 在浏览器中导航到，验证应用程序是否正在运行。

```console
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo 8080:8080
```

## <a name="next-steps"></a>后续步骤

- [使用 Azure Policy 控制群集配置](./use-azure-policy.md)
