---
title: Azure 容器实例的安全注意事项
description: 若要保护用于 Azure 容器实例和一般安全注意事项适用于任何容器平台的图像和机密的建议
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/29/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: dc516277d79e37500e73e1aee6b88c908acb9b1c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943991"
---
# <a name="security-considerations-for-azure-container-instances"></a>Azure 容器实例的安全注意事项

本文介绍了使用 Azure 容器实例运行容器应用的安全注意事项。 主题包括：

> [!div class="checklist"]
> * **安全建议**用于管理 Azure 容器实例的映像和机密
> * **容器生态系统的注意事项**整个容器生命周期，适用于任何容器平台

## <a name="security-recommendations-for-azure-container-instances"></a>Azure 容器实例的安全建议

### <a name="use-a-private-registry"></a>使用专用注册表

容器是基于一个或多个存储库中存储的映像构建的。 这些存储库可以如隶属于公共注册表[Docker Hub](https://hub.docker.com)，或到专用注册表。 [Docker 受信任注册表](https://docs.docker.com/datacenter/dtr/2.0/)是专用注册表的例子，它可以安装在本地或者安装在虚拟私有云中。 此外可以使用基于云的专用容器注册表服务，包括[Azure 容器注册表](../container-registry/container-registry-intro.md)。 

公用容器映像不保证安全。 容器映像包含多个软件层，且每个软件层可能有漏洞。 为了帮助降低攻击的威胁，您应存储和从 Azure 容器注册表或 Docker 受信任注册表等专用注册表中检索图像。 除了提供托管的专用注册表，Azure 容器注册表支持[服务基于主体的身份验证](../container-registry/container-registry-authentication.md)通过基本身份验证流的 Azure Active Directory。 此身份验证包括只读的 （拉取）、 写入 （推送） 和所有者权限的基于角色的访问权限。

### <a name="monitor-and-scan-container-images"></a>监视和扫描容器映像

安全监视和扫描解决方案[Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview)并[Aqua Security](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview)可通过 Azure Marketplace。 可以使用它们来扫描专用注册表中的容器映像并识别潜在的漏洞。 请务必了解的扫描不同的解决方案提供深度。 

### <a name="protect-credentials"></a>保护凭据

容器可以分布在多个群集和 Azure 区域。 因此，必须确保安全登录名或 API 访问权限，如密码或令牌所需的凭据。 确保只有授权的用户可以访问这些容器在传输过程中和静止。 列出所有凭据机密信息的都清单，然后要求开发人员可以使用适用于容器平台设计的新兴机密管理工具。  请确保您的解决方案包括加密的数据库，TLS 加密机密数据在传输过程，并最小特权[基于角色的访问控制](../role-based-access-control/overview.md)。 [Azure 密钥保管库](../key-vault/key-vault-secure-your-key-vault.md)为容器化应用程序是云服务，用于保护加密密钥和机密 （例如证书、 连接字符串和密码）。 由于此数据是敏感和业务关键，安全访问你的密钥保管库，以便只有经过授权的应用程序和用户可以访问它们。

## <a name="considerations-for-the-container-ecosystem"></a>容器生态系统的注意事项

以下的安全措施，也实现有效的管理，可以帮助您保护容器生态系统。 这些度量值将应用于整个容器生命周期，从开发到生产部署，再到一系列容器业务流程协调程序、 主机和平台。 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>使用作为容器开发生命周期的一部分的漏洞管理 

通过使用有效的安全漏洞管理在容器开发生命周期，可以改善你确定和解决安全问题，变得更严重的问题之前的几率。 

### <a name="scan-for-vulnerabilities"></a>漏洞扫描 

新漏洞的发现所有的时间，因此扫描和识别漏洞是一个连续的过程。 将合并漏洞扫描整个容器生命周期：

* 作为一种开发管道中的最后一个检查，应将映像推送到公有或私有注册表之前在容器上执行漏洞扫描。 
* 继续扫描标识以某种方式在开发过程中缺少任何缺陷和解决在使用在容器映像中的代码可能存在的任何新发现的漏洞的注册表中的容器映像。  

### <a name="map-image-vulnerabilities-to-running-containers"></a>将映射到正在运行的容器映像漏洞 

您需要能够通过某种方式映射到正在运行的容器，因此可以缓解安全问题，或将其解析的容器映像中标识的漏洞。  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>确保在您的环境中使用已批准的映像 

没有足够的更改和容器生态系统，但不允许未知的容器的波动性。 允许仅已批准的容器映像。 具有工具和过程来监视并阻止使用未经批准的容器映像。 

减少攻击面并防止开发人员进行的关键安全错误的有效方法是控制流的容器映像到您的开发环境。 例如，可能会批准为基础的映像，最好是一个精简的单一 Linux 分发 (Alpine 或 CoreOS 而不是 Ubuntu)，以便针对潜在攻击面降至最低。 

映像签名或指纹识别可以提供可用于验证的容器的完整性的监管链。 例如，Azure 容器注册表支持 Docker[内容信任](https://docs.docker.com/engine/security/trust/content_trust)模型，它允许映像发布者进行签名的推送到注册表，映像和映像的使用者请求仅签名映像。

### <a name="permit-only-approved-registries"></a>允许只批准的注册表 

确保您的环境使用已批准的映像的扩展为允许仅使用已批准的容器注册表。 无需使用已批准的容器注册表，限制有关未知的漏洞或安全问题的介绍减少自己遭受风险。 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>确保在整个生命周期的映像的完整性 

管理整个容器生命周期中的安全的一部分是为了确保在注册表中的容器映像的完整性，因为它们会更改或部署到生产环境。 

* 不应允许包含漏洞，即使再小的映像在生产环境中运行。 理想情况下，在生产环境中部署的所有映像应都保存在选择几个可访问的专用注册表。 保留生产映像的数小，以确保它们可以有效地管理。

* 因为很难确定从公用容器映像的软件的来源，从源构建映像以确保能够识别层的原点。 自制容器映像中出现漏洞时，客户可以更快地找到解决途径。 使用公共映像时，客户需要找到公共映像来修复此错误，或从发布服务器获取另一个安全映像的根。 

* 不保证在生产环境中部署的、 经过全面扫描的映像必须是最新的应用程序生存期内。 系统可能会针对映像的层报告以前并不知道的或者在生产部署后才引入的安全漏洞。 

  定期审核生产环境，以识别已过期或尚未更新在一段时间中的映像中部署的映像。 您可能使用蓝绿部署方法和滚动升级机制来更新容器映像，而无需停机。 可以通过使用在上一部分中所述的工具来扫描图像。 

* 使用带有集成安全性的扫描，以生成安全映像并将它们推送到专用注册表的持续集成 (CI) 管道。 CI 解决方案中内置的漏洞扫描可确保将通过所有测试的映像推送到从中部署了生产工作负荷的专用注册表中。 

  CI 管道故障可确保不将有漏洞的映像推送到用于生产工作负荷部署的专用注册表。 它还可自动执行映像安全扫描是否存在大量的映像。 相比之下，在映像中手动审核安全漏洞的过程可能相当繁琐且容易出错。 

### <a name="enforce-least-privileges-in-runtime"></a>强制实施最低权限运行时中 

最低权限的概念是也适用于容器的基本安全最佳实践。 时攻击的漏洞，它通常使攻击者可以访问和权限等于的受攻击的应用程序或进程。 确保使用最低权限运行的容器和顺利完成工作所需访问权限减少自己遭受风险。 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>通过删除不需要的特权来减小容器攻击面 

您还可以通过从容器运行时删除任何未使用或不必要的进程或权限来尽量减少潜在的攻击面。 以 root 身份运行特权的容器。 如果恶意用户或工作负荷会转义特权容器中，容器将然后以 root 身份上运行该系统。

### <a name="whitelist-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>列入允许列表的文件和容器能够访问或运行的可执行文件 

减少的变量或未知情况可帮助你保持稳定、 可靠的环境。 限制容器，因此，他们可以访问或运行仅预批准或已列入允许列表的文件和可执行文件是限制对风险暴露行之有效的方法。  

它是要简单得多时实现从一开始管理白名单。 在了解哪些文件和可执行文件所需的应用程序才能正常工作，白名单提供控件和可管理性的度量的值。 

白名单不仅可减少攻击面，但可以也提供基线来查找异常情况并防止发生"干扰邻居"和容器分组方案的用例。 

### <a name="enforce-network-segmentation-on-running-containers"></a>强制执行网络分段上正在运行的容器  

若要保护一个子网中的容器中另一个子网的安全风险，维护网络分段 （或 nano 分段） 或运行的容器之间的分离。 维护网络分段可能还需要满足法规遵从性要求的行业中使用容器所必需的。  

例如，合作伙伴工具[Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview)为 nano 分段提供自动化的方法。 浅绿色监视容器运行时中的网络活动。 它标识对其他容器、 服务、 IP 地址和公共 internet 的所有入站和出站网络连接。 Nano 分段是基于监视的流量自动创建。 

### <a name="monitor-container-activity-and-user-access"></a>监视容器活动和用户访问权限 

与任何 IT 环境中，应持续到你的容器生态系统，用于快速确定任何可疑活动或恶意活动监视活动和用户访问权限。 Azure 提供了容器监视解决方案，包括：

* [用于容器的 azure 监视器](../azure-monitor/insights/container-insights-overview.md)监视工作负荷部署到托管 Azure Kubernetes 服务 (AKS) 上的 Kubernetes 环境的性能。 用于容器的 Azure Monitor 通过 Metrics API 从 Kubernetes 中提供的控制器、节点和容器收集内存和处理器指标，来提供性能可见性。 

* [Azure 容器监视解决方案](../azure-monitor/insights/containers.md)可帮助您查看和管理其他 Docker 和 Windows 容器主机在一个位置。 例如：

  * 查看显示了与容器一起使用的命令的详细的审核信息。 
  * 通过查看和搜索而无需远程查看 Docker 或 Windows 主机的集中式的日志来排查容器问题。  
  * 查找可能会干扰并花费较长的主机上的过多资源的容器。
  * 查看集中式的 CPU、 内存、 存储和用于容器的网络使用情况和性能信息。  

  该解决方案支持容器业务流程协调程序包括 Docker Swarm、 DC/OS、 非托管 Kubernetes、 Service Fabric 和 Red Hat OpenShift。 

### <a name="monitor-container-resource-activity"></a>监视容器资源活动 

监视资源活动，如文件、 网络和你的容器访问其他资源。 监视资源活动和使用情况非常有用的性能监视和作为一种安全措施。 

使用 [Azure Monitor](../azure-monitor/overview.md) 可收集指标、活动日志和诊断日志，为 Azure 服务启用核心监视。 例如，可以通过活动日志了解新资源的创建或修改时间。 

可通过指标获取不同资源（甚至包括虚拟机中的操作系统）的性能统计信息。 可以使用 Azure 门户中的某个资源管理器查看此数据，还可以基于这些指标创建警报。 Azure Monitor 提供最快的指标管道 （乃至 1 分钟为 5 分钟），因此应将其用于时间关键型警报和通知。 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>日志审核的所有容器管理用户访问的权限 

维护对容器生态系统中，容器注册表和容器映像管理访问权限的准确审核线索。 这些日志可能所需的审核目的，在任何安全事件后会用作法庭证据。 可以使用[Azure 容器监视解决方案](../azure-monitor/insights/containers.md)来实现此目的。 

## <a name="next-steps"></a>后续步骤

* 深入了解如何管理与安全解决方案从容器漏洞[Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/)并[Aqua Security](https://www.aquasec.com/solutions/azure-container-security/)。

* 详细了解如何[在 Azure 中的容器安全性](https://azure.microsoft.com/resources/container-security-in-microsoft-azure/)。