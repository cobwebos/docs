---
title: Azure 红帽开放移位常见问题
description: 以下是有关微软 Azure 红帽开放移位的常见问题解答
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f468cb294d79c44f92ef95437c0d88639a78b9a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77619475"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure 红帽开放移位常见问题解答

本文针对微软 Azure 红帽开放Shift的常见问题 （FAQ）。

## <a name="which-azure-regions-are-supported"></a>支持哪些 Azure 区域？

有关 Azure 红帽开放Shift支持的全局区域列表，请参阅[支持的资源](supported-resources.md#azure-regions)。

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>是否可以将群集部署到现有虚拟网络？

不是。 但是，您可以通过对等互连将 Azure 红帽 OpenShift 群集连接到现有 VNET。 有关详细信息[，请参阅将群集的虚拟网络连接到现有虚拟网络](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network)。

## <a name="what-cluster-operations-are-available"></a>哪些群集操作可用？

您只能向上或缩小计算节点的数量。 创建后不允许对`Microsoft.ContainerService/openShiftManagedClusters`资源进行其他修改。 计算节点的最大数量限制为 20。

## <a name="what-virtual-machine-sizes-can-i-use"></a>我可以使用哪些虚拟机大小？

有关可与 Azure 红帽 OpenShift 群集一起使用的虚拟机大小列表，请参阅[Azure 红帽 OpenShift 虚拟机大小](supported-resources.md#virtual-machine-sizes)。

## <a name="is-data-on-my-cluster-encrypted"></a>群集上的数据是否加密？

默认情况下，静态加密。 Azure 存储平台在保留数据之前会自动加密数据，并在检索前解密数据。 有关详细信息[，请参阅 Azure 存储服务加密以了解静态数据](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>我可以使用普罗米图斯/格拉法纳来监视我的应用程序吗？

可以，您可以在命名空间中部署 Prometheus，并在命名空间中监视应用程序。

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>我能否使用 Prometheus/Grafana 来监视与群集运行状况和容量相关的指标？

不，不是当前时间。

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Docker 注册表是否在外部可用，因此我可以使用 Jenkins 等工具？

Docker 注册表可从`https://docker-registry.apps.<clustername>.<region>.azmosa.io/`但是，未提供强大的存储持久性保证。 您还可以使用 Azure[容器注册表](https://azure.microsoft.com/services/container-registry/)。

## <a name="is-cross-namespace-networking-supported"></a>是否支持跨命名空间网络？

客户和单个项目管理员可以使用`NetworkPolicy`对象自定义跨命名空间网络（包括拒绝）。

## <a name="can-an-admin-manage-users-and-quotas"></a>管理员可以管理用户和配额吗？

是的。 Azure 红帽 OpenShift 管理员除了访问所有用户创建的项目外，还可以管理用户和配额。

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>是否可以将群集限制为仅某些 Azure AD 用户？

是的。 您可以通过配置 Azure AD 应用程序来限制哪些 Azure AD 用户可以登录到群集。 有关详细信息，请参阅[：将应用限制为一组用户](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-i-restrict-users-from-creating-projects"></a>我可以限制用户创建项目吗？

是的。 以 Azure 红帽 OpenShift 管理员身份登录到群集，并执行以下命令：

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

有关详细信息，请参阅有关[禁用自我预配的](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)OpenShift 文档。

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>群集是否可以跨多个 Azure 区域具有计算节点？

不是。 Azure 红帽 OpenShift 群集中的所有节点必须源自同一 Azure 区域。

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>主节点和基础结构节点的抽象性与 Azure Kubernetes 服务 （AKS） 一样？

不是。 所有资源（包括群集主资源）都在客户订阅中运行。 这些类型的资源被放入只读资源组中。

## <a name="is-open-service-broker-for-azure-osba-supported"></a>是否支持 Azure （OSBA） 的开放服务代理？

是的。 您可以将 OSBA 与 Azure 红帽开放Shift 一起使用。 有关详细信息[，请参阅打开服务代理。](https://github.com/Azure/open-service-broker-azure#openshift-project-template)

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>我尝试在不同的订阅中窥视到虚拟网络，但会出错`Failed to get vnet CIDR`。

在具有虚拟网络的订阅中，请确保向`Microsoft.ContainerService``az provider register -n Microsoft.ContainerService --wait` 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>什么是 Azure 红帽开放移位 （ARO） 维护过程？

ARO 有三种类型的维护：蚀刻数据的升级、备份和恢复以及云提供商启动的维护。

+ 升级包括软件升级和 CCV。 CVE 修正在启动时通过`yum update`运行进行，并提供即时缓解。  并行将创建新的映像生成，以便将来创建群集。

+ 蚀刻数据的备份和管理是一个自动化过程，可能需要群集停机时间，具体取决于操作。 如果从备份还原蚀刻数据库，将停机。 我们每小时备份蚀刻，并保留最后 6 小时的备份。

+ 云提供商发起的维护包括网络、存储和区域中断。 维护依赖于云提供商，并且依赖于提供商提供的更新。

## <a name="what-is-the-general-upgrade-process"></a>什么是常规升级过程？

运行升级应该是一个安全运行过程，并且不应中断群集服务。 当新版本可用或 CCV 尚未完成时，SRE 可以触发升级过程。

可用更新在阶段环境中进行测试，然后应用于生产群集。 应用时，将临时添加新节点，并旋转方式更新节点，以便 pod 维护副本计数。 遵循最佳实践有助于确保最短或无停机时间。

根据挂起的升级或更新的严重性，该过程可能有所不同，因为更新可能快速应用，以减轻服务对 CVE 的暴露。 新映像将作为群集升级异步生成、测试和推出。 除了这一点，紧急和计划维护之间没有区别。 计划中的维护不会与客户预先计划。

如果需要与客户沟通，可以通过 ICM 和电子邮件发送通知。

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>紧急维修窗口与计划维护窗口又如何？

我们不区分这两种类型的维护。 我们的团队全天候提供 365 服务，不使用传统的"非工作时间"维护窗口。

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>主机操作系统和 OpenShift 软件将如何更新？

主机操作系统和 OpenShift 软件通过我们的一般升级和映像生成过程进行更新。

## <a name="whats-the-process-to-reboot-the-updated-node"></a>重新启动更新的节点的过程是什么？

这应作为升级的一部分进行处理。

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>数据存储在 ARO 上的蚀刻中是否加密？

它在蚀刻级别上未加密。 当前不支持打开它的选项。 OpenShift 支持此功能，但需要进行工程工作才能在路线图上实现。 数据在磁盘级别加密。 有关详细信息，请参阅在[数据存储层加密数据](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html)。

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>基础 VM 的日志是否可以流式传输到客户日志分析系统？

Syslog、Docker 日志、日志和 dmesg 由托管服务处理，不会暴露给客户。

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>客户如何在节点级别访问 CPU/内存等指标，以采取措施扩展、调试问题等。我似乎无法`kubectl top`在 ARO 群集上运行。

客户可以使用命令`oc adm top nodes`或使用`kubectl top nodes`客户管理员群集角色访问节点级别的 CPU/内存指标。  客户还可以`pods`使用 命令`oc adm top pods`或`kubectl top pods`

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>ARO 的默认 pod 计划程序配置是什么？

ARO 使用在 OpenShift 中发货的默认计划程序。 ARO 中不支持几个其他机制。 有关详细信息，请参阅[默认计划程序文档](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler)和[主计划程序文档](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v6/data/master/etc/origin/master/scheduler.json)。

高级/自定义计划当前不受支持。 有关详细信息，请参阅[计划文档](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html)。

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>如果我们扩展部署，Azure 容错域如何映射到窗格放置，以确保服务的所有 Pod 不会因单个容错域中的故障而中断？

默认情况下，在 Azure 中使用虚拟机缩放集时有五个容错域。 规模集中的每个虚拟机实例都将放入这些容错域之一。 这可确保部署到群集中的计算节点的应用程序将放置在单独的容错域中。

有关详细信息[，请参阅为虚拟机规模集选择正确数量的容错域](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains)。

## <a name="is-there-a-way-to-manage-pod-placement"></a>是否有管理吊舱放置的方法？

客户能够获取节点和查看标签作为客户管理员。 这将提供一种以规模集中的任何 VM 为目标的方法。

使用特定标签时必须小心：

- 不得使用主机名。 主机名经常通过升级和更新旋转，并保证更改。

- 如果客户请求特定标签或部署策略，可以完成此任务，但需要工程工作，并且今天不支持。

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>ARO 群集中的最大窗格数是多少？ARO 中每个节点的最大窗格数是多少？

 Azure 红帽 OpenShift 3.11 具有每个节点 50 个 pod 限制[，ARO 具有 20 个计算节点限制](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available)，因此，将 ARO 群集中支持的最大 pod 数上限上限为 50*20 = 1000。

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>我们能否指定用于在专用 VNET 上部署的 IP 范围，以避免一旦对等互连而与其他公司 VNET 发生冲突？

Azure 红帽 OpenShift 支持 VNET 对等互连，并允许客户提供 VNET 以对等和 VNET CIDR 进行 OpenShift 网络运行。

ARO 创建的 VNET 将受到保护，不会接受配置更改。 对等 VNET 由客户控制并驻留在其订阅中。

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>群集是否驻留在客户订阅中？ 

Azure 托管应用程序与客户订阅一起位于锁定的资源组中。 客户可以查看该 RG 中的对象，但不能修改。

## <a name="is-the-sdn-module-configurable"></a>SDN 模块是可配置的吗？

SDN 是开放式移位-ovs 网络策略，不可配置。

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>哪些 UNIX 权限（IaaS 中）可用于主节点/Infra/应用节点？

不适用于此产品/ 禁止节点访问。

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>我们拥有哪些 OCP 权利？ 群集管理员？ 项目管理员？

有关详细信息，请参阅 Azure 红帽 OpenShift[群集管理概述](https://docs.openshift.com/aro/admin_guide/index.html)。

## <a name="which-kind-of-federation-with-ldap"></a>与 LDAP 的联盟类型？

这将通过 Azure AD 集成来实现。 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>ARO 中是否有任何元素与其他客户共享？ 还是一切都是独立的？

每个 Azure 红帽 OpenShift 群集都专用于给定客户，并生活在客户的订阅范围内。 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>我们可以选择任何持久性存储解决方案，如 OCS 吗？ 

可从以下两个存储类中选择：Azure 磁盘和 Azure 文件。

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>如何更新群集（包括因漏洞而成为主要和未成年人）？

请参阅[什么是常规升级过程？](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>ARO 使用什么 Azure 负载均衡器？是标准还是基本，它是可配置的？

ARO 使用标准 Azure 负载均衡器，并且不可配置。

## <a name="can-aro-use-netapp-based-storage"></a>ARO 可以使用基于 NetApp 的存储吗？

目前唯一支持的存储选项是 Azure 磁盘和 Azure 文件存储类。 


