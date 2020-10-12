---
title: 容器实例的安全注意事项
description: 有关保护 Azure 容器实例的映像和机密的建议，以及任何容器平台的一般性安全注意事项
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: f49f115e10326887cf4d23406437467256b7df2e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87922228"
---
# <a name="security-considerations-for-azure-container-instances"></a>Azure 容器实例的安全注意事项

本文介绍使用 Azure 容器实例运行容器应用时的安全注意事项。 主题包括：

> [!div class="checklist"]
> * 有关管理 Azure 容器实例的映像和机密的**安全建议**
> * 任何容器平台在整个容器生命周期内的**容器生态系统注意事项**

有关可帮助你改善部署安全态势的综合建议，请参阅[容器实例的 Azure 安全基线](security-baseline.md)。


## <a name="security-recommendations-for-azure-container-instances"></a>Azure 容器实例的安全建议

### <a name="use-a-private-registry"></a>使用专用注册表

容器是基于一个或多个存储库中存储的映像构建的。 这些存储库可以属于公共注册表（例如 [Docker Hub](https://hub.docker.com)），也可以属于专用注册表。 [Docker 受信任注册表](https://docs.docker.com/datacenter/dtr/)是专用注册表的例子，它可以安装在本地或者安装在虚拟私有云中。 还可以使用基于云的专用容器注册表服务，包括 [Azure 容器注册表](../container-registry/container-registry-intro.md)。 

公用的容器映像不保证安全性。 容器映像包括多个软件层，每个软件层可能有漏洞。 为帮助减少攻击风险，应在 Azure 容器注册表或 Docker 受信任注册表等专用注册表中存储和检索映像 除了提供托管的专用注册表以外，Azure 容器注册表还支持通过 Azure Active Directory 使用[基于服务主体的身份验证](../container-registry/container-registry-authentication.md)执行基本身份验证流。 此身份验证包括使用只读（提取）、写入（推送）和其他权限进行的基于角色的访问。

### <a name="monitor-and-scan-container-images"></a>监视和扫描容器映像

利用解决方案来扫描专用注册表中的容器映像并识别潜在漏洞。 了解不同解决方案提供的威胁检测的深度很重要。

例如，Azure 容器注册表可以选择[与 Azure 安全中心](../security-center/azure-container-registry-integration.md)集成，以便自动扫描已推送到注册表的所有 Linux 映像。 Azure 安全中心的集成 Qualys 扫描程序可以检测映像漏洞、对其进行分类，并提供修正指导。

还可以通过 Azure Marketplace 使用安全监视和图像扫描解决方案，如 [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) 和 [浅绿安全性](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) 。  

### <a name="protect-credentials"></a>保护凭据

容器可能分散在多个群集和 Azure 区域之间。 因此，必须保护登录或 API 访问所需的凭据，例如密码或令牌。 确保只有特权用户能够在传输中和静态状态下访问这些容器。 清点所有凭据机密，并要求开发人员使用专为容器平台设计的新兴机密管理工具。  请确保解决方案包含加密数据库、传输中的机密数据的 TLS 加密，以及 azure [RBAC)  (的最低权限 azure 基于角色的访问控制 ](../role-based-access-control/overview.md)。 [Azure Key Vault](../key-vault/general/secure-your-key-vault.md) 是一种云服务，用于保护容器化应用程序的加密密钥和机密（例如证书、连接字符串和密码）。 由于这些数据极其机密且对企业至关重要，因此请保护对 Key Vault 的访问，以便只有经过授权的应用程序和用户才能访问它们。

## <a name="considerations-for-the-container-ecosystem"></a>容器生态系统的注意事项

妥善实施并有效管理以下安全措施有助于保护容器生态系统的安全。 从开发到生产部署的整个容器生命周期内，这些措施都适用于一系列容器业务流程协调程序、主机和平台。 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>在容器开发生命周期中使用漏洞管理 

在整个容器开发生命周期内使用有效的漏洞管理，可以改善识别到的不利情况，并解决安全隐忧，避免更严重的问题发生。 

### <a name="scan-for-vulnerabilities"></a>扫描漏洞 

随时都可能会发现新的漏洞，因此，扫描和识别漏洞是一个持续的过程。 在整个容器生命周期内整合漏洞扫描功能：

* 在将映像推送到公共或专用注册表之前，作为开发管道中的最后一项检查，应该对容器执行漏洞扫描。 
* 持续扫描注册表中的容器映像，以识别在开发过程中疏忽掉的任何缺陷，并解决容器映像使用的代码中可能存在的任何新发现的漏洞。  

### <a name="map-image-vulnerabilities-to-running-containers"></a>将映像漏洞映射到正在运行的容器 

需通过某种方式，将容器映像中识别到的漏洞映射到正在运行的容器，以便可以缓解或解决安全问题。  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>确保在环境中仅使用已批准的映像 

容器生态系统中有足够的更改和变动，同时也不允许使用未知容器。 仅允许已批准的容器映像。 部署用于监视和阻止使用未经批准的容器映像的工具与流程。 

减小受攻击面并防止开发人员出现严重安全错误的有效方式是控制容器映像流入开发环境 例如，可以批准将单个 Linux 分发版用作基础映像，最好是精简的映像（Alpine 或 CoreOS，而不是 Ubuntu），以最大程度地减少潜在受攻击面。 

映像签名或指纹可提供监护链，使你能够验证容器的完整性。 例如，Azure 容器注册表支持 Docker 的[内容信任](https://docs.docker.com/engine/security/trust/content_trust)模型，使映像发布者能够为推送到注册表的映像签名，并使映像使用者仅提取已签名的映像。

### <a name="permit-only-approved-registries"></a>仅允许已批准的注册表 

确保环境仅使用已批准的映像的一种延伸做法是仅允许使用已批准的容器注册表。 要求使用已批准的容器注册表可以通过限制引入未知漏洞或安全问题的可能性，来减少风险因素。 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>确保整个生命周期内的映像完整性 

管理整个容器生命周期内的安全性的一部分工作是确保注册表中的容器映像在发生更改或部署到生产环境时的完整性。 

* 不应该允许在生产环境中运行包含漏洞的映像（即使是轻微的漏洞）。 最好是将生产环境中部署的所有映像保存在只有少数人可以访问的专用注册表中。 保存少量的生产映像，确保能够有效地对其进行管理。

* 由于很难从公开提供的容器映像中找出软件源，因此请从源构建映像，以确保了解层的来源。 自制容器映像中出现漏洞时，客户可以更快地找到解决途径。 使用公共映像时，客户需要找到公共映像的根才能修复漏洞，或者需要从发布者获取另一个安全映像。 

* 在生产环境中部署的、经过全面扫描的映像并不能保证在应用程序的生存期内保持最新状态。 系统可能会针对映像的层报告以前并不知道的或者在生产部署后才引入的安全漏洞。 

  定期审核生产环境中部署的映像，以识别过时的映像或有一段时间未更新的映像。 可以使用蓝绿部署方法和滚动升级机制来更新容器映像，这不会造成停机。 可以使用上一部分所述的工具来扫描映像。 

* 使用集成了安全扫描的持续集成 (CI) 管道生成安全映像并将其推送到专用注册表。 CI 解决方案中内置的漏洞扫描可确保将通过所有测试的映像推送到从中部署了生产工作负荷的专用注册表中。 

  CI 管道故障确保不会将易受攻击的映像推送到用于生产工作负荷部署的专用注册表。 如果有大量映像，它还会自动执行映像安全扫描。 相比之下，在映像中手动审核安全漏洞的过程可能相当繁琐且容易出错。 

### <a name="enforce-least-privileges-in-runtime"></a>在运行时中强制实施最低特权 

最低特权的概念是一种基本的安全最佳做法，也适用于容器。 当某个漏洞遭到利用时，它为攻击者提供的访问权限和特权相当于攻击者在遭到入侵的应用程序或进程中获得的权限。 确保容器使用完成工作所需的最低特权和访问权限运行可以减少风险因素。 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>通过删除不需要的特权来减小容器的受攻击面 

还可以通过从容器运行时中删除任何不使用或不必要的进程或特权，来最大程度地减小潜在受攻击面。 特权容器以 root 身份运行。 如果恶意用户或工作负荷侵入某个特权容器，则该容器就会在该系统上以 root 身份运行。

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>预先批准允许容器访问或运行的文件和可执行文件 

减少可变因素或未知因素的数量有助于维持稳定可靠的环境。 限制容器以使其只能访问或运行已预先批准的或已加入安全列表的文件和可执行文件，是限制风险因素的已证实方法。  

从一开始就实现了一个安全的安全管理程序，这种方法很容易。 安全列表提供控制措施和管理功能，因为你知道正常运行应用程序需要哪些文件和可执行文件。 

安全列表不仅可以减小受攻击面，而且还能提供异常状况的基线，并防止出现“干扰邻居”和容器入侵情形的用例。 

### <a name="enforce-network-segmentation-on-running-containers"></a>在运行的容器中强制网络分段  

为了防范一个子网中的容器在另一个子网中遇到安全风险，请在运行的容器之间保持网络分段（或 nano 分段）或隔离。 若要在需要满足合规要求的行业中使用容器，可能还需要保持网络分段。  

例如，"合作伙伴" 工具 [水绿色](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) 为 nano 分段提供自动化方法。 浅绿色监视运行时中的容器网络活动。 它标识与其他容器、服务、IP 地址和公共 internet 之间的所有入站和出站网络连接。 Nano-根据监视的流量自动创建分段。 

### <a name="monitor-container-activity-and-user-access"></a>监视容器活动和用户访问 

与使用任何 IT 环境时一样，应始终如一地监视容器生态系统中的活动和用户访问，以快速识别任何可疑或恶意活动。 Azure 提供容器监视解决方案，包括：

* [用于容器的 Azure Monitor](../azure-monitor/insights/container-insights-overview.md) 监视托管在 Azure Kubernetes 服务 (AKS) 上的 Kubernetes 环境中部署的工作负荷的性能。 用于容器的 Azure Monitor 通过 Metrics API 从 Kubernetes 中提供的控制器、节点和容器收集内存和处理器指标，来提供性能可见性。 

* [Azure 容器监视解决方案](../azure-monitor/insights/containers.md)可帮助你在单个位置查看和管理其他 Docker 与 Windows 容器主机。 例如：

  * 查看详细审核信息，其中显示了与容器一起使用的命令。 
  * 通过查看和搜索集中式日志来排查容器问题，而无需远程查看 Docker 或 Windows 主机。  
  * 在主机上查找可能具有干扰性并且占用过多资源的容器。
  * 查看容器的集中式 CPU、内存、存储器、网络使用情况和性能信息。  

  该解决方案支持容器业务流程协调程序，包括 Docker Swarm、DC/OS、非托管 Kubernetes、Service Fabric 和 Red Hat OpenShift。 

### <a name="monitor-container-resource-activity"></a>监视容器资源活动 

监视资源活动，例如，容器访问的文件、网络和其他资源。 监视资源活动和消耗量对于性能监视非常有用，可用作一种安全措施。 

使用 [Azure Monitor](../azure-monitor/overview.md) 可收集指标、活动日志和诊断日志，为 Azure 服务启用核心监视。 例如，可以通过活动日志了解新资源的创建或修改时间。 

  可通过指标获取不同资源（甚至包括虚拟机中的操作系统）的性能统计信息。 可以使用 Azure 门户中的某个资源管理器查看此数据，还可以基于这些指标创建警报。 Azure Monitor 提供最快的指标管道（5 分钟乃至 1 分钟），因此应将其用于时间关键型警报和通知。 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>记录所有容器管理用户访问以用于审核 

维护对容器生态系统（包括 Kubernetes 群集）、容器注册表和容器映像的管理访问的准确审核线索。 这些日志在审核时可能需要用到，在发生任何安全事件后可用作法庭证据。 Azure 解决方案包括：

* [将 Azure Kubernetes 服务与 Azure 安全中心集成](../security-center/azure-kubernetes-service-integration.md)来监视群集环境的安全配置并生成安全建议
* [Azure 容器监视解决方案](../azure-monitor/insights/containers.md)
* [Azure 容器实例](container-instances-log-analytics.md)和[azure 容器注册表](../container-registry/container-registry-diagnostics-audit-logs.md)的资源日志

## <a name="next-steps"></a>后续步骤

* 有关可帮助你改善部署安全态势的综合建议，请参阅[容器实例的 Azure 安全基线](security-baseline.md)。

* 详细了解如何使用 [Azure 安全中心](../security-center/container-security.md)在容器化环境中进行实时威胁检测。

* 详细了解如何使用 [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) 和 [Aqua Security](https://www.aquasec.com/solutions/azure-container-security/) 提供的解决方案来管理容器漏洞。
