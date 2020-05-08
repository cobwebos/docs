---
title: Azure Red Hat OpenShift 常见问题
description: 下面是有关 Microsoft Azure Red Hat OpenShift 的常见问题的解答
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 92529c2d60b32e9c8b57b897008b5333adc2a4d4
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594961"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift 常见问题

本文解决 Microsoft Azure Red Hat OpenShift 的常见问题（Faq）。

## <a name="which-azure-regions-are-supported"></a>支持哪些 Azure 区域？

请参阅支持的[资源](supported-resources.md#azure-regions)，获取支持 Azure Red Hat OpenShift 的全球区域。

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>是否可以将群集部署到现有的虚拟网络？

否。 但可以通过对等互连将 Azure Red Hat OpenShift 群集连接到现有虚拟网络。 有关详细信息，请参阅[将群集的虚拟网络连接到现有虚拟网络](tutorial-create-cluster.md#create-the-cluster)。

## <a name="what-cluster-operations-are-available"></a>哪些群集操作可用？

只能增加或减少计算节点数。 创建后，不允许对`Microsoft.ContainerService/openShiftManagedClusters`资源进行任何其他修改。 计算节点的最大数目限制为20个。

## <a name="what-virtual-machine-sizes-can-i-use"></a>我可以使用哪些虚拟机大小？

有关可用于 Azure Red Hat OpenShift 群集的虚拟机大小的列表，请参阅[Azure Red Hat OpenShift 虚拟机大小](supported-resources.md#virtual-machine-sizes)。

## <a name="is-data-on-my-cluster-encrypted"></a>群集上的数据是否已加密？

默认情况下，静态加密。 Azure 存储平台会在保存数据之前自动对其进行加密，并在检索之前解密数据。 有关详细信息，请参阅[Azure 存储服务加密静态数据](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>是否可以使用 Prometheus/Grafana 监视我的应用程序？

是的，你可以在命名空间中部署 Prometheus，并监视你的命名空间中的应用程序。

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>是否可以使用 Prometheus/Grafana 监视与群集运行状况和容量相关的指标？

不，不是当前时间。

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Docker 注册表是否可在外部使用，因此可以使用 Jenkins 等工具？

中`https://docker-registry.apps.<clustername>.<region>.azmosa.io/`提供了 Docker 注册表，但未提供强大的存储持久性保证。 你还可以使用[Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)。

## <a name="is-cross-namespace-networking-supported"></a>是否支持交叉命名空间网络？

客户和单个项目管理员可以使用`NetworkPolicy`对象基于每个项目自定义跨命名空间网络（包括拒绝此网络）。

## <a name="can-an-admin-manage-users-and-quotas"></a>管理员是否可以管理用户和配额？

是的。 Azure Red Hat OpenShift 管理员可以管理用户和配额，还可以访问所有用户创建的项目。

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>是否可以将群集限制为仅限特定 Azure AD 的用户？

是的。 你可以通过配置 Azure AD 应用程序限制用户可以登录到群集的 Azure AD。 有关详细信息，请参阅[如何：将应用限制到一组用户](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-i-restrict-users-from-creating-projects"></a>可以限制用户创建项目吗？

是的。 以 Azure Red Hat OpenShift 管理员身份登录到群集，并执行以下命令：

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

有关详细信息，请参阅有关[禁用自我预配](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)的 OpenShift 文档。

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>群集是否可以跨多个 Azure 区域具有计算节点？

否。 Azure Red Hat OpenShift 群集中的所有节点都必须源自同一 Azure 区域。

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>主节点和基础结构节点在与 Azure Kubernetes 服务（AKS）配合时是否会被抽象掉？

否。 所有资源（包括群集主机）都在您的客户订阅中运行。 这些类型的资源置于只读资源组中。

## <a name="is-open-service-broker-for-azure-osba-supported"></a>是否支持 Azure （OSBA）的开放 Service Broker？

是的。 可以将 OSBA 与 Azure Red Hat OpenShift 配合使用。 有关详细信息，请参阅[Open Service Broker For Azure](https://github.com/Azure/open-service-broker-azure#openshift-project-template) 。

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>我尝试使用其他订阅中的虚拟网络，但收到`Failed to get vnet CIDR`错误消息。

在具有虚拟网络的订阅中，请确保将提供程序`Microsoft.ContainerService`注册到`az provider register -n Microsoft.ContainerService --wait` 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>Azure Red Hat OpenShift （ARO）维护过程是什么？

对于 ARO，有三种维护类型：升级、备份和还原 etcd 数据，以及云提供商启动的维护。

+ 升级包括软件升级和标识符。 在启动时，将通过运行`yum update`并提供即时缓解来进行 CVE 更正。  并行，将为将来创建群集创建新的映像版本。

+ Etcd 数据的备份和管理是一种自动化过程，根据具体的操作，可能需要群集停机。 如果正在从备份还原 etcd 数据库，则会有停机时间。 我们每小时备份一次 etcd，并保留过去6小时的备份。

+ 云提供商启动的维护包括网络、存储和区域性中断。 维护依赖于云提供商，并依赖于提供商提供的更新。

## <a name="what-is-the-general-upgrade-process"></a>一般升级过程是什么？

运行升级应是运行的安全过程，不应中断群集服务。 当新版本可用或标识符时，SRE 可以触发升级过程。

可用更新在暂存环境中进行测试，并应用于生产群集。 在应用时，将暂时添加一个新节点，并以旋转方式更新节点，使其保持副本计数。 遵循最佳做法有助于确保最小化的停机时间。

根据挂起的升级或更新的严重性，此过程可能会有所不同，因为更新可能会快速应用以减少服务对 CVE 的暴露程度。 新映像将以异步方式生成、测试和推出，作为群集升级。 除了这样，紧急维护和计划内维护之间没有区别。 计划内维护不与客户 prescheduled。

如果需要与客户进行通信，则可以通过 ICM 和电子邮件发送通知。

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>紧急情况与计划内维护时段如何呢？

我们不区分这两种维护类型。 我们的团队提供24/7/365，不使用传统计划的 "工作时间外" 维护时段。

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>如何更新主机操作系统和 OpenShift 软件？

主机操作系统和 OpenShift 软件通过我们的常规升级和映像生成过程进行更新。

## <a name="whats-the-process-to-reboot-the-updated-node"></a>重新启动已更新节点的过程是什么？

这应在升级过程中进行处理。

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>存储在 etcd 中的数据是否存储在 ARO 上？

它未在 etcd 级别进行加密。 当前不支持启用此选项的选项。 OpenShift 支持此功能，但需要进行工程工作才能将其放在路线图上。 在磁盘级别对数据进行加密。 有关详细信息，请参阅[数据存储层上的数据加密](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html)。

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>底层 Vm 的日志是否可以流入客户日志分析系统？

Syslog、docker 日志、日记和 dmesg 由托管服务进行处理，不会向客户显示。

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>客户如何在节点级别获取对 CPU/内存等度量值的访问权限，以采取措施来缩放、调试问题，等等。我似乎无法在 ARO `kubectl top`群集上运行。

客户可以通过使用命令`oc adm top nodes`或`kubectl top nodes`使用客户管理 clusterrole 来访问节点级别的 CPU/内存指标。  客户还可以`pods`使用命令`oc adm top pods`或访问的 CPU/内存指标`kubectl top pods`

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>ARO 的默认 pod 计划程序配置是什么？

ARO 使用 OpenShift 中随附的默认计划程序。 ARO 中有几种不支持的附加机制。 请参阅[默认计划程序文档](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler)和[主计划程序文档](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v16/data/master/etc/origin/master/scheduler.json)以了解更多详细信息。

当前不支持高级/自定义计划。 有关更多详细信息，请参阅[计划文档](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html)。

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>如果我们增加部署，Azure 容错域将如何映射到 pod 位置，以确保某个服务的所有 pod 在单个容错域中出现故障时不会被挖使。

使用 Azure 中的虚拟机规模集时，默认情况下有五个容错域。 规模集中的每个虚拟机实例将被放入这些容错域之一。 这可确保部署到群集中的计算节点的应用程序将被置于单独的容错域中。

有关更多详细信息，请参阅为[虚拟机规模集选择合适数量的容错域](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains)。

## <a name="is-there-a-way-to-manage-pod-placement"></a>是否有一种方法来管理 pod 位置？

客户能够以客户管理员身份获取节点和查看标签。 这将提供一种方法来针对规模集中的任何 VM。

使用特定标签时必须注意：

- 不得使用主机名。 主机名经常随升级和更新一起旋转，并保证更改。

- 如果客户有针对特定标签或部署策略的请求，则可以完成此操作，但目前不支持工程设计工作。

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>ARO 群集中的最大 pod 数是多少？ARO 中每个节点的最大 pod 数是多少？

 Azure Red Hat OpenShift 3.11 的每个节点限制为 50-pod， [aro 具有20个计算节点限制](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available)，因此 cap 群集中支持的最大 pod 数为 50 * 20 = 1000。

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>可以在专用 VNET 上指定部署的 IP 范围，以避免与其他公司 Vnet 发生一次对等互连的冲突？

Azure Red Hat OpenShift 支持 VNET 对等互连，并允许客户向对等机提供 VNET，以及 OpenShift 网络将在其中运行的 VNET CIDR。

由 ARO 创建的 VNET 将受到保护，不会接受配置更改。 对等互连的 VNET 由客户控制并驻留在其订阅中。

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>群集是否位于客户订阅中？ 

Azure 托管应用程序与客户订阅处于锁定的资源组中。 客户可以在该 RG 中查看对象，但不能修改。

## <a name="is-the-sdn-module-configurable"></a>SDN 模块是否可配置？

SDN 为 openshift-ovs-es-networkpolicy，不可配置。

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>哪些 UNIX 权限（在 IaaS 中）可用于主机/基础节点/应用节点？

不适用于此产品/服务。 禁止访问节点。

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>我们有哪些 OCP 权限？ 群集管理？ 项目-管理员？

有关详细信息，请参阅 Azure Red Hat OpenShift[群集管理概述](https://docs.openshift.com/aro/admin_guide/index.html)。

## <a name="which-kind-of-federation-with-ldap"></a>LDAP 有哪种类型的联合？

这可以通过 Azure AD 集成实现。 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>ARO 中是否有任何与其他客户共享的元素？ 或者一切都是独立的吗？

每个 Azure Red Hat OpenShift 群集专用于给定的客户，并在客户的订阅中。 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>能否选择任何持久性存储解决方案，如 OCS？ 

有两个存储类可供选择： Azure 磁盘和 Azure 文件。

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>群集是如何更新的（包括专业和不安全的）？

请参阅[什么是一般升级过程？](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>ARO 使用哪些 Azure 负载均衡器？是标准版还是基本版，是否可配置？

ARO 使用标准的 Azure 负载均衡器，它是不可配置的。

## <a name="can-aro-use-netapp-based-storage"></a>ARO 是否可以使用基于 NetApp 的存储？

目前唯一受支持的存储选项是 Azure 磁盘和 Azure 文件存储类。 


