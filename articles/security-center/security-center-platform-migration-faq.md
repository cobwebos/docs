---
title: "安全中心平台迁移常见问题解答 | Microsoft Docs"
description: "此常见问题解答会回答有关 Azure 安全中心平台迁移的问题。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 4b88b5015fcf44e8979b8b1a3aa1eb26f0fbb704
ms.contentlocale: zh-cn
ms.lasthandoff: 09/15/2017

---
# <a name="security-center-platform-migration-faq"></a>安全中心平台迁移常见问题解答
2017 年 6 月初，Azure 安全中心开始使用 Microsoft Monitoring Agent 收集和存储数据。 若要了解详细信息，请参阅 [Azure 安全中心平台迁移](security-center-platform-migration.md)。 此常见问题解答会回答有关平台迁移的问题。

## <a name="data-collection-agents-and-workspaces"></a>数据收集、代理和工作区

### <a name="how-is-data-collected"></a>如何收集数据？
安全中心使用 Microsoft Monitoring Agent 从 VM 中收集安全数据。 安全数据包括安全配置（用于识别漏洞）和安全事件（用于检测威胁）的相关信息。 代理收集的数据存储在连接到 VM 的现有 Log Analytics 工作区或安全中心创建的新工作区中。 安全中心创建新的工作区时，需要考虑 VM 的地理位置。

> [!NOTE]
> Microsoft Monitoring Agent 正是 Operations Management Suite (OMS)、Log Analytics 服务和 System Center Operations Manager (SCOM) 使用的代理。
>
>

首次启用数据收集时，或迁移订阅时，安全中心会检查 Microsoft Monitoring Agent 是否已作为 Azure 扩展安装在每个 VM 上。 如果未安装 Microsoft Monitoring Agent，则安全中心会：

- 在 VM 上安装 Microsoft Monitoring Agent
   - 如果安全中心创建的工作区所在的地理位置与 VM 相同，则代理会连接到此工作区
   - 如果工作区不存在，则安全中心会在该地理位置创建新的资源组和默认工作区，并将代理连接到该工作区。 工作区和资源组的命名约定是：

       工作区：DefaultWorkspace-[subscription-ID]-[geo]

       资源组：DefaultResouceGroup-[geo]
- 在工作区中安装安全中心解决方案

工作区的位置根据 VM 的位置确定。 若要了解详细信息，请参阅[数据安全](security-center-data-security.md)。

> [!NOTE]
> 在迁移平台前，安全中心使用 Azure Monitoring Agent 从 VM 收集安全数据，这些数据会存储在存储帐户中。 迁移平台后，安全中心使用 Microsoft Monitoring Agent 和工作区收集并存储相同的数据。 迁移后可以删除存储帐户。
>
>

### <a name="am-i-billed-for-log-analytics-or-oms-on-the-workspaces-created-by-security-center"></a>安全中心创建的工作区中的 Log Analytics 或 OMS 是否会产生费用？
不会。 OMS 配置为按节点计费时，安全中心创建的工作区不会产生 OMS 费用。 安全中心的计费始终依据工作区上安装的以下安全中心安全策略和解决方案：

- 免费层 – 安全中心在默认工作区中安装“SecurityCenterFree”解决方案。 免费层不会产生费用。
- 标准层 – 安全中心在默认工作区中安装“SecurityCenterFree”和“Security”解决方案。

