---
title: "安全中心规划和操作指南 | Microsoft Docs"
description: "本文档介绍了如何在采用 Azure 安全中心之前进行规划，并介绍了有关日常操作的注意事项。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: f984e4a2-ac97-40bf-b281-2f7f473494c4
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/02/2018
ms.author: yurid
ms.openlocfilehash: 8a44542091ea4dddc214a4954766c63dacd3e2a2
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2018
---
# <a name="azure-security-center-planning-and-operations-guide"></a>Azure 安全中心规划和操作指南
本指南适用于其组织正计划使用 Azure 安全中心的信息技术 (IT) 专业人员、IT 架构师、信息安全分析师和云管理员。

    
## <a name="planning-guide"></a>规划指南
本指南介绍如何通过一系列步骤和任务，根据组织的安全要求和云管理模型优化安全中心的使用。 若要充分利用安全中心，必须了解在需要进行安全开发和操作、监视、管理和事件响应的情况下，组织中的不同个人或团队是如何使用服务的。 规划安全中心的使用时，需要考虑的重要方面包括：

* 安全角色和访问控制
* 安全策略和建议
* 数据收集和存储
* 持续使用的非 Azure 资源
* 持续安全监视
* 事件响应

下一部分介绍如何针对每个方面进行计划，并根据要求应用相关建议。


> [!NOTE]
> 阅读 [Azure Security Center frequently asked questions (FAQ)](security-center-faq.md) （Azure 安全中心常见问题 (FAQ)），了解一系列常见问题，这些问题在设计和规划阶段可能也会有用。
> 

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

安全中心使用[基于角色的访问控制 (RBAC)](../active-directory/role-based-access-control-configure.md) 提供可在 Azure 中分配给用户、组和服务的[内置角色](../active-directory/role-based-access-built-in-roles.md)。 用户打开安全中心时，只能看到有权访问的资源的相关信息。 这意味着，可以将资源所属的订阅或资源组的“所有者”、“参与者”或“读者”角色分配给用户。 除这些角色外，还有两个特定的安全中心角色：

- 安全读者：属于此角色的用户只能查看安全中心配置（包括建议、警报、策略和运行状况），无法进行更改。
- 安全管理员：与安全读者一样，但它还可更新安全策略，消除建议和警报。

上述安全中心角色无权访问存储、Web 和移动或物联网等其他 Azure 服务区域。  

> [!NOTE]
> 用户需要拥有至少一个订阅，并身为资源组所有者或参与者才能在 Azure 中看到安全中心。 
> 
> 

根据上图介绍的人员，需要以下 RBAC：

Jeff（工作负荷所有者）

* 资源组所有者/协作者

**David（IT 安全）**

* 订阅所有者/协作者或安全管理员

**Judy（安全操作）**

* 查看警报的订阅读者或安全读者
* 消除警报所需的订阅所有者/协作者或安全管理员

**Sam（安全分析师）**

* 查看警报的订阅读者
* 消除警报所需的订阅所有者/协作者
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
安全策略定义了工作负载的相应配置，有助于确保用户遵守公司或法规方面的安全要求。 可以在安全中心定义 Azure 订阅的策略，按照工作负荷的类型或数据的敏感程度对其进行调整。

