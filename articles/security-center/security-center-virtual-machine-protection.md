---
title: 在 Azure 安全中心保护计算机和应用程序 | Microsoft Docs
description: 本文档介绍 Azure 安全中心内提供的，可帮助保护虚拟机和计算机以及 Web 应用和应用服务环境的建议。
services: security-center
documentationcenter: na
author: monhaber
manager: MBaldwin
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/27/2019
ms.author: monhaber
ms.openlocfilehash: 411fc025f5a25e961f69f5e6f66a9f6d115689a7
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55487737"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>在 Azure 安全中心保护计算机和应用程序
Azure 安全中心可分析 Azure 资源的安全状态。 在安全中心识别潜在的安全漏洞时，它会创建一些建议，这些建议会指导完成配置所需控件的过程。 建议适用于以下 Azure 资源类型：虚拟机 (VM) 和计算机、应用程序、网络、SQL，以及“标识和访问”。

本文介绍适用于计算机和应用程序的建议。

## <a name="monitoring-security-health"></a>监视安全运行状况
可以在“安全中心 - 概述”仪表板上监视资源的安全状态。 “资源”部分提供识别到的问题数，以及每种资源类型的安全状态。

可以选择“建议”来查看所有问题的列表。 有关如何应用建议的详细信息，请参阅[在 Azure 安全中心实施安全建议](security-center-recommendations.md)。

有关计算和应用服务建议的完整列表，请参阅[建议](security-center-virtual-machine-recommendations.md)。

若要继续，请在“资源”或安全中心主菜单中选择“计算和应用”。
![安全中心仪表板](./media/security-center-virtual-machine-recommendations/overview.png)

## <a name="monitor-compute-and-app-services"></a>监视计算和应用服务
在“计算和应用”下面有下述选项卡：

- **概述**：安全中心识别的监视问题和建议。
- **VM 和计算机**：列出 VM 和计算机及其当前安全状态。
- **云服务**：列出安全中心监视的 Web 角色和辅助角色。
- **应用服务**：列出应用服务环境及其当前安全状态。
- **容器(预览)**：列出在 IaaS Linux 计算机上托管的容器，并对其 Docker 配置进行安全评估。
- **计算资源(预览)**：列出针对计算资源（例如 Service Fabric 群集和事件中心）的建议。

若要继续，请在“资源安全机制”下选择“计算和应用”。

![计算](./media/security-center-virtual-machine-recommendations/compute.png)

每个选项卡可能包含多个部分，在每个部分，可以选择单个选项详细查看可解决该特定问题的建议步骤。

### 不受监视的 VM 和计算机 <a name="unmonitored-vms-and-computers"></a>
如果某个 VM 或计算机未运行 Microsoft Monitoring Agent 扩展，则它不受安全中心的监视。 计算机上可能已安装了本地代理，例如 OMS 直接代理或 SCOM 代理。 装有这些代理的计算机被标识为未受监视，因为安全中心不完全支持这些代理。 若要充分利用安全中心的所有功能，需要使用 Microsoft Monitoring Agent 扩展。

除了已安装的本地代理，还可以在未受监视的 VM 或计算机上安装该扩展。 对两个代理进行相同的配置，将二者连接到同一工作区。 这样，安全中心就能与 Microsoft Monitoring Agent 扩展交互并收集数据。 请参阅[启用 VM 扩展](../azure-monitor/learn/quick-collect-azurevm.md)，获取如何安装 Microsoft Monitoring Agent 扩展的说明。

