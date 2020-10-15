---
title: 指南和最佳做法
description: 了解将云和本地工作负载备份到云的最佳实践和指南
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 522f7d2502a49b912f34f392c52e5046eba8d01f
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92092301"
---
# <a name="backup-cloud-and-on-premises-workloads-to-cloud"></a>将云和本地工作负载备份到云

## <a name="introduction"></a>简介

Azure 备份通过一种简单、安全且经济高效的解决方案来全面保护 Azure 中的数据资产，该解决方案需要零基础结构。 它 Azure's 内置的数据保护解决方案，适用于各种工作负荷。 它可帮助保护在云中运行的任务关键型工作负荷，并确保你的备份始终可用，并可跨整个备份空间进行大规模管理。

### <a name="intended-audience"></a>目标受众

本文的主要目标受众是 IT 和应用程序管理员，以及大型和中型组织的实施者，他们想要了解 Azure 内置数据保护技术、Azure 备份的功能，以及如何高效地实施可更好地保护你的部署的解决方案。 本文假设你熟悉核心 Azure 技术、数据保护概念，并具有使用备份解决方案的经验。 本文中介绍的指南可让你更轻松地在 Azure 上使用已建立的模式设计备份解决方案，并避免出现问题。

### <a name="how-this-article-is-organized"></a>如何组织本文

尽管可以轻松地开始在 Azure 上保护基础结构和应用程序，但当你确保正确设置基础 Azure 资源并以最佳方式使用时，你可以加速实现价值。 本文简要概述了用于以最佳方式配置 Azure 备份部署的设计注意事项和指南。 它将检查核心组件 (例如，恢复服务保管库、备份策略) 和概念 (例如，调控) ，以及如何将这些组件及其功能与详细的产品文档链接结合起来。

## <a name="architecture"></a>体系结构

![Azure 备份体系结构](./media/guidance-best-practices/azure-backup-architecture.png)

### <a name="workloads"></a>工作负载

Azure 备份为 (本地和云) 的各种工作负荷启用数据保护。 它是 Azure 中安全可靠的内置数据保护机制。 它可以跨多个工作负载无缝缩放其保护，而无需任何管理开销。 还可以通过 PowerShell、CLI、Azure 资源管理器模板和 REST Api 启用此 (，同时提供多个自动化通道。 ) 

* **可缩放、持久且安全的存储-** Azure 备份通过内置的安全性和高可用性功能使用可靠的 Blob 存储。 可以选择 LRS、GRS 或 GRS 存储来查看备份数据。  

* **本机工作负荷集成-** Azure 备份在 Azure Vm 中 (Vm、SAP HANA、SQL 甚至 Azure 文件) 提供与 Azure 工作负荷的本机集成，而无需管理自动化或基础结构来部署代理、编写新脚本或预配存储。

### <a name="data-plane"></a>数据平面

* **自动存储管理** -Azure 备份会自动预配和管理备份数据的存储帐户，以确保在备份数据增长时可以扩展存储帐户。

* **恶意删除保护–** 防止任何意外和恶意尝试通过软删除备份删除备份。 已删除的备份数据将免费存储14天，并允许从该状态中恢复。

* **安全加密的备份-** Azure 备份确保以安全的方式存储你的备份数据，并利用 azure RBAC 和加密等 Azure 平台的内置安全功能。

* **备份数据生命周期管理-** Azure 备份会自动清理较旧的备份数据，以符合保留策略。 你还可以将数据从操作存储层分层存储到保管库存储中。

### <a name="management-plane"></a>管理平面

* **访问控制** -保管库 (恢复服务和备份保管库) 提供管理功能，可通过 Azure 门户、备份中心、保管库仪表板、SDK、CLI 甚至 REST api 进行访问。 它还是 Azure RBAC 边界，提供将访问权限限制为仅授权备份管理员的选项。

* "**策略管理**" –每个保管库中的 Azure 备份策略定义应该何时触发备份，以及需要保留多长时间。 你还可以管理这些策略，并将它们应用于多个项。

