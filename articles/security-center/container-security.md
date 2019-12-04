---
title: Azure 安全中心中的容器安全性 |Microsoft Docs
description: 了解 Azure 安全中心的容器安全功能。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 31e45906dfeafcd2af1651347dde9dc6d3c6fb7c
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769211"
---
# <a name="container-security-in-security-center"></a>安全中心的容器安全

Azure 安全中心是 Azure 本机解决方案，适用于容器安全性。 安全中心也是云工作负荷、Vm、服务器和容器的安全性的最佳单一窗格。

本文介绍如何改进、监视和维护容器及其应用的安全性。 你将了解安全中心如何帮助执行容器安全的这些核心方面：

* 漏洞管理
* 强化容器环境
* 运行时保护

[![Azure 安全中心的容器安全选项卡](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

## <a name="vulnerability-management---scanning-container-images-preview"></a>漏洞管理-扫描容器映像（预览版）
若要监视 Azure 容器注册表，请确保在安全中心的标准层上（请参阅[定价](https://docs.microsoft.com/azure/security-center/security-center-pricing.md)）。 然后启用可选的容器注册表绑定。 推送新映像后，安全中心将使用行业领先漏洞扫描供应商 Qualys 中的扫描仪扫描映像。

当通过 Qualys 或安全中心发现问题时，你将在安全中心仪表板中收到通知。 对于每个漏洞，安全中心都提供可操作的建议，以及严重性分类和有关如何修正问题的指南。 有关安全中心建议的详细信息，请参阅[此处](recommendations-compute-and-apps.md)的建议列表。

## <a name="environment-hardening"></a>环境强化

### <a name="continuous-monitoring-of-your-docker-configuration"></a>持续监视 Docker 配置
Azure 安全中心标识在 IaaS Linux Vm 或其他运行 Docker 容器的 Linux 计算机上托管的非托管容器。 安全中心会持续评估这些容器的配置。 然后，将它们与[Internet 安全（CIS） Docker 基准中心](https://www.cisecurity.org/benchmark/docker/)进行比较。 

安全中心包括 CIS Docker 基准的整个规则集，如果容器不满足任何控件，则会发出警报。 如果发现错误配置，安全中心会生成安全建议。 使用 "**建议" 页**可以查看建议和修正问题。 你还会在 "**容器**" 选项卡上看到显示部署了 Docker 的所有虚拟机的建议。 当你探索虚拟机上的安全问题时，安全中心会提供有关计算机上的容器的其他信息。 此类信息包括 Docker 版本和主机上运行的映像数。 有关建议的详细信息，请参阅[此处](https://docs.microsoft.com/azure/security-center/security-center-virtual-machine-protection)。 

>[!NOTE]
> 这些 CIS 基准检查不会在 AKS 管理的实例或 Databricks 托管的 Vm 上运行。

### <a name="continuous-monitoring-of-your-kubernetes-clusters-preview"></a>连续监视 Kubernetes 群集（预览版）
安全中心与 Azure Kubernetes 服务（AKS）一起工作，用于开发、部署和管理容器化应用程序。

AKS 提供安全控制和查看群集的安全状况。 安全中心使用以下功能：
* 持续监视 AKS 群集的配置
* 生成符合行业标准的安全建议

有关安全中心建议的详细信息，请参阅[虚拟机保护](security-center-virtual-machine-protection.md)。

## <a name="run-time-protection---real-time-threat-detection"></a>运行时保护-实时威胁检测

安全中心为容器化环境提供实时威胁检测，并生成可疑活动的警报。 可以使用此信息快速补救安全问题，并提高容器的安全性。

我们检测到主机和 AKS 群集级别的威胁。 有关完整详细信息，请参阅[Azure 容器服务](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-containers-)。


## <a name="to-view-the-security-posture-of-your-container-related-resources"></a>查看容器相关资源的安全状况
1.  打开安全中心的 "**计算 & 应用**" 页。
2.  单击 "**容器**" 选项卡。此时会显示 AKS 群集、ACR 注册表和运行 Docker 的 Vm 的状况。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 安全中心中的容器安全性，请参阅：
* [与 Azure Kubernetes 服务集成](azure-kubernetes-service-integration.md)的详细信息

* [与 Azure 容器注册表集成](azure-container-registry-integration.md)的详细信息