请参阅[监视代理运行状况问题](security-center-troubleshooting-guide.md#mon-agent)，详细了解安全中心无法成功监视那些已针对自动预配初始化的 VM 和计算机的原因。

### <a name="recommendations"></a>建议
此部分包含安全中心监视的每个 VM 和计算机、Web 和辅助角色、Azure 应用服务 Web 应用以及 Azure 应用服务环境适用的一系列建议。 第一列列出了建议。 第二列显示受该建议影响的资源总数。 第三列显示问题的严重性。

每条建议附带一组操作，可在选择该建议后执行这些操作。 例如，如果选择“缺失的系统更新”，则会显示缺少修补程序的 VM 和计算机数目，以及所缺更新的严重性。

“应用系统更新”以图形格式汇总了关键更新，分别适用于 Windows 和 Linux。 第二部分有一个表，其中包含以下信息：

- **名称**：所缺更新的名称。
- VM 和和计算机的数量**：缺少此更新的 VM 和计算机的总数。
- **更新严重性**：描述该特定建议的严重性：

    - **严重**：重要资源（应用程序、虚拟机或网络安全组）存在漏洞，需要引起注意。
    - **重要**：需要采取非关键步骤或额外步骤来完成某个过程或消除某个漏洞。
    - **中等**：漏洞需要解决，但不需立即处理。 （默认情况下，不显示严重性低的建议，但如果用户需要查看这些建议，可以将其筛选出来。）


- **状态**：此建议的当前状态：

    - **未解决**：此建议尚未得到处理。
    - **正在进行**：目前已将此建议应用到相关资源，不需要你采取行动。
    - **已解决**：已完成此建议。 （解决问题后，此条目将变暗）。

若要查看建议详细信息，请单击列表中缺失更新的名称。


> [!NOTE]
> 此处提供的安全建议与“建议”磁贴下的内容相同。 请参阅[在 Azure 安全中心实施安全建议](security-center-recommendations.md)，详细了解如何应用建议。
>
>

### <a name="vms-and-computers"></a>VM 和计算机
“VM 和计算机”部分提供所有 VM 和计算机建议的概述。 每一列代表一组建议。

![VM 和计算机建议](./media/security-center-virtual-machine-recommendations/vm-computers.png)

此列表中显示四种类型的图标：

![非 Azure 计算机](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) 非 Azure 计算机。

![Azure 资源管理器 VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure 资源管理器 VM。

![Azure 经典 VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Azure 经典 VM。


![从工作区中识别的 VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) 只能通过工作区（属于所查看的订阅）标识的 VM。 其中包括其他订阅中向此订阅中的工作区报告的 VM，以及使用 SCOM 直接代理安装的 VM（没有资源 ID）。

每条建议下面显示的图标有助于快速识别需要关注的 VM 和计算机以及建议的类型。 也可使用筛选器，按“资源类型”和“严重性”搜索列表。

若要向下钻取每个 VM 的安全建议，请单击相应的 VM。
此处显示 VM 或计算机的安全详细信息。 在底部可以看到建议的操作以及每个问题的严重性。
![云服务](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>云服务
对于云服务，会在操作系统版本过期时创建建议。

![云服务](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

如果确实提供了建议（在上一示例中不是这样），则需要遵循建议中的步骤更新操作系统版本。 如果有可用的更新，会出现警报（红色或橙色 - 取决于问题的严重性）。 如果选择 WebRole1（运行 Windows Server，将 Web 应用自动部署到 IIS）或 WorkerRole1（运行 Windows Server，将 Web 应用自动部署到 IIS）行中的此警报，则会显示有关此建议的更多详细信息。

若要查看有关此建议的更全面的说明，请在“说明”栏下单击“更新 OS 版本”。



![更新 OS 版本](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png)

### <a name="app-services"></a>应用程序服务
需要在订阅中启用应用服务，才能查看应用服务信息。 有关如何启用此功能的说明，请参阅[使用 Azure 安全中心保护应用服务](security-center-app-services.md)。
[!NOTE]
> 监视应用服务的功能以预览版提供，仅适用于安全中心的标准层。


在“应用服务”下，可以看到应用服务环境的列表，以及安全中心执行评估后提供的运行状况摘要。

![应用程序服务](./media/security-center-virtual-machine-recommendations/app-services.png)

此列表中显示三种类型的图标：

![应用服务环境](./media/security-center-virtual-machine-recommendations/ase.png) 应用服务环境。

![Web 应用程序](./media/security-center-virtual-machine-recommendations/web-app.png) Web 应用程序。

![函数应用程序](./media/security-center-virtual-machine-recommendations/function-app.png) 函数应用程序。

1. 选择 Web 应用程序。 此时会打开摘要视图，其中包含三个选项卡：

  - **建议**：基于安全中心执行的失败的评估。
  - **已通过的评估**：安全中心执行的、已通过的评估列表。
  - **不可用的评估**：由于出错或者建议与特定的应用服务不相关，而未能运行的评估列表。

  “建议”下面是选定 Web 应用程序的建议列表，以及每条建议的严重性。

  ![应用服务建议](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

2. 选择一条建议即可查看其说明，以及不正常资源、正常资源和未扫描资源的列表。

 - “已通过的评估”列下面是已通过的评估的列表。  这些评估的严重性始终为绿色。

 -  从评估说明列表、不正常和正常资源列表以及未扫描的资源列表中选择一个已通过的评估。 不正常的资源有一个对应的选项卡，但其中的列表始终为空，因为评估已经通过。

    ![应用服务修正](./media/security-center-virtual-machine-recommendations/app-service-remediation.png)


## <a name="compute-and-app-recommendations"></a>计算和应用建议
|资源类型|安全功能分数|建议|说明|
|----|----|----|----|
|应用服务|20|只能通过 HTTPS 访问 Web 应用程序|仅限通过 HTTPS 访问 Web 应用程序。|
|应用服务|20|应该只能通过 HTTPS 访问函数应用|仅限通过 HTTPS 访问函数应用。|
|应用服务|5|在应用服务中启用诊断日志|启用日志并将其保留长达一年。 这样便可以在发生安全事件或网络遭泄露时，重新创建活动线索用于调查目的。 |
|应用服务|10|应禁用 Web 应用程序的远程调试|如果不再需要使用 Web 应用程序的调试，请将其禁用。 远程调试需要在函数应用上打开入站端口。|
|应用服务|10|应禁用函数应用程序的远程调试|如果不再需要使用函数应用的调试，请将其禁用。 远程调试需要在函数应用上打开入站端口。|
|应用服务|10|为 Web 应用程序配置 IP 限制|定义允许访问应用程序的 IP 地址列表。 使用 IP 限制保护 Web 应用程序免受常见攻击的威胁。|
|应用服务|10|不允许所有 ('*') 资源访问你的应用程序| 不允许将 WEBSITE_LOAD_CERTIFICATES 参数设置为 ""。 将参数设置为 ‘’ 表示所有证书都将加载到 Web 应用程序个人证书存储。 这可能导致滥用最小特权原则，因为站点在运行时不太可能需要访问所有证书。|
|应用服务|20|CORS 不应允许所有资源都能访问你的 Web 应用程序|仅允许所需的域与 Web 应用程序交互。 跨源资源共享 (CORS) 不应允许所有域都能访问你的 Web 应用程序。|
|应用服务|20|CORS 不应允许所有资源都能访问函数应用| 仅允许所需的域与函数应用程序交互。 跨源资源共享 (CORS) 不应允许所有域都能访问你的函数应用程序。|
|计算资源 (Batch)|1|在 Batch 帐户中配置指标警报规则|在 Batch 帐户中配置指标警报规则并启用指标池删除完成事件和池删除启动事件|
|计算资源 (Service Fabric)|10|在 Service Fabric 中使用 Azure Active Directory 进行客户端身份验证|在 Service Fabric 中仅通过 Azure Active Directory 执行客户端身份验证。|
|计算资源（自动化帐户）|5| 启用自动化帐户加密|存储敏感数据时，请启用自动化帐户变量资产加密。|
|计算资源（负载均衡器）|5|在负载均衡器中启用诊断日志|启用日志并将其保留长达一年。 这样便可以在发生安全事件或网络遭泄露时，重新创建活动线索用于调查目的。 |
|计算资源（搜索）|5|在搜索服务中启用诊断日志|启用日志并将其保留长达一年。 这样便可以在发生安全事件或网络遭泄露时，重新创建活动线索用于调查目的。 |
|计算资源（服务总线）|5|在服务总线中启用诊断日志|启用日志并将其保留长达一年。 这样便可以在发生安全事件或网络遭泄露时，重新创建活动线索用于调查目的。 |
|计算资源（流分析）|5|在 Azure 流分析中启用诊断日志|启用日志并将其保留长达一年。 这样便可以在发生安全事件或网络遭泄露时，重新创建活动线索用于调查目的。 |
|计算资源 (Service Fabric)|5|在 Service Fabric 中启用诊断日志|启用日志并将其保留长达一年。 这样便可以在发生安全事件或网络遭泄露时，重新创建活动线索用于调查目的。 |
|计算资源 (Batch)|5|在 Batch 帐户中启用诊断日志|启用日志并将其保留长达一年。 这样便可以在发生安全事件或网络遭泄露时，重新创建活动线索用于调查目的。 |
|计算资源（事件中心）|5|在事件中心内启用诊断日志|启用日志并将其保留长达一年。 这样便可以在发生安全事件或网络遭泄露时，重新创建活动线索用于调查目的。 |
|计算资源（逻辑应用）|5|在逻辑应用中启用诊断日志|启用日志并将其保留长达一年。 这样便可以在发生安全事件或网络遭泄露时，重新创建活动线索用于调查目的。 |
|计算资源 (Service Fabric)|15|在 Service Fabric 中将 ClusterProtectionLevel 属性设置为 EncryptAndSign|Service Fabric 使用主要群集证书为节点之间的通信提供三个保护级别（None、Sign 和 EncryptAndSign）。  请设置保护级别，确保节点到节点的所有消息经过加密和数字签名。 |
|计算资源（服务总线）|1|从服务总线命名空间删除 RootManageSharedAccessKey 之外的所有授权规则 |服务总线客户端不应使用提供对命名空间中所有队列和主题的访问的命名空间级访问策略。 若要符合最低特权安全模型，应在实体级别针对队列和主题创建访问策略，以便仅提供对特定实体的访问。|
|计算资源（事件中心）|1|从事件中心命名空间删除 RootManageSharedAccessKey 之外的所有授权规则 |服务中心客户端不应使用提供对命名空间中所有队列和主题的访问的命名空间级访问策略。 若要符合最低特权安全模型，应在实体级别针对队列和主题创建访问策略，以便仅提供对特定实体的访问。|
|计算资源（事件中心）|5|对事件中心实体定义授权规则|审核事件中心实体的授权规则以授予最低访问权限。|
|计算机|50|在计算机上安装监视代理|安装监视代理以对每台计算机启用数据收集、更新扫描、基线扫描和终结点保护。|
|计算机|50|为订阅启用自动预配和数据收集 |为订阅中的计算机启用自动预配和数据收集，以便在添加到订阅的每台计算机上启用数据收集、更新扫描、基线扫描和终结点保护。|
|计算机|40|解决计算机上的监视代理运行状况问题|若要实现全面的安全中心保护，请遵照故障排除指南中的说明，解决计算机上的监视代理问题| 
|计算机|40|解决计算机上的终结点保护运行状况问题|若要实现全面的安全中心保护，请遵照故障排除指南中的说明，解决计算机上的监视代理问题。|
|计算机|40|解决计算机上缺少扫描数据的问题|排查虚拟机和计算机上缺少扫描数据的问题。 计算机上缺少扫描数据会导致缺少安全评估（例如更新扫描、基线扫描）和缺少终结点保护解决方案扫描。|
|计算机|40|在计算机上安装系统更新|安装缺少的系统安全和关键更新，以保护 Windows 和 Linux 虚拟机与计算机
|计算机|15|添加 Web 应用程序防火墙| 部署 Web 应用程序防火墙 (WAF) 解决方案以保护 Web 应用程序。 |
|计算机|40|更新云服务角色的 OS 版本|将云服务角色的操作系统(OS)版本更新为适用于 OS 系列的最新版本。|
|计算机|35|修复计算机上安全配置中的漏洞|修复计算机上安全配置的漏洞，以保护其免受攻击。 |
|计算机|35|修复容器上安全配置中的漏洞|修复安装了 Docker 的计算机上安全配置中的漏洞，使它们免受攻击。|
|计算机|25|启用自适应应用程序控制|启用应用程序控制，以控制哪些应用程序可在 Azure 中的 VM 上运行。 这有助于强化 VM 防范恶意软件的能力。 安全中心使用机器学习来分析每个 VM 上运行的应用程序，帮助你运用此智能来应用允许规则。 此功能简化了配置和维护应用程序允许规则的过程。|
|计算机|20|在计算机上安装终结点保护解决方案|在虚拟机上安装终结点保护解决方案，以保护其免受威胁和漏洞的侵害。|
|计算机|20|重启计算机以应用系统更新|重启计算机以应用系统更新并保护计算机免受漏洞攻击。|
|计算机|15|在虚拟机上应用磁盘加密|使用适用于 Windows 和 Linux 虚拟机的 Azure 磁盘加密来加密虚拟机磁盘。 Azure 磁盘加密 (ADE) 利用行业标准 Windows 的 BitLocker 功能和 Linux 的 DM-Crypt 功能提供 OS 磁盘和数据磁盘加密，以帮助保护数据，并实现组织在客户 Azure Key Vault 方面作出的安全性与合规性承诺。 当合规性与安全性政策要求使用加密密钥对数据进行端到端加密（包括加密临时磁盘，即本地附加的临时磁盘）时，请使用 Azure 磁盘加密。 或者，系统默认会使用 Azure 存储服务加密对托管磁盘进行静态加密，其中，加密密钥是 Azure 中的 Microsoft 托管密钥。 如果这符合你的合规性与安全性要求，则可以利用默认托管磁盘加密来满足要求。|
|计算机|30|在虚拟机上安装漏洞评估解决方案|在虚拟机上安装漏洞评估解决方案|
|计算机|15|添加 Web 应用程序防火墙| 部署 Web 应用程序防火墙 (WAF) 解决方案以保护 Web 应用程序。 |
|计算机|30|通过漏洞评估解决方案修复漏洞|会持续评估为其部署了漏洞评估第三方解决方案的虚拟机的应用程序和 OS 漏洞。 只要发现此类漏洞，就会在建议中提供详细信息。|
|计算机|30|在虚拟机上安装漏洞评估解决方案|在虚拟机上安装漏洞评估解决方案|
|计算机|1|将虚拟机迁移到新的 Azure 资源管理器资源|对虚拟机使用 Azure 资源管理器以提供安全增强功能，例如：更强的访问控制 (RBAC)、更好的审核、基于资源管理器的部署和治理、托管标识访问权限、用于存储机密的密钥保管库的访问权限、基于 Azure AD 的身份验证以及可实现更轻松安全管理的标记和资源组支持。 |
|计算机|30|通过漏洞评估解决方案修复漏洞|会持续评估为其部署了漏洞评估第三方解决方案的虚拟机的应用程序和 OS 漏洞。 只要发现此类漏洞，就会在建议中提供详细信息。|

 





## <a name="next-steps"></a>后续步骤
若要了解有关适用于其他 Azure 资源类型的建议的详细信息，请参阅以下内容：


* [了解虚拟机的 Azure 安全中心建议](security-center-virtual-machine-recommendations.md)
* [在 Azure 安全中心监视标识和访问](security-center-identity-access.md)
* [保护 Azure 安全中心中的网络](security-center-network-recommendations.md)
* [保护 Azure 安全中心中的 Azure SQL 服务](security-center-sql-service-recommendations.md)

若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](tutorial-security-policy.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [Azure 安全中心常见问题解答](security-center-faq.md) - 查找有关使用服务的常见问题。

