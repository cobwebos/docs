---
title: 排查常见的 Azure Kubernetes 服务问题
description: 了解如何排查和解决在使用 Azure Kubernetes 服务 (AKS) 时遇到的常见问题
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: c20f2cc03565ce861dfc6317be8459fdafeef0bf
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384099"
---
# <a name="aks-troubleshooting"></a>AKS 疑难解答
在创建或管理 AKS 群集时，可能偶尔会遇到问题。 本文详细介绍了一些常见问题及其排查步骤。

### <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-issues"></a>通常在何处查看 Kubernetes 问题调试的相关信息？

[此处](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)是介绍 kubernetes 群集问题排查的官方链接。
[此处](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)的链接指向 Microsoft 工程师就 Pod、节点和群集等问题排查所发布的答疑解难指南。

### <a name="i-am-getting-a-quota-exceeded-error-during-create-or-upgrade-what-should-i-do"></a>在创建或升级期间遇到超出配额的错误。 我该怎么办？ 

你需要在[此处](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)请求内核。

### <a name="what-is-the-max-pods-per-node-setting-for-aks"></a>对 AKS 而言，每个节点设置的最大 Pod 是多少？

如果是在 Azure 门户中部署 AKS 群集，则每个节点的最大 Pod 均默认设置为 30。
如果是在 Azure CLI 中部署 AKS 群集，则每个节点的最大 Pod 均默认设置为 110。 （确保使用的是最新版本的 Azure CLI）。 可在 az aks create 命令中使用 –max-nodes-per-pod 标记来更改此默认设置。

### <a name="i-am-getting-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>在使用高级网络部署 AKS 群集时收到“insufficientSubnetSize”错误。 我该怎么办？

在 AKS 创建期间为网络选择的自定义 VNET 选项中，Azure CNI 用于 IPAM。 AKS 群集中的节点数可介于 1 和 100 之间的任意位置。 基于 2) 上方的子网大小应为大于产品的节点数和每个节点的子网大小的最大 Pod 数 > 群集中没有节点 * 每个节点的最大 Pod。

### <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>我的 Pod 停滞在“CrashLoopBackOff”模式。 我该怎么办？

可能有多种原因导致 Pod 停滞在该模式。 可能想要深入了解 
* Pod 本身使用 `kubectl describe pod <pod-name>`
* 日志使用 `kubectl log <pod-name>`

### <a name="i-am-trying-to-enable-rbac-on-an-existing-cluster-can-you-tell-me-how-i-can-do-that"></a>尝试在现有群集上启用 RBAC。 告诉我如何实现？

遗憾的是，目前不支持在现有群集上启用 RBAC。 需要显式创建新群集。 如果使用 CLI，则默认情况下启用 RBAC，而在 AKS 门户创建工作流中启用 RBAC 的切换按钮是可用的。

### <a name="i-created-a-cluster-using-the-azure-cli-with-defaults-or-the-azure-portal-with-rbac-enabled-and-numerous-warnings-in-the-kubernetes-dashboard-the-dashboard-used-to-work-before-without-any-warnings-what-should-i-do"></a>使用带有默认值的 Azure CLI 或使用启用了 RBAC 的 Azure 门户创建了一个集群，并在 kubernetes 仪表板中发出了大量的警告。 仪表板以前在没有任何警告的情况下工作。 我该怎么办？

仪表板上收到警告的原因是现在启用了 RBAC，但已默认禁用了对它的访问。 一般来说，此方法比较棒，因为仪表板默认公开给群集的所有用户可能会导致安全威胁。 如果仍想要启用仪表板，请按照此[博客](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/)中的说明来启用它。

### <a name="i-cant-seem-to-connect-to-the-dashboard-what-should-i-do"></a>我似乎无法连接到仪表板。 我该怎么办？

要访问群集外的服务，最简单的方法是运行 kubectl 代理，它将代理对 Kubernetes API 服务器使用 localhost 端口 8001 的请求。 然后，apiserver 可代理你的服务： http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default

如果看不到 kubernetes 仪表板，请检查 kube-proxy pod 是否正在 kube-system 命名空间中运行。 如果未处于运行状态，请删除 Pod，它会重启。

### <a name="i-could-not-get-logs-using-kubectl-logs-or-cannot-connect-to-the-api-server-getting-the-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>我无法使用 Kubectl 日志获取日志或无法连接到 api 服务器，收到"来自服务器的错误: 拨号后端错误: 拨打 tcp..."。 我该怎么办？

请确保默认 NSG 未被修改并且端口 22 已打开以连接到 API 服务器。 请检查 tunnelfront pod 是否正在 kube-system 命名空间中运行。 如果未运行，请强制删除，它会随即重启。

### <a name="i-am-trying-to-upgrade-or-scale-and-am-getting-message-changing-property-imagereference-is-not-allowed-error--how-do-i-fix-this-issue"></a>我在尝试进行升级或缩放，并收到以下消息：“不允许更改属性‘imageReference’。” 错误。  如何修复此问题？

你收到此错误的原因可能是，你修改了 AKS 群集内代理节点中的标记。 如果修改和删除 MC_* 资源组中资源的标记和其他属性，可能会导致意外结果。 修改 AKS 群集中 MC_* 下的资源会中断 SLO。

### <a name="how-do-i-renew-the-service-principal-secret-on-my-aks-cluster"></a>如何续订我的 AKS 群集上的服务主体机密？

默认情况下，使用服务主体创建的 AKS 群集具有为期一年的有效期。 在一年有效期即将结束时，你可以重置凭据来将服务主体延长额外的一段时间。

以下示例将执行下列步骤：

1. 使用 [az aks show](/cli/azure/aks#az-aks-show) 命令获取你的群集的服务主体 ID。
1. 使用 [az ad sp credential list](/cli/azure/ad/sp/credential#az-ad-sp-credential-list) 列出服务主体客户端机密。
1. 使用 [az ad sp credential-reset](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) 命令将服务主体再延长一年。 服务主体客户端机密必须保持不变，AKS 群集才能正常运行。

```azurecli
# Get the service principal ID of your AKS cluster
sp_id=$(az aks show -g myResourceGroup -n myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)

# Get the existing service principal client secret
key_secret=$(az ad sp credential list --id $sp_id --query [].keyId -o tsv)

# Reset the credentials for your AKS service principal and extend for 1 year
az ad sp credential reset \
    --name $sp_id \
    --password $key_secret \
    --years 1
```
