---
title: Azure 安全中心的容器安全性 |微软文档
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
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: d46e2a9820ec0c45d197f135428f1ace712b2fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125139"
---
# <a name="container-security-in-security-center"></a>安全中心的集装箱安全

Azure 安全中心是用于容器安全的 Azure 本机解决方案。 安全中心也是云工作负载、VM、服务器和容器安全性的最佳单一窗格体验。

本文介绍了安全中心如何帮助您改进、监视和维护容器及其应用的安全性。 您将了解安全中心如何帮助容器安全这些核心方面：

* 漏洞管理
* 容器环境的硬化
* 运行时保护

[![Azure 安全中心的容器安全选项卡](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

有关如何使用这些功能的说明，请参阅[监视容器的安全性](monitor-container-security.md)。

## <a name="vulnerability-management---scanning-container-images"></a>漏洞管理 - 扫描容器映像
要监视基于 ARM 的 Azure 容器注册表，请确保位于安全中心的标准层（请参阅[定价](/azure/security-center/security-center-pricing)）。 然后，启用可选的容器注册表捆绑包。 推送新映像时，安全中心使用业界领先的漏洞扫描供应商 Qualys 的扫描仪扫描图像。

当发现问题时（由 Qualys 或安全中心）时，您将在安全中心仪表板中收到通知。 对于每个漏洞，安全中心都提供可操作的建议以及严重性分类，以及如何修复问题的指导。 有关安全中心对容器的建议的详细信息，请参阅[建议的参考列表](recommendations-reference.md#recs-containers)。

安全中心对扫描仪的调查结果进行筛选和分类。 当图像正常运行时，安全中心将其标记为正常。 安全中心仅针对要解决的问题的图像生成安全建议。 通过仅在出现问题时通知，安全中心可降低出现不需要的信息警报的可能性。

## <a name="environment-hardening"></a>环境硬化

### <a name="continuous-monitoring-of-your-docker-configuration"></a>持续监控 Docker 配置
Azure 安全中心标识托管在 IaaS Linux VM 或其他运行 Docker 容器的 Linux 计算机上的非托管容器。 安全中心不断评估这些容器的配置。 然后，它比较他们与[互联网安全中心（CIS）Docker基准](https://www.cisecurity.org/benchmark/docker/)。

安全中心包括 CIS Docker 基准测试的整个规则集，如果容器不符合任何控件，则会发出警报。 当它发现配置错误时，安全中心将生成安全建议。 使用**建议页面**查看建议并修复问题。 您还将在 **"容器"** 选项卡上看到建议，该选项卡显示使用 Docker 部署的所有虚拟机。 

有关此功能可能显示的相关安全中心建议的详细信息，请参阅建议参考表的[容器部分](recommendations-reference.md#recs-containers)。

当您探索 VM 的安全问题时，安全中心会提供有关计算机上的容器的其他信息。 此类信息包括 Docker 版本和主机上运行的映像数。 

>[!NOTE]
> 这些 CIS 基准检查不会在 AKS 管理的实例或数据砖管理的 VM 上运行。

### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>持续监控库伯奈斯集群
安全中心与 Azure Kubernetes 服务 （AKS） 协同工作，这是 Microsoft 用于开发、部署和管理容器化应用程序的托管容器编排服务。

AKS 提供安全控制和对群集安全状态的可见性。 安全中心使用这些功能：
* 持续监控 AKS 群集的配置
* 生成符合行业标准的安全建议

有关此功能可能显示的相关安全中心建议的详细信息，请参阅建议参考表的[容器部分](recommendations-reference.md#recs-containers)。

## <a name="run-time-protection---real-time-threat-detection"></a>运行时保护 - 实时威胁检测

安全中心为您的容器化环境提供实时威胁检测，并针对可疑活动生成警报。 可以使用此信息快速补救安全问题，并提高容器的安全性。

我们在主机和 AKS 群集级别检测威胁。 有关详细信息，请参阅[Azure 容器的威胁检测](threat-protection.md#azure-containers)。


## <a name="container-security-faq"></a>集装箱安全常见问题解答

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Azure 安全中心可以扫描哪些类型的映像？
安全中心扫描提供 shell 访问基于 Linux 操作系统的图像。 

Qualys 扫描仪不支持超简约图像，如[Docker 划痕](https://hub.docker.com/_/scratch/)图像，或仅包含应用程序及其运行时依赖项的"无版本"映像，而没有包管理器、shell 或操作系统。

### <a name="how-does-azure-security-center-scan-an-image"></a>Azure 安全中心如何扫描映像？
从注册表中拉出映像。 然后，它与 Qualys 扫描仪在隔离的沙盒中运行，该扫描器提取已知漏洞的列表。

安全中心对扫描仪的调查结果进行筛选和分类。 当图像正常运行时，安全中心将其标记为正常。 安全中心仅针对要解决的问题的图像生成安全建议。 通过仅在出现问题时通知，安全中心可降低出现不需要的信息警报的可能性。

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Azure 安全中心扫描图像的频率如何？
每次推送都会触发图像扫描。

### <a name="can-i-get-the-scan-results-via-rest-api"></a>我能否通过 REST API 获取扫描结果？
是的。 结果在[子评估休息 API](/rest/api/securitycenter/subassessments/list/)下。 此外，还可以使用 Azure 资源图 （ARG），即类似于 Kusto 的 API，用于所有资源：查询可以获取特定扫描。
 

## <a name="next-steps"></a>后续步骤

要了解有关 Azure 安全中心中的容器安全性的更多详细信息，请参阅以下相关文章：

* 要查看容器相关资源的安全状态，请参阅["保护计算机和应用程序](security-center-virtual-machine-protection.md#containers)"的容器部分。

* [与 Azure 库伯奈斯服务集成](azure-kubernetes-service-integration.md)的详细信息

* [与 Azure 容器注册表集成](azure-container-registry-integration.md)的详细信息