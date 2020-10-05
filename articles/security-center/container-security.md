---
title: Azure 安全中心的容器安全性 | Microsoft Docs
description: 了解 Azure 安全中心的容器安全功能。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 6b57428aeba702dc8cf06ec4ae7984854a94ac7a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91449181"
---
# <a name="container-security-in-security-center"></a>安全中心的容器安全性

Azure 安全中心是用于保护容器安全的 Azure 原生解决方案。

安全中心可以保护以下类型的容器资源：

| 资源类型 | 安全中心提供的保护 |
|:--------------------:|-----------|
| ![Kubernetes 服务](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)<br>**Azure Kubernetes 服务 (AKS) 群集** | - 持续评估 AKS 群集的配置以发现和直观显示错误配置，并提供帮助解决任何发现的问题的指导信息。<br>[深入了解如何通过安全建议强化环境](#environment-hardening)。<br><br>- 面向 AKS 群集和 Linux 节点的威胁防护。 针对可疑活动的警报由可选的[适用于 Kubernetes 的 Azure Defender](defender-for-kubernetes-introduction.md) 提供。<br>[深入了解面向 AKS 节点和群集的运行时保护](#run-time-protection-for-aks-nodes-and-clusters)。|
| ![容器主机](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)<br>**容器主机**<br>（运行 Docker 的 VM） | - 持续评估 Docker 配置以发现和直观显示错误配置，并通过[适用于服务器的 Azure Defender](defender-for-servers-introduction.md) 提供帮助解决任何发现的问题的指导信息。<br>[深入了解如何通过安全建议强化环境](#environment-hardening)。|
| ![容器注册表](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)<br>**Azure 容器注册表 (ACR) 的注册表** | - 通过可选的[适用于容器注册表的Azure Defender](defender-for-container-registries-introduction.md)，提供漏洞评估和管理工具，用于基于 Azure 资源管理器的 ACR 注册表中的映像。<br>[详细了解如何扫描容器映像漏洞](#vulnerability-management---scanning-container-images)。 |
|||

本文介绍如何将安全中心与适用于容器注册表、服务器和 Kubernetes 的 Azure Defender 计划（可选）结合使用，来改善、监视和维护容器及其应用的安全性。

你将了解 Azure 安全中心如何在容器安全性的以下核心方面提供帮助：

- [漏洞管理 - 扫描容器映像](#vulnerability-management---scanning-container-images)
- [环境强化](#environment-hardening)
- [面向 AKS 节点和群集的运行时保护](#run-time-protection-for-aks-nodes-and-clusters)

以下屏幕截图显示了“资产清单”页以及受安全中心保护的各种类型的容器资源。

:::image type="content" source="./media/container-security/container-security-tab.png" alt-text="安全中心“资产清单”页中与容器相关的资源" lightbox="./media/container-security/container-security-tab.png":::

## <a name="vulnerability-management---scanning-container-images"></a>漏洞管理 - 扫描容器映像

若要监视基于 Azure 资源管理器的 Azure 容器注册表中的映像，请启用[适用于容器注册表的 Azure Defender](defender-for-container-registries-introduction.md)。 安全中心扫描在过去 30 天内拉取的、推送到注册表中或导入的任何映像。 集成扫描程序由业界领先的漏洞扫描供应商 Qualys 提供。

当 Qualys 或安全中心发现问题时，你将在 [Azure Defender 仪表板](azure-defender-dashboard.md)中收到通知。 安全中心会针对每个漏洞提供可行的建议、严重性分类，以及有关如何修正问题的指南。 若要详细了解安全中心针对容器提供的建议，请参阅[建议的参考列表](recommendations-reference.md#recs-containers)。

安全中心会对扫描程序的扫描结果进行筛选和分类。 当映像正常运行时，安全中心会将其标为正常。 安全中心仅为存在待解决问题的映像生成安全建议。 安全中心仅在出现问题时发出通知，这样会降低发送不必要的信息警报的可能性。

## <a name="environment-hardening"></a>环境强化

### <a name="continuous-monitoring-of-your-docker-configuration"></a>持续监视 Docker 配置

Azure 安全中心会识别在 IaaS Linux VM 上或其他运行 Docker 容器的 Linux 计算机上承载的非托管容器。 安全中心会持续评估这些容器的配置。 然后，它会将其与 [Internet 安全中心 (CIS) 的 Docker 基准](https://www.cisecurity.org/benchmark/docker/)进行比较。

安全中心包含 CIS 的 Docker 基准的完整规则集，并会在容器不符合控件标准的情况下发出警报。 在发现错误配置时，安全中心会生成安全建议。 使用安全中心的建议页面来查看建议和修正问题。 不会对 AKS 托管的实例或 Databricks 托管的 VM 运行 CIS 基准检查。

若要详细了解可能会针对此功能显示的相关的安全中心建议，请参阅建议参考表的[容器部分](recommendations-reference.md#recs-containers)。

浏览 VM 的安全问题时，安全中心会提供计算机上有关容器的其他信息。 此类信息包括 Docker 版本以及主机上运行的映像数。 

若要监视 IaaS Linux VM 上承载的非托管容器，请启用可选的[适用于服务器的 Azure Defender](defender-for-servers-introduction.md)。


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>持续监视 Kubernetes 群集
安全中心可以与 Azure Kubernetes 服务 (AKS) 协同工作，后者是 Microsoft 的托管容器业务流程服务，用于开发、部署和管理容器化应用程序。

AKS 提供安全控制，并且可用于了解群集的安全状况。 安全中心使用这些功能来完成以下任务：
* 持续监视 AKS 群集的配置
* 生成符合行业标准的安全建议

若要详细了解可能会针对此功能显示的相关的安全中心建议，请参阅建议参考表的[容器部分](recommendations-reference.md#recs-containers)。

###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>使用 Kubernetes 准入控制实现工作负载保护最佳做法

安装**适用于 Kubernetes 的 Azure 策略加载项**，获取一系列有助于保护 Kubernetes 容器工作载的建议。

如[此用于 Kubernetes 的 Azure Policy 页](../governance/policy/concepts/policy-for-kubernetes.md)中所述，加载项扩展了 [开放策略代理](https://www.openpolicyagent.org/)的开源 [Gatekeeper v3](https://github.com/open-policy-agent/gatekeeper) 准入控制器 webhook。 Kubernetes 准入控制器是强制实施群集使用方式的插件。 此加载项注册为 Kubernetes 准入控制的 web 挂钩，并使你能够以集中一致的方式在群集上应用大规模强制性操作和安全措施。 

在 AKS 群集上安装了加载项后，将按照预先定义的一组最佳做法监视对 Kubernetes API 服务器的每个请求，然后再将其保存到群集。 然后，可以配置为强制实施最佳做法，并规定将其用于未来的工作负载。 

例如，可以规定不应创建特权容器，并且阻止以后的任何请求。

参阅[保护 Kubernetes 工作负载](kubernetes-workload-protections.md)，了解详细信息。


## <a name="run-time-protection-for-aks-nodes-and-clusters"></a>面向 AKS 节点和群集的运行时保护

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>后续步骤

通过此概述性介绍，你了解了 Azure 安全中心容器安全性的核心元素。 如需查看相关材料，请参阅：

- [适用于 Kubernetes 的 Azure Defender 简介](defender-for-kubernetes-introduction.md)
- [适用于容器注册表的 Azure Defender 简介](defender-for-container-registries-introduction.md)