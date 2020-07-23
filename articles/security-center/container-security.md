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
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: c01ed6dbbd6e1f7febfb99df11d2ee67cb1e5465
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800591"
---
# <a name="container-security-in-security-center"></a>安全中心的容器安全性

Azure 安全中心是用于保护容器安全的 Azure 本机解决方案。 安全中心可以保护以下容器资源类型：



|资源 |名称  |详细信息  |
|:---------:|---------|---------|
|![容器主机](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)|容器主机（运行 Docker 的虚拟机）|安全中心扫描 Docker 配置，并提供已评估的所有失败规则列表让你洞察错误配置。 安全中心提供指导来帮助你快速解决这些问题，因此可节省时间。 安全中心持续评估 Docker 配置，并提供其最新状态。|
|![Kubernetes 服务](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)|Azure Kubernetes 服务 (AKS) 群集|利用安全中心为标准层用户提供的[可选 AKS 捆绑](azure-kubernetes-service-integration.md)，更深入地了解 AKS 节点、云流量和安全控制。|
|![容器注册表](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)|Azure 容器注册表（ACR）注册表|利用安全中心为标准层用户提供的[可选 acr 捆绑包](azure-kubernetes-service-integration.md)，更深入地了解基于 ARM 的 acr 注册表中映像的漏洞。|
||||


本文介绍如何使用这些捆绑包来改善、监视和维护容器及其应用的安全性。 你将了解 Azure 安全中心如何在容器安全性的以下核心方面提供帮助：

- [漏洞管理 - 扫描容器映像](#vulnerability-management---scanning-container-images)
- [环境强化-持续监视 Docker 配置和 Kubernetes 群集](#environment-hardening)
- [运行时保护 - 实时检测威胁](#run-time-protection---real-time-threat-detection)

[![Azure 安全中心的“容器安全性”选项卡](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

有关如何使用这些功能的说明，请参阅[监视容器的安全性](monitor-container-security.md)。

## <a name="vulnerability-management---scanning-container-images"></a>漏洞管理 - 扫描容器映像
若要监视基于 ARM 的 Azure 容器注册表，请确保在安全中心的标准层上（请参阅[定价](/azure/security-center/security-center-pricing)）。 然后，启用可选的容器注册表捆绑包。 推送新映像时，安全中心会使用行业领先的漏洞扫描供应商 Qualys 提供的扫描程序来扫描映像。

当通过 Qualys 或安全中心发现问题时，你将在安全中心仪表板中收到通知。 安全中心会针对每个漏洞提供可行的建议、严重性分类，以及有关如何修正问题的指南。 若要详细了解安全中心针对容器提供的建议，请参阅[建议的参考列表](recommendations-reference.md#recs-containers)。

安全中心会对扫描程序的扫描结果进行筛选和分类。 当映像正常运行时，安全中心会将其标为正常。 安全中心仅为存在待解决问题的映像生成安全建议。 安全中心仅在出现问题时发出通知，这样会降低发送不必要的信息警报的可能性。

## <a name="environment-hardening"></a>环境强化

### <a name="continuous-monitoring-of-your-docker-configuration"></a>持续监视 Docker 配置
Azure 安全中心会识别在 IaaS Linux VM 上或其他运行 Docker 容器的 Linux 计算机上承载的非托管容器。 安全中心会持续评估这些容器的配置。 然后，它会将其与 [Internet 安全中心 (CIS) 的 Docker 基准](https://www.cisecurity.org/benchmark/docker/)进行比较。

安全中心包含 CIS 的 Docker 基准的完整规则集，并会在容器不符合控件标准的情况下发出警报。 在发现错误配置时，安全中心会生成安全建议。 请使用**建议页面**来查看建议和修正问题。 也可在“容器”选项卡上查看建议，该选项卡会显示使用 Docker 部署的所有虚拟机。 

若要详细了解可能会针对此功能显示的相关的安全中心建议，请参阅建议参考表的[容器部分](recommendations-reference.md#recs-containers)。

浏览 VM 的安全问题时，安全中心会提供计算机上有关容器的其他信息。 此类信息包括 Docker 版本以及主机上运行的映像数。 

>[!NOTE]
> 这些 CIS 基准检查不会在 AKS 托管实例或 Databricks 托管 VM 上运行。

### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>持续监视 Kubernetes 群集
安全中心可以与 Azure Kubernetes 服务 (AKS) 协同工作，后者是 Microsoft 的托管容器业务流程服务，用于开发、部署和管理容器化应用程序。

AKS 提供安全控制，并且可用于了解群集的安全状况。 安全中心使用这些功能来完成以下任务：
* 持续监视 AKS 群集的配置
* 生成符合行业标准的安全建议

若要详细了解可能会针对此功能显示的相关的安全中心建议，请参阅建议参考表的[容器部分](recommendations-reference.md#recs-containers)。

## <a name="run-time-protection---real-time-threat-detection"></a>运行时保护 - 实时检测威胁

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]




## <a name="next-steps"></a>后续步骤

本概述介绍了 Azure 安全中心的容器安全核心元素。 继续[了解如何监视容器的安全性](monitor-container-security.md)。
> [!div class="nextstepaction"]
> [监视容器的安全性](monitor-container-security.md)