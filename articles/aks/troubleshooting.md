---
title: 排查常见的 Azure Kubernetes 服务问题
description: 了解如何排查和解决在使用 Azure Kubernetes 服务 (AKS) 时遇到的常见问题
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: fd3d1c464c6f2d4cbecd715db0689581ca141769
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/20/2018
ms.locfileid: "53654064"
---
# <a name="aks-troubleshooting"></a>AKS 疑难解答

当创建或管理 Azure Kubernetes 服务 (AKS) 群集时，可能偶尔会遇到问题。 本文详细介绍了一些常见问题及其排查步骤。

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>通常在何处查看 Kubernetes 问题调试的相关信息？

请尝试 [Kubernetes 群集故障排除的官方指南](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)。
还可尝试由 Microsoft 工程师发布的[故障排除指南](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)，用于对 Pod、节点、群集和其他功能进行故障排除。

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>在创建或升级期间遇到“超出配额”的错误。 我该怎么办？ 

需要[请求内核](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)。

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>对 AKS 而言，每个节点设置的最大 Pod 是多少？

如果在 Azure 门户中部署 AKS 群集，则每个节点的最大 Pod 均默认设置为 30。
如果在 Azure CLI 中部署 AKS 群集，则每个节点的最大 Pod 均默认设置为 110。 （确保使用最新版本的 Azure CLI）。 可以使用 `az aks create` 命令中的 `–-max-pods` 标记来更改此默认设置。

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>在使用高级网络部署 AKS 群集时收到 insufficientSubnetSize 错误。 我该怎么办？

在创建 AKS 期间提供网络的自定义 Azure 虚拟网络选项中，Azure 容器网络接口 (CNI) 用于 IP 地址管理 (IPAM)。 AKS 群集中的节点数可介于 1 和 100 之间的任意位置。 根据前一节，子网大小应大于节点数和每个节点的最大 Pod 数的乘积。 关系可以表示这种方式：子网大小 > 群集中的节点数 * 每个节点的最大 Pod。

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>我的 Pod 停滞在 CrashLoopBackOff 模式。 我该怎么办？

可能有多种原因导致 Pod 停滞在该模式。 可能通过以下方式查看：

* 使用 `kubectl describe pod <pod-name>` 查看 Pod 本身。
* 使用 `kubectl log <pod-name>` 查看日志。

有关如何对 Pod 的问题进行故障排除的详细信息，请参阅[调试应用程序](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods)。

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>尝试在现有群集上启用 RBAC。 该如何操作？

遗憾的是，目前不支持在现有群集上启用基于角色的访问控制 (RBAC)。 必须显式创建新群集。 如果使用 CLI，则默认启用 RBAC。 如果使用 AKS 门户，则在创建工作流时可使用切换按钮来启用 RBAC。

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>使用带有默认值的 Azure CLI 或 Azure 门户创建了一个启用了 RBAC 的集群，现在 Kubernetes 仪表板上出现了许多警告。 仪表板以前在没有任何警告的情况下工作。 我该怎么办？

仪表板上收到警告的原因是群集现在启用了 RBAC，但已默认禁用了对它的访问。 一般来说，此方法比较棒，因为仪表板默认公开给群集的所有用户可能会导致安全威胁。 如果仍想要启用仪表板，请遵循此[博客文章](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/)中的步骤进行操作。

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>我无法连接到仪表板。 我该怎么办？

要访问群集外的服务，最简单的方法是运行 `kubectl proxy`，它将代理对 Kubernetes API 服务器使用 localhost 端口 8001 的请求。 在此，API 服务器可以代理服务：`http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`。

如果看不到 Kubernetes 仪表板，请检查 `kube-proxy` Pod 是否在 `kube-system` 命名空间中运行。 如果未处于运行状态，请删除 Pod，它会重启。

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>无法使用 Kubectl 日志获取日志或无法连接到 API 服务器。 我收到“来自服务器的错误：拨号后端时出错: 拨打 tcp...” 我该怎么办？

请确保默认网络安全组 (NSG) 未被修改并且端口 22 已打开以连接到 API 服务器。 检查 `tunnelfront` Pod是否在 `kube-system` 命名空间中运行。 如果没有，请强制删除 Pod，它会重启。

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error--how-do-i-fix-this-problem"></a>我在尝试进行升级或缩放，并收到“消息：不允许更改属性‘imageReference’”错误。  如何修复此问题？

收到此错误的原因可能是，你修改了 AKS 群集内代理节点中的标记。 如果修改和删除 MC_* 资源组中资源的标记和其他属性，可能会导致意外结果。 修改 AKS 群集中 MC_ * 组下的资源会中断服务级别目标 (SLO)。

## <a name="how-do-i-renew-the-service-principal-secret-on-my-aks-cluster"></a>如何续订我的 AKS 群集上的服务主体机密？

默认情况下，使用服务主体创建的 AKS 群集具有为期一年的有效期。 在有效期即将结束时，你可以重置凭据来将服务主体延长额外的一段时间。

此示例将执行下列步骤：

1. 使用 [az aks show](/cli/azure/aks#az-aks-show) 命令获取群集的服务主体 ID。
1. 使用 [az ad sp credential list](/cli/azure/ad/sp/credential#az-ad-sp-credential-list) 列出服务主体客户端机密。
1. 使用 [az ad sp credential-reset](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) 命令将服务主体再延长一年。 服务主体客户端机密必须保持不变，AKS 群集才能正常运行。

```azurecli
# Get the service principal ID of your AKS cluster.
sp_id=$(az aks show -g myResourceGroup -n myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)

# Get the existing service principal client secret.
key_secret=$(az ad sp credential list --id $sp_id --query [].keyId -o tsv)

# Reset the credentials for your AKS service principal and extend for one year.
az ad sp credential reset \
    --name $sp_id \
    --password $key_secret \
    --years 1
```