有关详细信息，请参阅[安全中心 定价](https://azure.microsoft.com/pricing/details/security-center/)。 定价页说明了 2017 年 6 月起安全数据存储和按比例计费的更改。

> [!NOTE]
> 安全中心创建的工作区的 OMS 定价层不会影响安全中心计费。
>
>

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>是否可以删除安全中心创建的默认工作区？
建议不要删除默认工作区。 安全中心使用默认工作区存储 VM 的安全数据。  删除工作区后，安全中心无法收集此数据，因此无法提供某些安全建议和提示

若要恢复，请删除连接到已删除工作区的 VM 上的 Microsoft Monitoring Agent。 安全中心会重新安装代理并创建新的默认工作区。

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>我如何使用现有的 Log Analytics 工作区？

可以选择现有 Log Analytics 工作区，用于存储安全中心收集的数据。 使用现有 Log Analytics 工作区的先决条件：

- 工作区必须与选定 Azure 订阅相关联。
- 至少必须拥有读取权限，才能访问工作区。

选择现有 Log Analytics 工作区的具体步骤：

1. 在“安全策略 - 数据收集”下，选择“使用另一个工作区”。

   ![使用另一个工作区][5]

2. 从下拉菜单中，选择一个工作区，用于存储所收集的数据。

   > [!NOTE]
   > 下拉菜单中只显示用户有权访问且 Azure 订阅包含的工作区。
   >
   >

3. 选择“保存”。
4. 选择“保存”后，系统就会询问是否要重新配置受监视的 VM。

   - 如果只希望在新 VM 上应用新的工作区设置，请选择“否”。 新的工作区设置只会应用于新的代理安装；新发现的 VM 没有安装 Microsoft Monitoring Agent。
   - 如果希望在所有 VM 上应用新的工作区设置，请选择“是”。 此外，所有连接到安全中心创建的工作区的 VM 也都会重新连接到新的目标工作区。

   > [!NOTE]
   > 如果选择“是”，不得删除安全中心创建的工作区，除非所有 VM 已重新连接到新的目标工作区。 如果过早删除工作区，此操作将会失败。
   >
   >

   - 选择“取消”，以取消此操作。

      ![重新配置受监视的 VM][6]

### <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>如果 Microsoft Monitoring Agent 已作为扩展安装到 VM 上，会怎样？
安全中心不会覆盖用户工作区的现有连接。 安全中心会在已连接的工作区中存储 VM 的安全数据。

### <a name="what-if-i-had-a-microsoft-monitoring-agent-installed-on-the-machine-but-not-as-an-extension"></a>如果已在计算机上安装 Microsoft Monitoring Agent，但不是作为扩展安装，会怎样？
如果 Microsoft Monitoring Agent 是直接安装到 VM 上（而不是作为 Azure 扩展进行安装），那么安全中心不会安装 Microsoft Monitoring Agent，并且安全监视也会受限。

### <a name="what-is-the-impact-of-removing-these-extensions"></a>删除这些扩展会有什么影响？
删除 Microsoft 监视扩展后，安全中心无法从 VM 收集安全数据，因此无法提供某些安全建议和提示。 安全中心会在 24 小时内确定 VM 缺少扩展并重新安装扩展。

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>如何停止自动安装代理和创建工作区？
可以在安全策略中为订阅禁用自动预配，但不建议这样做。 禁用自动预配会限制安全中心的建议和警报。 标准定价层的订阅必须启用自动预配。 禁用自动预配的具体步骤：

1. 如果订阅配置为标准层，则打开该订阅的安全策略，并选择“免费”层。

   ![定价层][1]

2. 接下来，在“安全策略 - 数据收集”边栏选项卡上选择“关”，禁用自动预配。
   ![数据收集][2]

### <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>如何删除由安全中心安装的 OMS 扩展？
可以手动删除 Microsoft Monitoring Agent。 不建议这样操作，因为这会限制安全中心的建议和提示。

> [!NOTE]
> 启用数据收集后，安全中心会删除代理，然后重新安装。  需要禁用数据收集，然后再手动删除代理。 有关禁用数据收集的说明，请参阅[如何停止自动安装代理和创建工作区？](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation?)。
>
>

手动删除代理：

1.  在门户中，打开“Log Analytics”。
2.  在“Log Analytics”边栏选项卡中选择一个工作区：
3.  选择每个不需要监视的 VM 并选择“断开连接”。

   ![删除代理][3]

> [!NOTE]
> 如果 Linux VM 已有非扩展 OMS 代理，则删除该扩展也会删除代理，因此客户需要重新安装。
>
>

## <a name="existing-oms-customers"></a>现有 OMS 客户

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>安全中心是否会覆盖 VM 和工作区之间的任何现有连接？
如果 VM 已将 Microsoft Monitoring Agent 作为 Azure 扩展进行安装，则安全中心不会覆盖现有工作区连接。 相反，安全中心会使用现有工作区。

如果没有安全中心解决方案，则会在工作区安装，并且此解决方案仅适用于相关的 VM。 添加解决方案时，默认情况下会自动将它部署到连接到 Log Analytics 工作区的所有 Windows 和 Linux 代理。 借助 [解决方案目标](../operations-management-suite/operations-management-suite-solution-targeting.md)这一 OMS 功能，可以限定解决方案的范围。

如果 Microsoft Monitoring Agent 是直接安装到 VM 上（而不是作为 Azure 扩展进行安装），那么安全中心不会安装 Microsoft Monitoring Agent，并且安全监视也会受限。

### <a name="what-should-i-do-if-i-suspect-that-the-data-platform-migration-broke-the-connection-between-one-of-my-vms-and-my-workspace"></a>如果怀疑数据平台迁移中断了其中一个 VM 与工作区之间的连接，该怎么办？
应该不会发生这种情况。 如果确实发生了，则[创建一个 Azure 支持请求](../azure-supportability/how-to-create-azure-support-request.md)并在其中包括以下详细信息：

- 受影响 VM 的 Azure 资源 ID
- 连接中断前，在扩展上配置的工作区的 Azure 资源 ID
- 之前已安装的代理和版本

### <a name="does-security-center-install-solutions-on-my-existing-oms-workspaces-what-are-the-billing-implications"></a>安全中心是否在现有 OMS 工作区上安装解决方案？ 计费会产生什么影响？
安全中心确定 VM 已连接到所创建的工作区时，会根据定价层启用此工作区上的解决方案。 由于[解决方案目标](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting)，解决方案仅应用于相关的 Azure VM，因此计费保持不变。

- 免费层 – 安全中心在工作区中安装“SecurityCenterFree”解决方案。 免费层不会产生费用。
- 标准层 – 安全中心在工作区中安装“SecurityCenterFree”和“Security”解决方案。

   ![默认工作区上的解决方案][4]

> [!NOTE]
> Log Analytics 中的“安全性”解决方案是 OMS 中的“安全性与审核”解决方案。
>
>

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>环境中已存在工作区，是否可以将其用于收集安全数据？
如果 VM 已将 Microsoft Monitoring Agent 作为 Azure 扩展进行安装，则安全中心会使用现有连接的工作区。 如果没有安全中心解决方案，则会在工作区安装，并且由于[解决方案目标](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting)此解决方案仅适用于相关的 VM。

安全中心在 VM 上安装 Microsoft Monitoring Agent 时，会使用安全中心创建的默认工作区。 客户很快便可配置要使用的工作区。

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>工作区已存在安全解决方案。 计费会产生什么影响？
“安全性与审核”解决方案用于启用 Azure VM 的安全中心标准层功能。 如果已在工作区上安装“安全性与审核”解决方案，则安全中心会使用现有解决方案。 计费方面没有任何更改。

## <a name="next-steps"></a>后续步骤
若要了解有关安全中心平台迁移的详细信息，请参阅

- [Azure 安全中心平台迁移](security-center-platform-migration.md)
- [Azure 安全中心故障排除指南](security-center-troubleshooting-guide.md)

<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
[6]: ./media/security-center-platform-migration-faq/reconfigure-monitored-vm.png