* **监视和报告** -Azure 备份与 Log Analytics 集成，还提供通过工作簿查看报表的功能。

* **快照管理** -azure 备份 (Vm 和 azure 文件) 为某些 azure 本机工作负荷拍摄快照，管理这些快照并允许从这些快照快速还原。 此选项可大大减少将数据恢复到原始存储的时间。

## <a name="vault-considerations"></a>保管库注意事项

Azure 备份使用保管库 (恢复服务和备份保管库) 来安排和管理备份。 它还使用保管库来存储备份的数据。 有效的保管库设计可帮助组织建立结构，以便在 Azure 中组织和管理备份资产，以支持业务优先级。 创建保管库时，请考虑以下准则：  

### <a name="align-to-subscription-design-strategy"></a>与订阅设计策略一致

由于保管库的作用域是订阅，因此适合你的保管库设计，以满足订阅设计策略（例如 *应用程序类别策略* ），其中的订阅基于特定的应用程序或服务或应用程序原型的各行进行分隔。 有关详细信息，请参阅[此文](/azure/cloud-adoption-framework/decision-guides/subscriptions/)。

### <a name="single-or-multiple-vault"></a>单个或多个保管库

可以使用一个或多个保管库来组织和管理备份。 遵循以下指南：

* 如果工作负载全部由单个订阅和单个资源管理，则可以使用单个保管库来监视和管理备份空间。

