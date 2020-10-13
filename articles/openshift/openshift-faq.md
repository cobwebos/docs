---
title: Azure Red Hat OpenShift 常见问题
description: 下面是有关 Microsoft Azure Red Hat OpenShift 的常见问题的解答
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 07/31/2020
ms.openlocfilehash: a8b5ec48b64341ad9eabd087d7ee20bb703198c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88816229"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift 常见问题

本文解答了有关 Microsoft Azure Red Hat OpenShift 的常见问题 (常见问题解答) 。

## <a name="installation-and-upgrade"></a>安装和升级

### <a name="which-azure-regions-are-supported"></a>支持哪些 Azure 区域？

有关 Azure Red Hat OpenShift 4.x 支持的区域的列表，请参阅 [可用区域](https://docs.openshift.com/aro/4/welcome/index.html#available-regions)。

有关 Azure Red Hat OpenShift 3.11 支持的区域的列表，请参阅 [可用产品（按区域](supported-resources.md#azure-regions)）。

### <a name="what-virtual-machine-sizes-can-i-use"></a>我可以使用哪些虚拟机大小？

有关 Azure Red Hat OpenShift 4 支持的虚拟机大小列表，请参阅 [Azure Red Hat OpenShift 4 支持的资源](support-policies-v4.md)。

有关 Azure Red Hat OpenShift 3.11 支持的虚拟机大小列表，请参阅 [Azure Red Hat OpenShift 3.11 支持的资源](supported-resources.md)。

### <a name="what-is-the-maximum-number-of-pods-in-an-azure-red-hat-openshift-cluster--what-is-the-maximum-number-of-pods-per-node-in-azure-red-hat-openshift"></a>Azure Red Hat OpenShift 群集中的最大 pod 数是多少？  Azure Red Hat OpenShift 中每个节点的最大 pod 数是多少？

受支持的箱的实际数目取决于应用程序的内存、CPU 和存储要求。

Azure Red Hat OpenShift 4.x 具有 250 pod 每节点限制和100计算节点限制。 此上限：群集中支持的最大 pod 数 250 &times; 100 = 25000。

Azure Red Hat OpenShift 3.11 的每节点上限为50，每个节点限制为20个计算节点。 此上限到 50 20 = 1000 的群集中支持的最大 pod 数 &times; 。

### <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>群集是否可以跨多个 Azure 区域具有计算节点？

不是。 Azure Red Hat OpenShift 群集中的所有节点都必须源自同一 Azure 区域。

### <a name="can-a-cluster-be-deployed-across-multiple-availability-zones"></a>是否可以跨多个可用性区域部署群集？

是。 如果将群集部署到支持可用性区域的 Azure 区域，则会自动发生这种情况。 有关详细信息，请参阅[可用性区域](../availability-zones/az-overview.md#availability-zones)。

### <a name="are-control-plane-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>控制平面节点与 Azure Kubernetes Service (AKS) 相比是否抽象？

不是。 所有资源（包括群集主节点）都在您的客户订阅中运行。 这些类型的资源置于只读资源组中。

### <a name="does-the-cluster-reside-in-a-customer-subscription"></a>群集是否位于客户订阅中？ 

Azure 托管应用程序与客户订阅处于锁定的资源组中。 客户可以查看该资源组中的对象，但不能对其进行修改。

### <a name="is-there-any-element-in-azure-red-hat-openshift-shared-with-other-customers-or-is-everything-independent"></a>Azure Red Hat OpenShift 中是否有任何与其他客户共享的元素？ 或者一切都是独立的吗？

每个 Azure Red Hat OpenShift 群集专用于给定的客户，并在客户的订阅中。 

### <a name="are-infrastructure-nodes-available"></a>是否提供了基础结构节点？

在 Azure Red Hat OpenShift 4.x 群集上，基础结构节点当前不可用。

在 Azure Red Hat OpenShift 3.11 群集上，默认情况下包括基础结构节点。

## <a name="how-do-i-handle-cluster-upgrades"></a>如何实现处理群集升级？

有关升级、维护和支持的版本的信息，请参阅 [支持生命周期指南](support-lifecycle.md)。

### <a name="how-will-the-host-operating-system-and-openshift-software-be-updated"></a>如何更新主机操作系统和 OpenShift 软件？

主机操作系统和 OpenShift 软件已更新，因为 Azure Red Hat OpenShift 会消耗来自上游 OpenShift 容器平台的次要发行版和修补程序。

### <a name="whats-the-process-to-reboot-the-updated-node"></a>重新启动已更新节点的过程是什么？

在升级过程中，节点将重新启动。

## <a name="cluster-operations"></a>群集操作

### <a name="can-i-use-prometheus-to-monitor-my-applications"></a>是否可以使用 Prometheus 监视我的应用程序？

Prometheus 是预安装的，并配置为适用于 Azure Red Hat OpenShift 1.x 群集。 阅读有关 [群集监视](https://docs.openshift.com/container-platform/3.11/install_config/prometheus_cluster_monitoring.html)的详细信息。

对于 Azure Red Hat OpenShift 3.11 群集，可以在命名空间中部署 Prometheus 并监视命名空间中的应用程序。 有关详细信息，请参阅 [在 Azure Red Hat OpenShift 群集中部署 Prometheus 实例](howto-deploy-prometheus.md)。

### <a name="can-i-use-prometheus-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>是否可以使用 Prometheus 监视与群集运行状况和容量相关的指标？

在 Azure Red Hat OpenShift 4.x 中：是。

在 Azure Red Hat OpenShift 3.11 中：否。

### <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>底层 Vm 的日志是否可以流入客户日志分析系统？

来自基础 Vm 的日志由托管服务处理，不会向客户公开。

### <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-azure-red-hat-openshift-cluster"></a>客户如何在节点级别获取对 CPU/内存等指标的访问权限，以采取措施来缩放、调试问题，等等。 我似乎无法在 Azure Red Hat OpenShift 群集上运行 kubectl。

对于 Azure Red Hat OpenShift 4.x 群集，OpenShift web 控制台在节点级别包含所有指标。 有关详细信息，请参阅关于 [查看群集信息](https://docs.openshift.com/aro/4/web_console/using-dashboard-to-get-cluster-information.html)的 Red Hat 文档。

对于 Azure Red Hat OpenShift 3.11 群集，客户可以通过使用命令 `oc adm top nodes` 或 `kubectl top nodes` 使用客户管理员群集角色来访问节点级别的 CPU/内存指标。 客户还可以使用命令或访问的 CPU/内存指标 `pods` `oc adm top pods` `kubectl top pods` 。

### <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>如果我们增加部署，Azure 容错域将如何映射到 pod 位置，以确保某个服务的所有 pod 在单个容错域中出现故障时不会被挖使。

使用 Azure 中的虚拟机规模集时，默认情况下有五个容错域。 规模集中的每个虚拟机实例将被放入这些容错域之一。 这可确保部署到群集中的计算节点的应用程序将被置于单独的容错域中。

有关详细信息，请参阅 [为虚拟机规模集选择合适数量的容错域](../virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains.md)。

### <a name="is-there-a-way-to-manage-pod-placement"></a>是否有一种方法来管理 pod 位置？

客户能够以客户管理员身份获取节点和查看标签。这将提供一种方法来针对规模集中的任何 VM。

使用特定标签时必须注意：

- 不得使用主机名。 主机名经常随升级和更新一起旋转，并保证更改。
- 如果客户有针对特定标签或部署策略的请求，则可以完成此操作，但目前不支持工程设计工作。

有关详细信息，请参阅 [控制 pod 位置](https://docs.openshift.com/aro/4/nodes/scheduling/nodes-scheduler-about.html)。

### <a name="is-the-image-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>映像注册表是否可在外部使用，因此可以使用 Jenkins 等工具？

对于4.x 群集，需要公开安全注册表并配置身份验证。 有关详细信息，请参阅以下 Red Hat 文档：

- [公开注册表](https://docs.openshift.com/aro/4/registry/securing-exposing-registry.html)
- [访问注册表](https://docs.openshift.com/aro/4/registry/accessing-the-registry.html)

对于3.11 群集，可以使用 Docker 映像注册表。 可从获取 Docker 注册表 `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` 。 你还可以使用 Azure 容器注册表。

## <a name="networking"></a>网络

### <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>是否可以将群集部署到现有的虚拟网络？

在4.x 群集中，你可以将群集部署到现有的 VNet 中。

在3.11 群集中，无法将群集部署到现有的 VNet 中。 可以通过对等互连将 Azure Red Hat OpenShift 3.11 群集连接到现有 VNet。

### <a name="is-cross-namespace-networking-supported"></a>是否支持交叉命名空间网络？

客户和单个项目管理员可以自定义跨命名空间网络 (包括使用对象拒绝每个项目) `NetworkPolicy` 。

### <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>我尝试对另一个订阅中的虚拟网络进行对等互连，但未能获取 VNet CIDR 错误。

在具有虚拟网络的订阅中，请确保将 `Microsoft.ContainerService` 提供程序注册到以下命令： `az provider register -n Microsoft.ContainerService --wait`

### <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>可以在专用 VNet 上指定部署的 IP 范围，以避免与其他公司 Vnet 发生一次对等互连的冲突？

在4.x 群集中，可以指定自己的 IP 范围。

在3.11 群集中，Azure Red Hat OpenShift 支持 VNet 对等互连，并允许客户向对等机提供 VNet，以及 OpenShift 网络将在其中运行的 VNet CIDR。

Azure Red Hat OpenShift 创建的 VNet 将受到保护，不会接受配置更改。 对等互连的 VNet 由客户控制并驻留在其订阅中。

### <a name="is-the-software-defined-network-module-configurable"></a>软件定义的网络模块是否可配置？

软件定义的网络为 `openshift-ovs-networkpolicy` 且不可配置。

### <a name="what-azure-load-balancer-is-used-by-azure-red-hat-openshift--is-it-standard-or-basic-and-is-it-configurable"></a>Azure Red Hat OpenShift 使用什么 Azure 负载均衡器？  是标准版还是基本版，是否可配置？

Azure Red Hat OpenShift 使用标准的 Azure 负载均衡器，并且不可配置。

## <a name="permissions"></a>权限

### <a name="can-an-admin-manage-users-and-quotas"></a>管理员是否可以管理用户和配额？

是。 Azure Red Hat OpenShift 管理员可以管理用户和配额，还可以访问所有用户创建的项目。

### <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>是否可以将群集限制为仅限特定 Azure AD 的用户？

是。 你可以通过配置 Azure AD 应用程序限制用户可以登录到群集的 Azure AD。 有关详细信息，请参阅 [如何：将应用限制为一组用户](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md)。

### <a name="can-i-restrict-users-from-creating-projects"></a>可以限制用户创建项目吗？

是。 以管理员身份登录到群集，然后执行以下命令：

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

有关详细信息，请参阅 OpenShift 文档，了解如何对群集版本禁用自我预配：

- [禁用4.3 群集中的自我预配](https://docs.openshift.com/aro/4/applications/projects/configuring-project-creation.html#disabling-project-self-provisioning_configuring-project-creation)
- [禁用3.11 群集中的自我预配](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)

### <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>IaaS) 中 (哪些 UNIX 权限可用于主机/基础节点/应用节点？

对于4.x 群集，可以通过群集管理角色访问节点。 有关详细信息，请参阅 [RBAC 概述](https://docs.openshift.com/container-platform/4.3/authentication/using-rbac.html)。

对于3.11 群集，禁止访问节点。

### <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>我们有哪些 OCP 权限？ 群集管理？ 项目-管理员？

对于4.x 群集，群集管理角色可用。 有关详细信息，请参阅 [RBAC 概述](https://docs.openshift.com/container-platform/4.3/authentication/using-rbac.html)。

有关3.11 群集的详细信息，请参阅 [群集管理概述](https://docs.openshift.com/aro/admin_guide/index.html) 。

### <a name="which-identity-providers-are-available"></a>提供哪些标识提供程序？

对于4.x 群集，可以配置自己的标识提供者。 有关详细信息，请参阅有关 [配置标识 prodivers](https://docs.openshift.com/aro/4/authentication/identity_providers/configuring-ldap-identity-provider.html)的 Red Hat 文档。

对于3.11 群集，可以使用 Azure AD 集成。 

## <a name="storage"></a>存储

### <a name="is-data-on-my-cluster-encrypted"></a>群集上的数据是否已加密？

默认情况下，静态加密数据。 Azure 存储平台会在保存数据之前自动对其进行加密，并在检索之前解密数据。 有关详细信息，请参阅[静态数据的 Azure 存储服务加密](../storage/common/storage-service-encryption.md)。

### <a name="is-data-stored-in-etcd-encrypted-on-azure-red-hat-openshift"></a>存储在 etcd 中的数据是否存储在 Azure Red Hat OpenShift 上？

对于 Azure Red Hat OpenShift 4 群集，默认情况下不会对数据进行加密，但你可以选择启用加密。 有关详细信息，请参阅 [加密 etcd](https://docs.openshift.com/container-platform/4.3/authentication/encrypting-etcd.html)指南。

对于3.11 群集，不会在 etcd 级别对数据进行加密。 当前不支持启用加密的选项。 OpenShift 支持此功能，但需要进行工程工作才能将其放在路线图上。 在磁盘级别对数据进行加密。 有关详细信息，请参阅 [数据存储层上的数据加密](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) 。

### <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>能否选择任何持久性存储解决方案，如 OCS？ 

对于4.x 群集，Azure 磁盘 (Premium_LRS) 配置为默认存储类。 有关其他存储提供程序以及配置详细信息 (包括 Azure 文件) ，请参阅 [永久性存储](https://docs.openshift.com/aro/4/storage/understanding-persistent-storage.html)上的 Red Hat 文档。

对于3.11 群集，默认情况下提供两个存储类：一个用于 Azure 磁盘 (Premium_LRS) ，一个用于 Azure 文件。

## <a name="does-aro-store-any-customer-data-outside-of-the-clusters-region"></a>ARO 是否将任何客户数据存储在群集区域之外？

不是。 在 ARO 群集中创建的所有数据都保留在群集的区域内。