安全中心策略包含以下组件：
- [数据收集](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection)：代理预配和数据收集设置。
- [安全策略](https://docs.microsoft.com/azure/security-center/security-center-policies)：可以通过 [Azure 策略](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)来决定安全中心所监视和建议的控件，还可以通过 Azure 策略来创建新的定义、定义其他策略，以及跨管理组分配策略。
- [电子邮件通知](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)：安全联系人和通知设置。
- [定价层](https://docs.microsoft.com/azure/security-center/security-center-pricing)：在选择定价时，可以选择“免费”或“标准”，这决定了相应范围内的资源可以使用哪些安全中心功能（可以针对订阅、资源组和工作区进行指定）。

> [!NOTE]
> 指定安全联系人可以确保在发生安全事件时，Azure 能够联系到贵组织中的合适人员。 若要详细了解如何启用此建议，请阅读 [Provide security contact details in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) （在 Azure 安全中心提供安全方面的联系细节）。

### <a name="security-policies-definitions-and-recommendations"></a>安全策略定义和建议
安全中心自动为每个 Azure 订阅创建默认的安全策略。 可以在安全中心编辑该策略，也可以使用 Azure 策略创建新的定义、定义其他策略、跨管理组（可以代表整个组织、组织中的某个业务部门，等等）分配策略，以及跨相应范围监视对这些策略的遵循情况。

在配置安全策略之前，请查看每项 [安全建议](https://docs.microsoft.com/azure/security-center/security-center-recommendations)，确定这些策略是否适合各种订阅和资源组。 此外，请务必了解解决安全建议应采取的行动，以及组织中负责采纳新建议并采取必要措施的人员。

## <a name="data-collection-and-storage"></a>数据收集和存储
Azure 安全中心使用 Microsoft Monitoring Agent（Operations Management Suite 和 Log Analytics 服务同样适用此代理）从虚拟机中收集安全数据。 通过此代理[收集的数据](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection)将存储在 Log Analytics 工作区中。

### <a name="agent"></a>代理

在安全策略中启用自动预配后，Microsoft Monitoring Agent（适用于 [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) 或 [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)）会安装在所有支持的 Azure VM 和新建的任何 VM 上。 如果 VM 或计算机已安装 Microsoft Monitoring Agent，Azure 安全中心会利用当前的已安装代理。 代理的过程设计为非入侵性，对 VM 性能的影响非常小。

适用于 Windows 的 Microsoft Monitoring Agent 需要使用 TCP 端口 443。 有关其他详细信息，请参阅[故障排除文章](security-center-troubleshooting-guide.md)。

如需在某个时候禁用数据收集功能，可在安全策略中将其关闭。 然而，由于其他 Azure 管理和监视服务可能使用 Microsoft Monitoring Agent，因此关闭安全中心数据收集后不会自动卸载代理。 必要时可手动卸载代理。

> [!NOTE]
> 若要查找受支持 VM 的列表，请阅读 [Azure 安全中心常见问题解答 (FAQ)](security-center-faq.md)。
> 

### <a name="workspace"></a>工作区

工作区是一种 Azure 资源，用作数据容器。 你或组织中的其他成员可以使用多个工作区，管理收集自所有或部分 IT 基础结构的不同数据集。

通过 Microsoft Monitoring Agent（代表 Azure 安全中心）收集的数据存储在与 Azure 订阅关联的现有 Log Analytics 工作区或新工作区中，具体取决于 VM 的地理位置。 

在 Azure 门户中，可浏览查看 Log Analytics 工作区的列表，其中包括 Azure 安全中心创建的任何工作区。 系统会为新工作区创建相关资源组。 二者均遵循此命名约定： 

* 工作区：DefaultWorkspace-[subscription-ID]-[geo]
* 资源组：DefaultResouceGroup-[geo]

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

如果首次选择为当前 Azure 环境使用安全中心，请务必查看所有建议，此操作可在“建议”磁贴中进行，也可按资源（“计算”、“网络”、“存储和数据”、“应用程序”）进行。

解决所有建议的问题以后，所有已解决问题的资源的“预防”部分应显示为绿色。 此时进行持续监视会变得更容易，因为用户只需根据“资源安全运行状况和建议”磁贴中的变化进行操作。

“检测”部分更具响应性。这些是与问题相关的警报，这些问题可能发生在现在，也可能发生在过去，并且被安全中心控件和第三方系统检测到过。 “安全警报”磁贴所显示的条形图代表检测到威胁的警报数，这些警报每天都会出现，分布在不同的严重性类别（低、中、高）。 有关安全警报的详细信息，请参阅[管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md)。

计划在进行日常安全操作时，访问[威胁智能](https://docs.microsoft.com/azure/security-center/security-center-threat-intel)选项。 可以在其中确定对环境的安全威胁，例如，确定特定计算机是否为僵尸网络的一部分。

### <a name="monitoring-for-new-or-changed-resources"></a>监视新的或更改的资源
大多数 Azure 环境是动态的，新资源会定期出现和消失，也会出现新配置或新变化等。安全中心可确保用户能够查看这些新资源的安全状态。

将新资源（VM、SQL 数据库）添加到 Azure 环境时，安全中心会自动发现这些资源，并开始监视其安全性。 这还包括 PaaS Web 角色和辅助角色。 如果在 [安全策略](security-center-policies.md)中启用了数据收集功能，则会自动为虚拟机启用更多监视功能。

![关键领域](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3-newUI.png)

1. 对于虚拟机，请单击“预防”部分的“计算”。 如果在启用数据或相关建议时出现问题，该问题会显示在“概述”选项卡和“监视建议”部分。
2. 查看“建议”，了解为新资源确定了何种安全风险（如果有）。
3. 将新的 VM 添加到环境时，只在一开始安装了操作系统，这很常见。 资源所有者可能需要一些时间来部署其他应用供这些 VM 使用。  理想情况下，用户应该知道此工作负荷的最终目的。 它将用作应用程序服务器？ 根据这个新的工作负荷的用途，可以启用相应的 **安全策略**，这在此工作流中是第三步。
4. 随着新的资源添加到 Azure 环境，新警报可能会显示在“安全警报”磁贴中。 应始终验证此磁贴中是否有新警报，并根据安全中心的建议采取措施。

此外还需定期监视现有资源的状态，确定造成安全风险、建议基线漂移和安全警报的配置更改。 从“安全中心”仪表板开始。 在这里需采取一致措施查看三大方面。

![操作](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4-newUI.png)

1. 可通过“预防”部分面板快速访问关键资源。 使用此选项来监视“计算”、“网络”、“存储和数据”以及“应用程序”。
2. 可以通过“建议”面板查看安全中心的建议。 在持续监视过程中，用户可能会发现并不是每天都有建议出现，这是正常的，因为在一开始设置安全中心时，所有建议的问题都已解决。 因此，此部分可能不是每天都有新信息，只需根据需要进行访问即可。
3. “检测”部分可能会特别频繁地或特别不频繁地发生更改。 请始终查看安全警报，根据安全中心建议采取行动。

### <a name="hardening-access-and-applications"></a>强化对访问权限和应用程序的控制

在进行安全操作时，还应采取预防性措施，限制对 VM 的访问，并控制在 VM 上运行的应用程序。 锁定到 Azure VM 的入站流量即可降低受攻击的风险，同时可以轻松进行访问，视需要连接到 VM。 使用[实时 VM](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) 访问功能，强化 VM 访问控制。 

可以通过[自适应应用程序控制](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)来控制哪些应用程序能够在位于 Azure 中的 VM 上运行，这样有很多好处，其中之一是能够增强 VM 对恶意软件的抵抗力。 安全中心使用机器学习来分析在 VM 中运行的进程，帮助你运用此智能来应用允许列表规则。


## <a name="incident-response"></a>事件响应
安全中心会检测威胁并在威胁出现时向用户发出警报。 组织应监视是否有新的安全警报，并根据需要采取行动，进一步进行调查，或采取应对攻击的补救措施。 有关安全中心威胁检测机制的详细信息，请阅读 [Azure Security Center detection capabilities](security-center-detection-capabilities.md)（Azure 安全中心检测功能）。

虽然本文不会协助用户创建自己的事件响应计划，但仍会在云的生命周期中使用 Microsoft Azure 安全响应作为事件响应阶段的基础。 下图显示了这些阶段：

![可疑活动](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> 若要构建自己的事件响应计划，用户可以使用国家标准和技术协会 (NIST) 提供的 [Computer Security Incident Handling Guide](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) （计算机安全事件处理指南）作为参考。
> 

可以在以下阶段使用安全中心警报：

* **检测**：确定一个或多个资源中的可疑活动。 
* **评估**：进行初始评估，了解可疑活动的详细信息。
* **诊断**：通过补救步骤采用技术过程解决问题。

每个安全警报所提供的信息都可以用来更好地了解攻击的性质，并提供可能的缓解措施建议。 某些警报还提供链接，单击这些链接即可获取更多信息或访问 Azure 中的其他信息源。 可使用提供的信息进一步研究并开始缓解操作，还可搜索存储于工作区中的安全相关数据。

下面的示例演示了正在发生的可疑的 RDP 活动：

![可疑活动](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

此页显示的详细信息包括攻击发生的时间、源主机名、目标 VM，并提供了建议步骤。 在某些情况下，攻击的源信息可能为空。 阅读 [Missing Source Information in Azure Security Center Alerts](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) （Azure 安全中心警报中缺少源信息），了解此类行为的详细信息。

也可从此页开始[调查](https://docs.microsoft.com/azure/security-center/security-center-investigation)，以便更好地了解攻击的时间线、攻击是如何发生的、哪些系统可能受损、使用了哪些凭据，并查看整个攻击链的图形表示方式。

确定受损系统以后，即可运行此前创建的安全[攻略](https://docs.microsoft.com/azure/security-center/security-center-playbooks)。 安全攻略是可以在安全中心执行的过程的集合，其执行前提是已从所选警报触发特定的攻略。

在 [How to Leverage the Azure Security Center & Microsoft Operations Management Suite for an Incident Response](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703)（如何利用 Azure 安全中心和 Microsoft Operations Management Suite 进行事件响应）视频中，用户可以看到一些演示，了解如何在每个这样的阶段发挥安全中心的作用。

> [!NOTE]
> 参阅[利用 Azure 安全中心进行事件响应](security-center-incident-response.md)，详细了解事件响应过程如何使用安全中心功能进行协助。 
> 
> 

## <a name="next-steps"></a>后续步骤
本文档介绍如何进行规划，为采用安全中心做准备。 若要了解有关安全中心的详细信息，请参阅以下文章：

* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Security health monitoring in Azure Security Center](security-center-monitoring.md) （Azure 安全中心的安全运行状况监视）- 了解如何监视 Azure 资源的运行状况。
* [Monitoring partner solutions with Azure Security Center](security-center-partner-solutions.md) （通过 Azure 安全中心监视合作伙伴解决方案）- 了解如何监视合作伙伴解决方案的运行状况。
* [Azure Security Center FAQ](security-center-faq.md) （Azure 安全中心常见问题）- 查找有关如何使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章。

