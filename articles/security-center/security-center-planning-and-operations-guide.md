---
title: 安全中心规划和操作指南
description: 本文档介绍了如何在采用 Azure 安全中心之前进行规划，并介绍了有关日常操作的注意事项。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: e33cd64da32dcb918d30cd44f413748f719023b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84771285"
---
# <a name="planning-and-operations-guide"></a>规划和操作指南
本指南适用于计划使用 Azure 安全中心的信息技术 (IT) 专业人员、IT 架构师、信息安全分析师和云管理员。


## <a name="planning-guide"></a>规划指南
本指南介绍如何通过一系列任务，根据组织的安全要求和云管理模型优化安全中心的使用。 若要充分利用安全中心，必须了解在需要进行安全开发和操作、监视、管理和事件响应的情况下，组织中的不同个人或团队是如何使用服务的。 规划安全中心的使用时，需要考虑的重要方面包括：

* 安全角色和访问控制
* 安全策略和建议
* 数据收集和存储
* 载入非 Azure 资源
* 持续安全监视
* 事件响应

下一部分介绍如何针对每个方面进行计划，并根据要求应用相关建议。


> [!NOTE]
> 阅读 [Azure Security Center frequently asked questions (FAQ)](faq-general.md) （Azure 安全中心常见问题 (FAQ)），了解一系列常见问题，这些问题在设计和规划阶段可能也会有用。

## <a name="security-roles-and-access-controls"></a>安全角色和访问控制
很多个人和团队可能会使用安全中心执行不同的安全相关任务，具体取决于组织的大小和结构。 下图示例性地说明了各种虚构性的人员及其相应的角色和安全责任：

![角色](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-new.png)

这些人员通过安全中心行使不同的责任。 例如：

Jeff（工作负荷所有者）

* 管理云工作负荷及其相关资源
* 负责根据公司安全策略实施和维护各种保护措施

**Ellen（首席信息安全官/首席信息官）**

* 负责公司安全的各个方面
* 需要跨云工作负荷了解公司的安全状况
* 需要了解各种主要攻击和风险

**David（IT 安全）**

* 制定公司安全策略，确保实施适当的保护措施
* 监视合规性
* 生成领导力报表或审核报表

**Judy（安全操作）**

* 全天候监视和响应安全警报
* 将案例上报到云工作负荷所有者或 IT 安全分析师

**Sam（安全分析师）**

* 调查各种攻击
* 与云工作负荷所有者协作应用补救措施

安全中心使用[基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-portal.md) 提供可在 Azure 中分配给用户、组和服务的[内置角色](../role-based-access-control/built-in-roles.md)。 用户打开安全中心时，只能看到有权访问的资源的相关信息。 这意味着，可以将资源所属的订阅或资源组的“所有者”、“参与者”或“读者”角色分配给用户。 除这些角色外，还有两个特定的安全中心角色：

- 安全读取者：属于此角色的用户只能查看安全中心配置（包括建议、警报、策略和运行状况），无法进行更改。
- 安全管理员：与安全读取者一样，但它还可更新安全策略，消除建议和警报。

上述安全中心角色无权访问存储、Web 和移动或物联网等其他 Azure 服务区域。

根据上图介绍的人员，需要以下 RBAC：

Jeff（工作负荷所有者）

* 资源组所有者/参与者

**Ellen（首席信息安全官/首席信息官）**

* 订阅所有者/参与者或安全管理员

**David（IT 安全）**

* 订阅所有者/参与者或安全管理员

**Judy（安全操作）**

* 查看警报的订阅读取者或安全读取者
* 需要订阅所有者/参与者或安全管理员身份以消除警报

**Sam（安全分析师）**

* 查看警报的订阅读者
* 需要订阅所有者/参与者身份以消除警报
* 可能需要工作区的访问权限

其他一些需要考虑的重要信息：

* 只有订阅所有者/参与者和安全管理员可以编辑安全策略。
* 只有订阅和资源组的所有者和参与者可以应用针对某个资源的安全建议。

使用安全中心的 RBAC 规划访问控制时，请确保了解组织中的哪些人员会使用安全中心。 另外还需了解这些人员所执行的任务的类型，才能进行相应的 RBAC 配置。

> [!NOTE]
> 对于需要完成任务的用户，建议尽可能为其分配权限最小的角色。 例如，如果用户只需查看资源的安全状况信息而不需执行操作（例如应用建议或编辑策略），则应为其分配“读者”角色。
>
>

## <a name="security-policies-and-recommendations"></a>安全策略和建议
安全策略定义了工作负载的相应配置，有助于确保用户遵守公司或法规方面的安全要求。 在安全中心内，可以定义 Azure 订阅策略，即根据工作负载类型或数据机密性量身定制。

