---
title: Azure 容器实例安全注意事项
description: 保护 Azure 容器实例的映像和机密的建议, 以及任何容器平台的一般安全注意事项
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/29/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 618d3a901698e46760d970f6d4fbc4157c5d2ea3
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325922"
---
# <a name="security-considerations-for-azure-container-instances"></a>Azure 容器实例的安全注意事项

本文介绍使用 Azure 容器实例运行容器应用的安全注意事项。 主题包括：

> [!div class="checklist"]
> * 用于管理 Azure 容器实例的映像和机密的**安全建议**
> * 容器**生态系统**整个容器生命周期的注意事项, 适用于任何容器平台

## <a name="security-recommendations-for-azure-container-instances"></a>Azure 容器实例的安全建议

### <a name="use-a-private-registry"></a>使用专用注册表

容器是基于一个或多个存储库中存储的映像构建的。 这些存储库可以属于公共注册表, 如[Docker Hub](https://hub.docker.com)或专用注册表。 [Docker 受信任注册表](https://docs.docker.com/datacenter/dtr/2.0/)是专用注册表的例子，它可以安装在本地或者安装在虚拟私有云中。 你还可以使用基于云的专用容器注册表服务, 包括[Azure 容器注册表](../container-registry/container-registry-intro.md)。 

公开提供的容器映像不保证安全性。 容器映像包含多个软件层, 并且每个软件层都可能有漏洞。 为了帮助降低攻击威胁, 你应该从专用注册表 (如 Azure 容器注册表或 Docker 受信任的注册表) 存储和检索映像。 除了提供托管的专用注册表, Azure 容器注册表还支持[基于服务主体的身份验证](../container-registry/container-registry-authentication.md), Azure Active Directory 基本身份验证流。 此身份验证包括针对只读 (请求)、写入 (推送) 和所有者权限的基于角色的访问。

### <a name="monitor-and-scan-container-images"></a>监视和扫描容器映像

可以通过 Azure Marketplace 获取安全监视和扫描解决方案, 如[Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview)和[浅绿安全性](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview)。 你可以使用它们来扫描专用注册表中的容器映像, 并识别潜在的漏洞。 了解不同解决方案提供的扫描深度很重要。 

### <a name="protect-credentials"></a>保护凭据

容器可以分散到多个群集和 Azure 区域。 因此, 必须保护登录名或 API 访问所需的凭据, 例如密码或令牌。 请确保只有特权用户可以访问传输和静止的容器。 清点所有凭据机密, 并要求开发人员使用专为容器平台设计的新兴密钥管理工具。  确保你的解决方案包含加密数据库、传输中的机密数据的 TLS 加密, 以及基于角色的最低权限[访问控制](../role-based-access-control/overview.md)。 [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md)是一种云服务, 用于保护容器化应用程序的加密密钥和机密 (例如证书、连接字符串和密码)。 由于此数据是敏感和业务关键的, 因此可以安全地访问密钥保管库, 以便只有经过授权的应用程序和用户可以访问它们。

## <a name="considerations-for-the-container-ecosystem"></a>容器生态系统的注意事项

以下安全措施能够有效地实现并有效地进行管理, 有助于保护和保护容器生态系统。 这些度量值适用于整个容器生命周期, 从开发到生产部署, 再到一系列容器协调器、主机和平台。 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>在容器开发生命周期中使用漏洞管理 

通过在整个容器开发生命周期中使用有效的漏洞管理, 你可以在问题变得更严重的问题之前, 提高发现和解决安全问题的机会。 

### <a name="scan-for-vulnerabilities"></a>扫描漏洞 

此时会发现新的漏洞, 因此扫描和识别漏洞是一种持续的过程。 在整个容器生命周期中合并漏洞扫描:

* 作为开发管道中的最后一项检查, 你应该在将映像推送到公共或专用注册表之前, 对容器执行漏洞扫描。 
* 继续扫描注册表中的容器映像, 以确定在开发过程中因某种原因而丢失的任何缺陷, 并解决容器映像中使用的代码中可能存在的任何新发现的漏洞。  

### <a name="map-image-vulnerabilities-to-running-containers"></a>将映像漏洞映射到正在运行的容器 

你需要有一种方法, 将容器映像中标识的漏洞映射到运行容器, 因此可以缓解或解决安全问题。  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>确保仅在您的环境中使用已批准的映像 

容器生态系统中有足够的更改和变动, 同时也不允许使用未知容器。 仅允许批准的容器映像。 准备好用于监视和阻止使用未经批准的容器映像的工具和过程。 

减小攻击面并防止开发人员造成严重安全错误的一种有效方法是控制容器映像到开发环境的流动。 例如, 你可以将单个 Linux 发行版批准为基本映像, 最好是精益 (Alpine 或 CoreOS, 而不是 Ubuntu), 以最大程度地减少潜在攻击面。 

映像签名或指纹可提供一系列保管环节, 使你能够验证容器的完整性。 例如, Azure 容器注册表支持 Docker 的[内容信任](https://docs.docker.com/engine/security/trust/content_trust)模式, 这允许映像发布者对推送到注册表中的映像进行签名, 并使用映像使用者来仅请求已签名的映像。

### <a name="permit-only-approved-registries"></a>仅允许批准的注册表 

确保你的环境仅使用批准的映像的扩展是只允许使用批准的容器注册表。 如果需要使用批准的容器注册表, 则会限制引入未知漏洞或安全问题的可能性, 从而降低风险。 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>确保映像在整个生命周期中的完整性 

在整个容器生命周期中管理安全性的一部分是在注册表中确保容器映像的完整性, 并在更改或将其部署到生产环境中。 

* 应该不允许在生产环境中运行具有安全漏洞的映像, 甚至不允许在生产环境中运行。 理想情况下, 在生产环境中部署的所有映像都应该保存在一个可供选择的专用注册表中。 保持较小的生产映像数量, 以确保能够有效地对其进行管理。

* 由于很难从公开提供的容器映像中找出软件源, 因此请从源构建映像, 以确保了解层的来源。 自制容器映像中出现漏洞时，客户可以更快地找到解决途径。 使用公共映像时, 客户需要找到公共映像的根以修复它, 或从发布者那里获取其他安全映像。 

* 在应用程序的生存期内, 在生产环境中部署的全面扫描映像不一定是最新的。 系统可能会针对映像的层报告以前并不知道的或者在生产部署后才引入的安全漏洞。 

  定期审核在生产环境中部署的映像, 以确定过期或未在一段时间内更新的映像。 你可以使用蓝绿色部署方法和滚动升级机制来更新容器映像, 而不会造成停机。 你可以使用上一节中所述的工具来扫描图像。 

* 使用带有集成安全扫描的持续集成 (CI) 管道构建安全映像, 并将其推送到专用注册表。 CI 解决方案中内置的漏洞扫描可确保将通过所有测试的映像推送到从中部署了生产工作负荷的专用注册表中。 

  CI 管道故障确保不会将易受攻击的映像推送到用于生产工作负荷部署的专用注册表。 如果有大量映像, 它还会自动执行映像安全扫描。 相比之下，在映像中手动审核安全漏洞的过程可能相当繁琐且容易出错。 

### <a name="enforce-least-privileges-in-runtime"></a>在运行时强制执行最低权限 

最小特权的概念是一种基本的安全性最佳实践, 适用于容器。 当攻击者利用漏洞时, 它通常为攻击者提供与被破坏的应用程序或进程的访问权限和特权。 确保容器以最低权限运行, 而完成工作所需的访问权限可降低风险。 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>通过删除不需要的权限来减少容器攻击面 

还可以通过删除容器运行时中任何未使用的或不必要的进程或特权, 来最大程度地减少潜在攻击面。 特权容器以 root 身份运行。 如果恶意用户或工作负荷在特权容器中转义, 则容器将在该系统上以 root 身份运行。

### <a name="whitelist-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>允许容器访问或运行的允许列表文件和可执行文件 

减少变量数或未知值有助于维护稳定可靠的环境。 限制容器以便仅可访问或运行预先批准或白名单文件和可执行文件, 这是限制风险暴露的一种行之有效的方法。  

从一开始就实现了白名单时, 管理白名单要容易得多。 当你了解应用程序正常运行所需的文件和可执行文件时, 白名单提供控制和可管理性的度量。 

白名单不仅减少了攻击面, 还可以为异常提供基线, 并防止出现 "干扰邻居" 和容器专题讨论情况。 

### <a name="enforce-network-segmentation-on-running-containers"></a>在运行的容器上强制网络分段  

为了帮助保护某个子网中的容器免受另一个子网中的安全风险, 请维护网络分段 (或 nano 分段) 或运行中的容器之间的隔离。 若要在需要满足合规性要求的行业中使用容器, 也可能需要维护网络分段。  

例如, "合作伙伴" 工具[水绿色](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview)为 nano 分段提供自动化方法。 浅绿色监视运行时中的容器网络活动。 它标识与其他容器、服务、IP 地址和公共 internet 之间的所有入站和出站网络连接。 Nano-根据监视的流量自动创建分段。 

### <a name="monitor-container-activity-and-user-access"></a>监视容器活动和用户访问 

与任何 IT 环境一样, 你应一致地监视活动和用户对容器生态系统的访问, 以快速识别任何可疑或恶意活动。 Azure 提供容器监视解决方案, 其中包括:

* [容器的 Azure Monitor](../azure-monitor/insights/container-insights-overview.md) , 用于监视部署到 Azure Kubernetes 服务 (AKS) 上托管的 Kubernetes 环境中的工作负荷性能。 用于容器的 Azure Monitor 通过 Metrics API 从 Kubernetes 中提供的控制器、节点和容器收集内存和处理器指标，来提供性能可见性。 

* [Azure 容器监视解决方案](../azure-monitor/insights/containers.md)可帮助你查看和管理单个位置中的其他 Docker 和 Windows 容器主机。 例如：

  * 查看详细的审核信息, 其中显示了与容器一起使用的命令。 
  * 通过查看和搜索集中式日志来排查容器问题, 而无需远程查看 Docker 或 Windows 主机。  
  * 查找可能干扰并消耗主机上的过量资源的容器。
  * 查看容器的集中式 CPU、内存、存储和网络使用情况和性能信息。  

  此解决方案支持容器协调器, 其中包括 Docker Swarm、DC/OS、非托管 Kubernetes、Service Fabric 和 Red Hat OpenShift。 

### <a name="monitor-container-resource-activity"></a>监视容器资源活动 

监视资源活动, 如文件、网络和容器访问的其他资源。 监视资源活动和消耗对性能监视和安全措施都很有用。 

使用 [Azure Monitor](../azure-monitor/overview.md) 可收集指标、活动日志和诊断日志，为 Azure 服务启用核心监视。 例如，可以通过活动日志了解新资源的创建或修改时间。 

可通过指标获取不同资源（甚至包括虚拟机中的操作系统）的性能统计信息。 可以使用 Azure 门户中的某个资源管理器查看此数据，还可以基于这些指标创建警报。 Azure Monitor 提供最快的指标管道 (5 分钟下降到1分钟), 因此应将其用于时间关键警报和通知。 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>记录所有容器管理用户访问以进行审核 

维护对容器生态系统、容器注册表和容器映像的管理访问权限的准确审核线索。 出于审核目的, 此类日志可能是必需的, 并且在任何安全事件之后都将有用。 可以使用[Azure 容器监视解决方案](../azure-monitor/insights/containers.md)实现此目的。 

## <a name="next-steps"></a>后续步骤

* 详细了解如何通过[Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/)和[浅绿色安全](https://www.aquasec.com/solutions/azure-container-security/)解决方案管理容器漏洞。

* 详细了解[Azure 中的容器安全性](https://azure.microsoft.com/resources/container-security-in-microsoft-azure/)。