* 如果你的工作负荷分布在多个订阅中，则可以创建多个保管库，每个订阅一个或多个。
  * 备份中心允许使用单个窗格来管理与备份相关的所有任务。 [在此处了解详细信息]()。
  * 可以通过工作簿模板自定义视图。 备份资源管理器是 Azure Vm 的一个模板。 [在此处了解详细信息](monitor-azure-backup-with-backup-explorer.md)。
  * 如果你需要跨保管库的一致策略，则可以使用 Azure 策略来跨多个保管库传播备份策略。 可以编写一个自定义的 [Azure 策略定义](../governance/policy/concepts/definition-structure.md) ，该定义使用 ["deployifnotexists"](../governance/policy/concepts/effects.md#deployifnotexists) 效果跨多个保管库传播备份策略。 分配可将此 Azure 策略定义 [分配](../governance/policy/assign-policy-portal.md) 给特定范围 (订阅或 RG) ，以便将 "备份策略" 资源部署到 Azure 策略分配范围内的所有恢复服务保管库。 备份策略 (的设置，例如备份频率、保留期等) 应由用户指定为 Azure 策略分配中的参数。

* 随着组织的占用量的增长，你可能想要跨订阅移动工作负荷，原因如下：按备份策略、合并保管库、在较低冗余上权衡 (从 GRS 迁移到 LRS) 。  Azure 备份支持在 Azure 订阅之间移动恢复服务保管库，或将其移动到同一订阅中的其他资源组。 [在此处了解详细信息](backup-azure-move-recovery-services-vault.md)。

### <a name="review-default-settings"></a>查看默认设置

在保管库中配置备份之前，查看存储复制类型和安全设置的默认设置以满足你的要求。

* 默认情况下，*存储复制类型*设置为异地冗余 (GRS) 。 配置备份后，将禁用修改选项。 请按照 [以下](backup-create-rs-vault.md#set-storage-redundancy) 步骤查看并修改设置。

* 默认情况下，在新创建的保管库上启用*软删除*，以防止意外或恶意删除备份数据。 请按照 [以下](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) 步骤查看并修改设置。

* *跨区域还原* 允许你还原次要区域中的 azure vm，这是一个 Azure 配对区域。 使用此选项，可以执行深化来满足审核或符合性要求，以及在主要区域发生灾难时还原 VM 或其磁盘。 CRR 是适用于任何 GRS 保管库的选择加入功能。 [在此处了解详细信息](backup-create-rs-vault.md#set-cross-region-restore)。

* 在完成保管库设计之前，请查看 [保管库支持矩阵](backup-support-matrix.md#vault-support) ，了解可能会影响或限制你的设计选择的因素。

## <a name="backup-policy-considerations"></a>备份策略注意事项

Azure 备份策略有两个组件： *计划* (何时进行备份) 和 *保留* (保留备份) 的时间。 可以基于要备份的数据的类型、RTO/RPO 要求、运营或法规符合性需求以及工作负荷类型 (例如，VM、数据库、文件) 来定义策略。 [在此处了解详细信息](backup-architecture.md#backup-policy-essentials)。

创建备份策略时，请考虑以下准则：

### <a name="schedule-considerations"></a>计划注意事项

* 考虑将需要相同计划开始时间、频率和保留设置的 Vm 分组到单个策略中。

* 确保备份计划开始时间为非高峰生产应用程序时间。

* 若要分发备份流量，请考虑在一天的不同时间备份不同的 Vm，并确保该时间不重叠。

### <a name="retention-considerations"></a>保留注意事项

* 短期保留可以是 "分钟" 或 "每天"。 "每周"、"每月" 或 "每年" 备份点的保留期称为长期保留。

* 长期保留：
  * 计划的 (符合性要求) -如果事先知道数据是当前时间所必需的年份，请使用长期保留。
  * 计划外的 (按需需求) -如果事先不知道，请使用特定自定义保留设置的按需， (这些自定义保留设置不受策略设置) 影响。

* 使用自定义保留的按需备份-如果需要执行不是通过备份策略计划的备份，则可以使用按需备份。 这对于执行不适合你计划的备份的备份或执行细化备份 (例如，每天有多个 IaaS VM 备份，因为计划的备份每日只允许一个备份) 。 请务必注意，在计划策略中定义的保留策略不适用于按需备份。

### <a name="optimize-backup-policy"></a>优化备份策略

* 随着业务需求的变化，你可能需要扩展或缩短保留期。 执行此操作时，可能会出现以下情况：  
  * 如果延长保留期，则会根据新策略对现有的恢复点进行标记和保留。
  * 如果保留期减少，恢复点将在下一清理作业中标记为要删除，并随后被删除。
  * 最新的保留规则适用于所有保留点， (不包括按需保留点) 。 因此，如果保留期已延长 (例如100天) ，则在进行备份时，如果保留的 (保留期为100天到7天) ，则将根据最后指定的保留期保留所有备份数据 (，即7天) 。

* Azure 备份可让你灵活地 *停止保护和管理备份*：
  * *停止保护并保留备份数据*。 如果要停用或解除授权数据源 (VM、应用程序) ，但需要保留数据以进行审核或符合性，则可以使用此选项停止所有将来的备份作业来保护数据源，并保留已备份的恢复点。 然后，你可以还原或恢复 VM 保护。
  * *停止保护并删除备份数据*。 此选项将使所有将来的备份作业停止保护你的 VM 并删除所有恢复点。 你将无法还原 VM，也无法使用“恢复备份”选项。

  * 如果恢复已停止并保留数据) 的数据源的保护 (，则将应用保留规则。 将在计划时间)  (删除过期的恢复点。

* 在完成策略设计之前，请务必注意可能会影响你的设计选择的下列因素。
  * 备份策略的作用域限定为保管库。
  * 每个策略的项数有限制 (例如，100 Vm) 。 若要缩放，可以创建具有相同或不同计划的重复策略。
  * 不能有选择地删除特定的恢复点。
  * 你无法完全禁用计划的备份，并使数据源处于受保护状态。 可使用策略配置的最不频繁的备份是每周计划备份一次。 一种替代方法是使用 "保留数据" 停止保护，并在每次执行备份时启用保护，按需备份，然后关闭保护，但保留备份数据。 [在此处了解详细信息](backup-azure-manage-vms.md#stop-protecting-a-vm)。

## <a name="security-considerations"></a>安全注意事项

为了帮助你保护你的备份数据并满足你的业务的安全需求，Azure 备份提供了保密性、完整性和可用性保证，防范蓄意攻击和滥用宝贵的数据和系统。 对于 Azure 备份解决方案，请考虑以下安全准则：

### <a name="authentication-and-authorization"></a>身份验证和授权

* Azure RBAC) 的基于角色的访问控制 (可实现精细的访问管理，将职责划分到团队中，并仅授予执行作业所需的对用户的访问权限。 [在此处了解详细信息](backup-rbac-rs-vault.md)。

* Azure 备份提供了三种内置角色来控制备份管理操作：备份参与者、操作员和读者。 [在此处了解详细信息](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions)。

* Azure 备份有多个内置于服务中的安全控件，用于防止、检测和响应安全漏洞 (详细了解) 

* 恢复服务保管库使用的存储帐户是独立的，用户不能出于任何恶意目的对其进行访问。 仅允许通过 Azure 备份管理操作（例如还原）进行访问。

### <a name="encryption-of-data-in-transit-and-at-rest"></a>传输中数据和静态数据的加密

加密可以保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。

* 在 Azure 中，Azure 存储与保管库之间传输的数据受 HTTPS 保护。 此数据将保留在 Azure 网络中。

* 使用 Microsoft 托管的密钥自动加密备份数据。 或者，你可以使用自己的密钥，也称为 [客户托管密钥](encryption-at-rest-with-cmk.md)。

* Azure 备份支持备份和还原已使用 Azure 磁盘加密 (ADE) 功能加密了其 OS/数据磁盘的 Azure VM。 [在此处了解详细信息](backup-azure-vms-encryption.md)。

### <a name="protection-of-backup-data-from-unintentional-deletes"></a>防止意外删除备份数据

Azure 备份提供安全功能来帮助保护备份数据，即使是删除了备份数据，也能予以恢复。 使用软删除时，如果用户删除 VM 的备份 (，SQL Server 数据库、Azure 文件共享、SAP HANA 数据库) 备份数据保留14天，这使得恢复该备份项目不会丢失数据。 "软删除" 状态中的备份数据的额外14天的保留期不会产生任何费用。 [在此处了解详细信息](backup-azure-security-feature-cloud.md)。

### <a name="monitoring-and-alerts-of-suspicious-activity"></a>可疑活动的监视和警报

Azure 备份提供内置监视和警报功能，用于查看和配置 Azure 备份相关事件的操作。 [在此处了解详细信息](security-overview.md#monitoring-and-alerts-of-suspicious-activity)。

### <a name="security-features-to-help-protect-hybrid-backups"></a>可帮助保护混合备份的安全功能

Azure 备份服务使用 Microsoft Azure 恢复服务 (MARS) 代理将本地计算机中的文件、文件夹和卷/系统状态备份和还原到 Azure。 MARS 现在提供了安全功能：在从 Azure 备份下载之后，在上传和解密之前要加密的密码，已删除的备份数据将保留14天，从删除日期开始，并 (ex 的关键操作。 更改密码) 只能由拥有有效 Azure 凭据的用户执行。 [在此处了解详细信息](backup-azure-security-feature.md)。

## <a name="network-considerations"></a>网络注意事项

Azure 备份需要将数据从工作负荷移动到恢复服务保管库。 Azure 备份提供多种功能，以防止无意中公开 (（例如，网络) 上的中间人攻击）。 遵循以下指南：

### <a name="internet-connectivity"></a>Internet 连接

* *AZURE VM 备份* -存储和 azure 备份服务之间的所有必需通信和数据传输在 Azure 网络中发生，无需访问虚拟网络。 因此，将 Azure Vm 备份到受保护的网络中不需要你允许访问任何 Ip 或 Fqdn。

* Azure *vm 上的 SAP HANA 数据库，SQL Server AZURE vm 上的数据库*-需要连接到 azure 备份服务、azure 存储和 Azure Active Directory。 这可以通过使用专用终结点，或允许访问所需的公共 IP 地址或 FQDN 来实现。 如果不允许正确连接到所需的 Azure 服务，则可能会导致诸如数据库发现、配置备份、执行备份和还原数据等操作失败。 有关使用 NSG 标记、Azure 防火墙和 HTTP 代理时的完整网络指导，请参阅这些 [SQL](backup-sql-server-database-azure-vms.md#establish-network-connectivity) 和 [SAP HANA](./backup-azure-sap-hana-database.md#establish-network-connectivity) 文章。

* *混合* -Microsoft Azure 恢复服务) 代理的 MARS (需要对所有关键操作（安装、配置、备份和还原）进行网络访问。 MARS 代理可以通过 [Azure ExpressRoute](install-mars-agent.md#use-azure-expressroute) 连接到 azure 备份服务，方法是使用公共对等互连 (适用于旧线路) 和 Microsoft 对等互连，使用 [专用终结点](install-mars-agent.md#private-endpoints) 或通过 [适当的访问控制通过代理/防火墙](install-mars-agent.md#verify-internet-access)。

### <a name="private-endpoints-for-azure-backup"></a>Azure 备份的专用终结点

Azure [专用终结点](../private-link/private-endpoint-overview.md) 是一个网络接口，该接口将你私下并安全地连接到由 Azure 专用链接提供支持的服务。 通过 Azure 备份，你可以使用专用终结点从恢复服务保管库安全地备份和还原数据。

* 启用保管库的专用终结点时，这些终结点仅用于在 Azure VM 和 MARS 代理备份中备份和还原 SQL 和 SAP HANA 工作负荷。  还可以使用保管库来备份其他工作负荷， (它们不需要) 的私有终结点。 除了使用 MARS 代理备份 SQL 和 SAP HANA 工作负荷和备份之外，专用终结点还用于在 Azure VM 备份时执行文件恢复。 [在此处了解详细信息](private-endpoints.md#recommended-and-supported-scenarios)。

* Azure Active Directory 当前不支持专用终结点。 因此，在对 Azure Vm 中的数据库执行备份和使用 MARS 代理进行备份时，需要使用 Azure Active Directory 所需的 Ip 和 Fqdn。 如果适用，还可以使用 NSG 标记和 Azure 防火墙标记来允许访问 Azure AD。 在此处了解有关 [先决条件](./private-endpoints.md#before-you-start)的详细信息。

## <a name="governance-considerations"></a>治理注意事项

Azure 中的管理主要是通过 [Azure 策略](../governance/policy/overview.md) 和 [azure 成本管理](../cost-management-billing/cost-management-billing-overview.md)实现的。 [Azure Policy ](../governance/policy/overview.md)允许你创建、分配和管理策略定义，以强制执行资源规则。 此功能可使这些资源符合企业标准。 [Azure 成本管理](../cost-management-billing/cost-management-billing-overview.md)可用于跟踪 Azure 资源和其他云提供商的云使用情况和开支。 此外，下列工具（例如 [Azure 价格计算器](https://azure.microsoft.com/pricing/calculator/) 和 [azure 顾问](../advisor/advisor-overview.md)  ）在成本管理过程中扮演着重要的角色。

### <a name="azure-backup-support-two-key-scenarios-via-built-in-azure-policy"></a>Azure 备份通过内置 Azure 策略支持两个关键方案

* 确保自动备份新创建的业务关键计算机，并提供适当的保留设置。 Azure 备份提供了一个内置策略（使用 Azure Policy），可以将其分配给“订阅或资源组中指定位置的所有 Azure VM”。 将此策略分配到给定范围时，该范围中创建的所有新 VM 都将自动配置为备份到位于“同一位置和订阅中的现有保管库”。 用户可以指定备份的 VM 应关联的保管库和保留策略。 [在此处了解详细信息](backup-azure-auto-enable-backup.md)。

* 确保新创建的保管库已启用诊断功能以支持报表。 通常，为每个保管库手动添加诊断设置可能是一件繁重的任务。 而且，创建的任何新保管库都需要启用诊断设置，以便可以查看此保管库的报表。 为了简化使用 Log Analytics 作为目标) 进行大规模 (的诊断设置创建，Azure 备份提供了一个内置的 Azure 策略。 此策略将一个 LA 诊断设置添加到每个订阅或资源组中的所有保管库中。 以下部分介绍了如何使用此策略。 [在此处了解详细信息](azure-policy-configure-diagnostics.md)。

### <a name="azure-backup-cost-considerations"></a>Azure 备份成本注意事项

Azure 备份服务的功能可让你灵活地管理你的成本，并仍能满足你的 BCDR (业务连续性和灾难恢复) 业务要求。 遵循以下指南：

* 使用定价计算器可以通过调整各种拉杆来评估和优化成本。 [在此处了解详细信息](azure-backup-pricing.md)

* 备份资源管理器：使用备份资源管理器或备份报表来了解和优化备份存储的增长，停止非关键工作负荷或已删除 Vm 的备份。 你可以从备份的角度获取整个场地的汇总视图。 这不仅包括有关备份项目的信息，还包括未为备份配置的资源。 这可确保永远不会丢失保护发展的空间中的关键数据，并针对非关键工作负荷或已删除的工作负荷优化备份。

* 优化备份策略
  * 基于工作负荷原型优化计划和保留设置 (例如，关键任务、非关键) 
  * 优化即时还原的保持期设置
  * 选择适当的备份类型来满足要求，同时采用受支持的备份类型 (按 Azure 备份中的工作负荷) 完整、增量、日志、差异。

* 有选择地备份磁盘：排除磁盘 (预览功能) 提供一种高效且经济高效的选择来选择备份关键数据。 例如，如果你不想备份附加到 VM 的其他磁盘，请只备份一个磁盘。 如果有多个备份解决方案，这也很有用。 例如，当你使用工作负荷备份解决方案备份数据库或数据时 (SQL Server Azure VM 备份中的数据库) ，并且你希望对所选磁盘使用 Azure VM 级别备份。

* Azure 备份使用 Azure Vm 的快照，并将它们与磁盘存储在一起，以提升恢复点的创建速度并加速还原操作。 这称为 "即时还原"。 默认情况下，即时还原快照保留两天。 此功能通过减少还原时间，允许从这些快照还原操作。 它减少了从保管库转换和复制数据所需的时间。 因此，可以查看 7 天内创建的快照的相应存储成本。 [在此处了解详细信息](backup-instant-restore-capability.md#configure-snapshot-retention)。

* 默认情况下，Azure 备份保管库的存储复制类型设置为异地冗余 (GRS) 。 保护项后无法更改此选项。 异地冗余存储 (GRS) 提供比本地冗余存储 (LRS) 更高的数据持久性，允许选择使用跨区域还原并增加成本。 在较低的成本和更高的数据持续性之间检查利弊，并确定最适合你的方案。 [在此处了解详细信息](backup-create-rs-vault.md#set-storage-redundancy)

* 如果要保护 VM 和 VM 自身内运行的工作负荷，请查看是否需要这种双重保护。

## <a name="monitoring-and-alerting-considerations"></a>监视和警报注意事项

作为备份用户或管理员，你应该能够监视所有备份解决方案并获得重要方案的通知。 本部分详细介绍 Azure 备份服务提供的监视和通知功能。

### <a name="monitoring"></a>监视

* Azure 备份为操作（例如配置备份、备份、还原、删除备份等）提供 **内置的作业监视** 。 这适用于保管库，最适合用于监视单个保管库。 [在此处了解详细信息](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault)。

* 如果需要大规模监视操作活动， **备份资源管理器** 提供了整个备份空间的聚合视图，并支持详细的向下钻取分析和故障排除。 它是一个内置的 Azure Monitor 工作簿，提供单个中心位置，可帮助你监视 Azure 上整个备份空间、跨租户、位置、订阅、资源组和保管库的操作活动。 [在此处了解详细信息](monitor-azure-backup-with-backup-explorer.md)。
  * 使用它来确定未配置为进行备份的资源，并确保不会丢失在成长空间内的关键数据的保护。
  * 该仪表板提供过去7天的操作活动 (最大) 。 如果需要保留此数据，则可以导出为 Excel 文件并保留这些数据。
  * 如果你是 Azure Lighthouse 用户，则可以跨多个租户查看信息，启用无边界监视。

* 如果需要保留并查看长期的操作活动，请使用 **报表**。 备份管理员的一个常见要求是根据长时间内的数据获取有关备份的见解。 此类解决方案的用例包括：
  * 分配和预测需使用的云存储空间。
  * 审核备份和还原。
  * 确定不同粒度级别的关键趋势。

* 此外，
  * 你可以将数据 (例如，作业、策略等) 发送到 **Log Analytics** 工作区。 这将启用 Azure Monitor 日志的功能，使数据与 Azure Monitor 收集的其他监视数据进行关联，将多个 Azure 订阅和租户中的日志条目合并到一个位置以便一起分析，使用日志查询执行复杂的分析，并深入了解日志条目。 [在此处了解详细信息](../azure-monitor/platform/activity-log.md#send-to-log-analytics-workspace)。
  * 可以向事件中心发送数据，以在 Azure 外部发送条目，例如，发送到第三方 SIEM (安全信息和事件管理) 或其他 log analytics 解决方案。 [在此处了解详细信息](../azure-monitor/platform/activity-log.md#send-to-azure-event-hubs)。
  * 如果要将日志数据保留超过90天，则可以将数据发送到 Azure 存储帐户，以进行审核、静态分析或备份。 如果只需将事件保留90天或更短时间，则无需设置存储帐户的存档，因为活动日志事件保留在 Azure 平台90天内。 [了解详细信息](../azure-monitor/platform/activity-log.md#send-to--azure-storage)。

### <a name="alerting"></a>警报

* 警报主要是一种获得相关操作相关通知的方法。 "备份警报" 部分显示 Azure 备份服务生成的警报。

* Azure 备份通过电子邮件为故障、警告和关键操作提供 **内置的警报** 通知机制。 你可以指定要在生成警报时通知的单个电子邮件地址或通讯组列表。 还可以选择是要接收每个警报的通知，还是将这些警报分组成按小时摘要，然后接收通知。
  * 这些警报由服务定义，并提供对有限方案的支持-备份/还原失败、停止保护并保留数据/停止保护并删除数据等等。 [在此处了解详细信息](backup-azure-monitoring-built-in-monitor.md#alert-scenarios)。
  * 如果执行了破坏性操作（如 "停止保护并删除数据"），则会发出警报，并将电子邮件发送给订阅所有者、管理员和共同管理员，即使 **未** 为恢复服务保管库配置通知。
  * 某些工作负荷可能会产生较高的故障频率 (例如，每15分钟 SQL Server) 。 若要防止在出现每个失败事件时引发的警报淹没，会合并警报。 [在此处了解详细信息](backup-azure-monitoring-built-in-monitor.md#consolidated-alerts)。
  * 生成的警报不能自定义，并且仅限于 Azure 门户中定义的电子邮件。

* 如果需要 **创建自定义警报** (例如，成功作业的警报) 然后使用 Log Analytics。 在 Azure Monitor 中，可以在 Log Analytics 工作区内创建你自己的警报。  (DPM/MABS) 的混合工作负载还可以将数据发送到 LA，并使用 LA 在 Azure 备份支持的工作负荷之间提供常见警报。

* 还可以通过内置的恢复服务保管库 **活动日志**获取通知。 但是，它支持有限的方案并且不适用于计划备份之类的操作，这些操作与活动日志相比，与资源日志更好。 若要了解有关这些限制的详细信息，以及如何使用 Log Analytics 的工作区对受 Azure 备份保护的所有工作负荷进行大规模监视和警报，请参阅此 [文](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-to-monitor-at-scale)。

## <a name="next-steps"></a>后续步骤

建议你阅读以下文章作为使用 Azure 备份的起点：

* [Azure 备份概述](backup-overview.md)
* [常见问题解答](backup-azure-backup-faq.md)