安全中心策略由以下部分组成：
- [数据收集](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection)：代理预配和数据收集设置。
- [安全策略](https://docs.microsoft.com/azure/security-center/security-center-policies)：可以通过 [Azure Policy](../governance/policy/overview.md) 来决定安全中心所监视和建议的控件，还可以通过 Azure Policy 来创建新的定义、定义其他策略，以及跨管理组分配策略。
- [电子邮件通知](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)：安全联系人和通知设置。
- [定价层](https://docs.microsoft.com/azure/security-center/security-center-pricing)：在选择定价时，可以选择“免费”或“标准”，这决定了相应范围内的资源可以使用哪些安全中心功能（可以针对订阅、资源组和工作区进行指定）。

> [!NOTE]
> 指定安全联系人可以确保在发生安全事件时，Azure 能够联系到贵组织中的合适人员。 若要详细了解如何启用此建议，请阅读 [Provide security contact details in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) （在 Azure 安全中心提供安全方面的联系细节）。

### <a name="security-policies-definitions-and-recommendations"></a>安全策略定义和建议
安全中心自动为每个 Azure 订阅创建默认的安全策略。 可以在安全中心编辑该策略，也可以使用 Azure Policy 创建新的定义、定义其他策略、跨管理组（可以代表整个组织、组织中的某个业务部门，等等）分配策略，以及跨相应范围监视对这些策略的遵循情况。

在配置安全策略之前，请查看每项 [安全建议](https://docs.microsoft.com/azure/security-center/security-center-recommendations)，确定这些策略是否适合各种订阅和资源组。 此外，请务必了解解决安全建议应采取的行动，以及组织中负责采纳新建议并采取必要措施的人员。

## <a name="data-collection-and-storage"></a>数据收集和存储
Azure 安全中心使用 Log Analytics 代理（Azure Monitor 服务同样使用此代理）从虚拟机中收集安全数据。 通过此代理[收集的数据](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection)将存储在 Log Analytics 工作区中。

### <a name="agent"></a>Agent

如果在安全策略中启用了自动预配，则会在所有支持的 Azure Vm 和创建的任何新 Vm 上安装 Log Analytics 代理（适用于[Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents)或[Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)）。 如果 VM 或计算机已安装 Log Analytics 代理，则 Azure 安全中心将利用当前安装的代理。 代理的进程设计为无干扰性，对 VM 性能的影响非常小。

适用于 Windows 的 Log Analytics 代理要求使用 TCP 端口443。 有关其他详细信息，请参阅[故障排除文章](security-center-troubleshooting-guide.md)。

如需在某个时候禁用数据收集功能，可在安全策略中将其关闭。 但是，由于其他 Azure 管理和监视服务可能使用 Log Analytics 代理，因此在安全中心关闭数据收集时，代理不会自动卸载。 必要时可手动卸载代理。

> [!NOTE]
> 若要查找受支持 VM 的列表，请阅读 [Azure 安全中心常见问题解答 (FAQ)](faq-vms.md)。

### <a name="workspace"></a>工作区

工作区是一种 Azure 资源，用作数据容器。 你或组织中的其他成员可以使用多个工作区，管理收集自所有或部分 IT 基础结构的不同数据集。

通过 Log Analytics 代理（代表 Azure 安全中心）收集的数据存储在与 Azure 订阅关联的现有 Log Analytics 工作区或新工作区中，具体取决于 VM 的地理位置。

在 Azure 门户中，可浏览查看 Log Analytics 工作区的列表，其中包括 Azure 安全中心创建的任何工作区。 系统会为新工作区创建相关资源组。 二者均遵循此命名约定：

* 工作区：DefaultWorkspace-[subscription-ID]-[geo]
* 资源组：DefaultResourceGroup-[geo]

对于 Azure 安全中心创建的工作区，数据将保留 30 天。 对于现有工作区，保留期取决于工作区定价层。 还可以根据需要使用现有工作区。

> [!NOTE]
> Microsoft 坚决承诺保护此类数据的隐私和安全性。 从编程到服务运营，Microsoft 都严格遵守相关法规与安全准则。 有关数据处理和隐私的详细信息，请参阅 [Azure 安全中心数据安全](security-center-data-security.md)。
>

## <a name="onboarding-non-azure-resources"></a>载入非 Azure 资源

安全中心可以监视非 Azure 计算机的安全状态，但首先需要载入这些资源。 请阅读[载入到 Azure 安全中心标准层以增强安全性](https://docs.microsoft.com/azure/security-center/security-center-onboarding#onboard-non-azure-computers)，详细了解如何载入非 Azure 资源。

## <a name="ongoing-security-monitoring"></a>持续安全监视
对安全中心建议进行初始配置和应用以后，下一步是考虑安全中心的操作过程。

“安全中心概览”提供了一个统一的视图，介绍了已连接的所有 Azure 资源和非 Azure 资源的安全情况。 以下示例显示了一个有许多问题需要解决的环境：

![仪表板](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig11.png)

> [!NOTE]
> 安全中心不会干扰正常的操作过程，而是被动监视部署，根据启用的安全策略提供建议。

首次选择为当前 Azure 环境使用安全中心时，请确保查看所有建议，这些建议可在 "**建议**" 页中完成。

计划访问威胁智能选项，将其作为日常安全操作的一部分。 可以在其中确定对环境的安全威胁，例如，确定特定计算机是否为僵尸网络的一部分。

### <a name="monitoring-for-new-or-changed-resources"></a>监视新的或更改的资源

大多数 Azure 环境是动态的，其中资源会定期创建、开启或关闭、重新配置和更改。 安全中心可确保用户能够查看这些新资源的安全状态。

将新资源（VM、SQL 数据库）添加到 Azure 环境时，安全中心会自动发现这些资源，并开始监视其安全性。 这还包括 PaaS Web 角色和辅助角色。 如果在 [安全策略](tutorial-security-policy.md)中启用了数据收集功能，则会自动为虚拟机启用更多监视功能。

此外还应定期监视现有资源，确定可能造成安全风险、偏离建议基线和安全警报的配置更改。 

### <a name="hardening-access-and-applications"></a>强化对访问权限和应用程序的控制

在进行安全操作时，还应采取预防性措施，限制对 VM 的访问，并控制在 VM 上运行的应用程序。 锁定到 Azure VM 的入站流量即可降低受攻击的风险，同时可以轻松进行访问，视需要连接到 VM。 使用[实时 VM](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) 访问功能，以强化 VM 访问控制。

可以使用[自适应应用程序控制](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)来限制可在 Azure 中的 VM 上运行的应用程序。 除其他优势外，这种控制还强化了 VM 抵御恶意软件侵害的能力。 安全中心可以使用机器学习分析在 VM 中运行的进程，以帮助创建允许列表规则。


## <a name="incident-response"></a>事件响应
安全中心会检测威胁并在威胁出现时向用户发出警报。 组织应监视是否有新的安全警报，并根据需要采取行动，进一步进行调查，或采取应对攻击的补救措施。 有关安全中心威胁防护工作原理的详细信息，请参阅 [Azure 安全中心如何检测和响应威胁](security-center-alerts-overview.md#detect-threats)。

尽管本文不能帮助你创建自己的事件响应计划，但我们将在云生命周期中使用 Microsoft Azure 安全响应作为事件响应阶段的基础。 下图显示了这些阶段：

![可疑活动](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> 若要构建自己的事件响应计划，用户可以使用国家标准和技术协会 (NIST) 提供的 [Computer Security Incident Handling Guide](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) （计算机安全事件处理指南）作为参考。
>

可以在以下阶段使用安全中心警报：

* **检测**：确定一个或多个资源中的可疑活动。
* **评估**：进行初始评估，了解可疑活动的详细信息。
* **诊断**：通过补救步骤采用技术过程解决问题。

每个安全警报所提供的信息都可以用来更好地了解攻击的性质，并提供可能的缓解措施建议。 某些警报还提供链接，单击这些链接即可获取更多信息或访问 Azure 中的其他信息源。 可使用提供的信息进一步研究并开始缓解操作，还可搜索存储于工作区中的安全相关数据。

下面的示例演示了正在发生的可疑的 RDP 活动：

![可疑活动](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

此页显示的详细信息包括攻击发生的时间、源主机名、目标 VM，并提供了建议步骤。 在某些情况下，攻击的源信息可能为空。 阅读 [Missing Source Information in Azure Security Center Alerts](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) （Azure 安全中心警报中缺少源信息），了解此类行为的详细信息。

也可从此页开始调查，以便更好地了解攻击的时间线、攻击是如何发生的、哪些系统可能受损、使用了哪些凭据，并查看整个攻击链的图形表示方式。

确定受损系统后，可以运行之前创建的[工作流自动化](workflow-automation.md)。 这些是可在警报触发后从安全中心执行的过程的集合。

在[如何利用 Azure 安全中心 & Microsoft Operations Management Suite 事件响应](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703)视频中，你可以看到一些演示，它们可帮助你了解如何在这些阶段中使用安全中心。

> [!NOTE]
> 阅读[管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md)，了解有关如何使用安全中心功能在事件响应过程中为您提供帮助的详细信息。
>
>

## <a name="next-steps"></a>后续步骤
本文档介绍如何进行规划，为采用安全中心做准备。 若要了解有关安全中心的详细信息，请参阅以下文章：

* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Security health monitoring in Azure Security Center](security-center-monitoring.md) （Azure 安全中心的安全运行状况监视）- 了解如何监视 Azure 资源的运行状况。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) - 了解如何监视合作伙伴解决方案的运行状态。
* [Azure Security Center FAQ](faq-general.md) （Azure 安全中心常见问题）- 查找有关如何使用服务的常见问题。
* [Azure 安全性博客](https://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章。
