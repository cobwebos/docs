---
title: 从 Azure Red Hat OpenShift 3.11 迁移到 Azure Red Hat OpenShift 4
description: 从 Azure Red Hat OpenShift 3.11 迁移到 Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 08/13/2020
keywords: 迁移，aro，openshift，red hat
ms.openlocfilehash: 322c0cf5ece2a9c950e71b947e2aa6088a165cb8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89469739"
---
# <a name="migrate-from-azure-red-hat-openshift-311-to-azure-red-hat-openshift-4"></a>从 Azure Red Hat OpenShift 3.11 迁移到 Azure Red Hat OpenShift 4

OpenShift 4 上的 Azure Red Hat OpenShift 在 Red Hat 核心操作系统、专用群集上引入了 Kubernetes 1.16，提供了你自己的虚拟网络支持和完整群集管理角色。 此外，现在还提供了许多新功能，如对操作员框架、操作员中心和 OpenShift Service 网格的支持。

若要成功从 Azure Red Hat OpenShift 3.11 转换到 Azure Red Hat OpenShift 4，请确保查看 [存储、网络、日志记录、安全性和监视方面的区别](https://docs.openshift.com/container-platform/4.4/migration/migrating_3_4/planning-migration-3-to-4.html)。

在本文中，我们将演示如何从 Azure Red Hat OpenShift 3.11 群集迁移到 Azure Red Hat 4 群集。

> [!NOTE]
> Red Hat OpenShift 迁移工具（如控制平面迁移帮助工具和群集应用程序迁移工具） (凸轮) 不能与 Azure Red Hat OpenShift 3.11 群集一起使用。

## <a name="before-you-begin"></a>在开始之前

本文假定你已有一个 Azure Red Hat OpenShift 3.11 群集。

## <a name="create-a-target-azure-red-hat-openshift-4-cluster"></a>创建目标 Azure Red Hat OpenShift 4 群集

首先，创建要用作目标群集 [的 Azure Red Hat OpenShift 4 群集](tutorial-create-cluster.md) 。 在这里，我们将使用基本配置。 如果对不同的设置感兴趣，请参阅 [创建 Azure Red Hat OpenShift 4 群集教程](tutorial-create-cluster.md)。

创建一个虚拟网络，其中包含两个用于主节点和辅助节点的空子网。

```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
```

然后，使用以下命令创建群集。

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

## <a name="configure-the-target-openshift-4-cluster"></a>配置 target OpenShift 4 群集

### <a name="authentication"></a>身份验证

要使用户能够与 Azure Red Hat OpenShift 交互，必须先向群集进行身份验证。 身份验证层标识与 Azure Red Hat OpenShift API 请求关联的用户。 然后，授权层使用关于请求用户的信息来确定是否允许该请求。

创建 Azure Red Hat OpenShift 4 群集后，会创建一个临时的管理用户。 [连接到群集](tutorial-connect-cluster.md)，添加用户和组，并为两者 [配置适当的权限](https://docs.openshift.com/aro/4/authentication/understanding-authentication.html) 。

### <a name="networking"></a>网络

Azure Red Hat OpenShift 4 使用几个不同的操作员在群集中设置网络： [群集网络操作员](https://docs.openshift.com/aro/4/networking/cluster-network-operator.html#nw-cluster-network-operator_cluster-network-operator)、 [DNS 操作员](https://docs.openshift.com/aro/4/networking/dns-operator.html)和 [入口操作员](https://docs.openshift.com/aro/4/networking/ingress-operator.html)。 有关在 Azure Red Hat OpenShift 4 群集中设置网络的详细信息，请参阅 [网络图](concepts-networking.md) 和 [了解网络](https://docs.openshift.com/aro/4/networking/understanding-networking.html)。

### <a name="storage"></a>存储
Azure Red Hat OpenShift 4 支持以下 PersistentVolume 插件：

- AWS) 中的弹性块存储 (
- Azure 磁盘
- Azure 文件
- GCE 永久性磁盘
- HostPath
- iSCSI
- 本地卷
- NFS
- Red Hat OpenShift 容器存储

有关配置这些存储类型的信息，请参阅 [配置永久性存储](https://access.redhat.com/documentation/azure_red_hat_openshift/4/html/storage/configuring-persistent-storage)。

### <a name="registry"></a>注册表

Azure Red Hat OpenShift 4 可以从源代码构建映像，并对其进行部署，并管理其生命周期。 为了实现这一点，Azure Red Hat OpenShift 提供了4个 [集成的内部容器映像注册表](https://docs.openshift.com/aro/4/registry/registry-options.html) ，可在 Azure Red hat OpenShift 环境中将其部署到本地管理映像。

如果使用的是外部注册表，如 [Azure 容器注册表](../container-registry/index.yml)、 [red hat Quay](https://docs.openshift.com/aro/4/registry/registry-options.html#registry-quay-overview_registry-options)注册表或启用了 [身份验证的 red hat 注册表](https://docs.openshift.com/aro/4/registry/registry-options.html#registry-authentication-enabled-registry-overview_registry-options)，请按照步骤向群集提供凭据，使群集能够访问存储库。

### <a name="monitoring"></a>监视

Azure Red Hat OpenShift 包括预配置的预安装和自行更新监视堆栈，该堆栈基于 Prometheus 开源项目及其更广泛的系统。 它可以监视群集组件并包含一组警报，以立即通知群集管理员出现的任何问题和一组 Grafana 仪表板。 群集监视堆栈仅支持监视 Azure Red Hat OpenShift 群集。 有关详细信息，请参阅 [Azure Red Hat OpenShift 的群集监视](https://docs.openshift.com/aro/4/monitoring/cluster_monitoring/about-cluster-monitoring.html)。

如果你使用的是适用于 [Azure Red Hat OpenShift 3.11 的容器 Azure Monitor](../azure-monitor/insights/container-insights-azure-redhat-setup.md)，则还可以为 [Azure red hat OpenShift 4 群集](../azure-monitor/insights/container-insights-azure-redhat4-setup.md) 的容器启用 Azure Monitor，并继续使用同一 Log Analytics 工作区。

## <a name="move-your-dns-or-load-balancer-configuration-to-the-new-cluster"></a>将 DNS 或负载平衡器配置移动到新群集

如果正在使用 Azure 流量管理器，请添加终结点以引用目标群集并设置这些终结点的优先级。

## <a name="deploy-application-to-your-target-cluster"></a>将应用程序部署到目标群集

为工作负荷正确配置目标群集后， [连接到群集](tutorial-connect-cluster.md) ，并为项目创建必要的应用程序、组件或服务。 使用 Azure Red Hat OpenShift，可以通过 Git、容器映像、Red Hat 开发人员目录、Dockerfile、YAML/JSON 定义或从目录中选择数据库服务来创建它们。

## <a name="delete-your-source-cluster"></a>删除源群集
确认 Azure Red Hat OpenShift 4 群集正确设置后，请删除 Azure Red Hat OpenShift 3.11 群集。

```
az openshift delete --name $CLUSTER_NAME
                    --resource-group $RESOURCE_GROUP
                    [--no-wait]
                    [--subscription]
                    [--yes]
```
## <a name="next-steps"></a>后续步骤
[在此处](https://docs.openshift.com/aro/4/welcome/index.html)查看 red hat 提供的 Azure Red hat OpenShift 文档。