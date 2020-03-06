---
title: Azure 安全中心常见问题解答-数据收集和代理
description: 有关 Azure 安全中心的数据收集、代理和工作区的常见问题，一种有助于防止、检测和响应威胁的产品
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 8317a13b9ef87679836f55627268deefa4500dce
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372873"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>常见问题解答-有关数据收集、代理和工作区的问题

安全中心从 Azure 虚拟机（Vm）、虚拟机规模集、IaaS 容器和非 Azure 计算机（包括本地计算机）收集数据以监视安全漏洞和威胁。 数据是使用 Microsoft Monitoring Agent 收集的，它从计算机中读取各种安全相关的配置和事件日志，然后将数据复制到工作区以进行分析。


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>在安全中心创建的工作区上，是否对 Azure Monitor 日志计费？

No。 安全中心创建的工作区，配置为按节点计费 Azure Monitor 日志，不会产生 Azure Monitor 日志收费。 安全中心的计费始终依据工作区上安装的以下安全中心安全策略和解决方案：

- **免费层** – 安全中心在默认工作区中启用“SecurityCenterFree”解决方案。 免费层不会计费。

- **标准层** – 安全中心在默认工作区中启用“Security”解决方案。

有关详细信息，请参阅[安全中心 定价](https://azure.microsoft.com/pricing/details/security-center/)。

> [!NOTE]
> 安全中心创建的工作区的 log analytics 定价层不会影响安全中心计费。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>VM 需要满足哪些条件才能在其上自动预配 Microsoft Monitoring Agent 安装？

Windows 或 Linux IaaS VM 的合格条件如下：

- 当前未在该 VM 上安装 Microsoft Monitoring Agent 扩展。
- 该 VM 处于正在运行状态。
- 已安装 Windows 或 Linux [Azure 虚拟机代理](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)。
- 该 VM 未用作应用程序，例如 Web 防火墙或下一代防火墙。


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>是否可以删除安全中心创建的默认工作区？

建议不要删除默认工作区。 安全中心使用默认工作区存储 VM 的安全数据。 删除工作区后，安全中心无法收集此数据，因此无法提供某些安全建议和提示。

若要恢复，请删除连接到已删除工作区的 VM 上的 Microsoft Monitoring Agent。 安全中心会重新安装代理并创建新的默认工作区。

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>我如何使用现有的 Log Analytics 工作区？

可以选择现有 Log Analytics 工作区，用于存储安全中心收集的数据。 使用现有 Log Analytics 工作区的先决条件：

- 工作区必须与选定 Azure 订阅相关联。
- 至少必须拥有读取权限，才能访问工作区。

选择现有 Log Analytics 工作区的具体步骤：

1. 在“安全策略 - 数据收集”下，选择“使用另一个工作区”。

    ![使用另一个工作区][4]

1. 从下拉菜单中，选择一个工作区，用于存储所收集的数据。

    > [!NOTE]
    > 下拉菜单中只显示用户有权访问且 Azure 订阅包含的工作区。

1. 选择“保存”。 系统会询问你是否要重新配置受监视的 Vm。

    - 如果只希望在新 VM 上应用新的工作区设置，请选择“否”。 新的工作区设置只会应用于新的代理安装；新发现的 VM 没有安装 Microsoft Monitoring Agent。
    - 如果希望在所有 VM 上应用新的工作区设置，请选择“是”。 此外，所有连接到安全中心创建的工作区的 VM 也都会重新连接到新的目标工作区。

    > [!NOTE]
    > 如果选择 **"是"** ，请不要删除安全中心创建的任何工作区，直到所有 vm 均已重新连接到新的目标工作区。 如果过早删除工作区，此操作将会失败。

    - 若要取消操作，请选择 "**取消**"。

## 如果 Microsoft Monitoring Agent 已作为扩展安装到 VM 上，该怎么办？<a name="mmaextensioninstalled"></a>

当监视代理安装为扩展时，扩展配置仅允许向单个工作区进行报告。 安全中心不会覆盖用户工作区的现有连接。 安全中心将从已连接的工作区中的 VM 存储安全数据，前提是已在该工作区中安装了 "安全" 或 "SecurityCenterFree" 解决方案。 在此过程中，安全中心可以将扩展版本升级到最新版本。

有关详细信息，请参阅[在预先存在的代理安装的情况下自动预配](security-center-enable-data-collection.md#preexisting)。



## 如果 Microsoft Monitoring Agent 直接安装在计算机上，而不是作为扩展（直接代理），该怎么办？<a name="directagentinstalled"></a>

如果 Microsoft Monitoring Agent 直接安装在 VM （而不是 Azure 扩展）上，安全中心将安装 Microsoft Monitoring Agent 扩展，并可能将 Microsoft Monitoring Agent 升级到最新版本。

安装的代理将继续向其已配置的工作区报告，此外，还会向安全中心（在 Windows 计算机上支持多主页）上配置的工作区报告。

如果配置的工作区是一个用户工作区（而不是安全中心的默认工作区），则需要在该工作区上安装 "Security/" SecurityCenterFree "解决方案，以便安全中心开始处理向该工作区报告的 Vm 和计算机的事件。

对于 Linux 计算机，尚不支持代理多宿主，因此，如果检测到现有的代理安装，则不会发生自动预配，并且不会更改计算机的配置。

对于在 17 2019 年3月之前载入到 Security Center 的订阅中的现有计算机，将不会安装 Microsoft Monitoring Agent 扩展，并且计算机将不会受到影响。 对于这些计算机，请参阅 "解决计算机上的监视代理运行状况问题" 建议，以解决这些计算机上的代理安装问题

有关详细信息，请参阅下一节[如果已在 VM 上安装了 System Center Operations Manager 或 OMS 直接代理，会发生什么情况？](#scomomsinstalled)

## 如果 System Center Operations Manager 代理已安装在我的 VM 上，该怎么办？<a name="scomomsinstalled"></a>

安全中心会将 Microsoft Monitoring Agent 扩展并排安装到现有 System Center Operations Manager 代理。 现有代理将继续正常向 System Center Operations Manager 服务器报告。 请注意，Operations Manager 代理和 Microsoft Monitoring Agent 共享公共运行库，在此过程中将更新为最新版本。 注意-如果安装了 Operations Manager 代理2012版，请不要启用自动预配（当 Operations Manager 服务器也是版本2012时，可管理性功能可能会丢失）。


## <a name="what-is-the-impact-of-removing-these-extensions"></a>删除这些扩展会有什么影响？

删除 Microsoft 监视扩展后，安全中心无法从 VM 收集安全数据，因此无法提供某些安全建议和提示。 安全中心会在 24 小时内确定 VM 缺少扩展并重新安装扩展。


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>如何停止自动安装代理和创建工作区？

可以在安全策略中为订阅禁用自动预配，但不建议这样做。 禁用自动预配会限制安全中心的建议和警报。 禁用自动预配的具体步骤：

1. 如果订阅配置为标准层，则打开该订阅的安全策略，并选择“免费”层。

   ![定价层][1]

1. 接下来，通过选择 "**安全策略-数据收集**" 页上的 "**关闭**" 来关闭自动设置。
   ![数据收集][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>是否应选择禁用自动安装代理和创建工作区？

> [!NOTE]
> 如果选择禁用自动预配，请务必查看[选择禁用有何影响](#what-are-the-implications-of-opting-out-of-automatic-provisioning)和[选择禁用时的建议步骤](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)部分。

如果存在以下情况，可以选择禁用自动预配：

- 安全中心执行的自动代理安装应用到整个订阅。 无法将自动安装应用到一部分 VM。 如果使用 Microsoft Monitoring Agent 无法安装某些关键 VM，则应选择禁用自动预配。
- 安装 Microsoft Monitoring Agent （MMA）扩展将更新代理的版本。 这适用于直接代理和 System Center Operations Manager 代理（在后一种情况下，Operations Manager 和 MMA 共享公共运行时库-将在此过程中更新）。 如果安装的 Operations Manager 代理版本为2012，并且升级，则当 Operations Manager 服务器也是版本2012时，可管理性功能可能会丢失。 如果安装的 Operations Manager 代理是版本2012，请考虑选择退出自动预配。
- 如果订阅具有外部自定义工作区（集中工作区），则应选择不使用自动设置。 可以手动安装 Microsoft Monitoring Agent 扩展并将其连接到工作区，无需安全中心覆盖连接。
- 如果想要避免为每个订阅创建多个工作区，并且订阅中包含自己的自定义工作区，则可以采取以下两种做法：

   1. 可以选择禁用自动预配。 迁移后，根据[如何使用现有的 Log Analytics 工作区](#how-can-i-use-my-existing-log-analytics-workspace)中所述指定默认工作区设置。

   1. 或者，可以让迁移完成，在 VM 上安装 Microsoft Monitoring Agent，并将 VM 连接到所创建的工作区。 然后，在选择启用重新配置已安装代理的情况下，通过指定默认工作区设置来选择自己的自定义工作区。 有关详细信息，请参阅[如何使用现有的 Log Analytics 工作区？](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>选择禁用自动预配有何影响？

迁移完成后，安全中心无法从 VM 收集安全数据，并且无法使用某些安全建议和警报。 如果选择退出，请手动安装 Microsoft Monitoring Agent。 请参阅[选择禁用时的建议步骤](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)。


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>选择禁用自动预配时的建议步骤是什么？

手动安装 Microsoft Monitoring Agent 扩展，以便安全中心可以从 Vm 收集安全数据，并提供建议和警报。 有关安装的指导，请参阅 [Windows VM 的代理安装](../virtual-machines/extensions/oms-windows.md)或 [Linux VM 的代理安装](../virtual-machines/extensions/oms-linux.md)。

可将代理连接到任何现有的自定义工作区或安全中心创建的工作区。 如果自定义工作区未启用“Security”或“SecurityCenterFree”解决方案，则需要应用某个解决方案。 若要应用，请选择自定义工作区或订阅，并通过 "**安全策略-定价层**" 页应用定价层。

   ![定价层][1]

安全中心将会根据选定的定价层在工作区中启用正确的解决方案。


## 如何删除由安全中心安装的 OMS 扩展？<a name="remove-oms"></a>

可以手动删除 Microsoft Monitoring Agent。 不建议这样操作，因为这会限制安全中心的建议和提示。

> [!NOTE]
> 启用数据收集后，安全中心会删除代理，然后重新安装。  需要禁用数据收集，然后再手动删除代理。 请参阅“如何停止自动代理安装和工作区创建？”，查看有关禁用数据收集的说明。

手动删除代理：

1.  在门户中，打开“Log Analytics”。

1.  在 "Log Analytics" 页上，选择一个工作区：

1.  选择不想监视的 Vm，并选择 "**断开连接**"。

   ![删除代理][3]

> [!NOTE]
> 如果 Linux VM 已有非扩展 OMS 代理，则删除该扩展也会删除该代理，你必须重新安装它。


## <a name="how-do-i-disable-data-collection"></a>如何禁用数据收集？

默认情况下自动设置处于关闭状态。 可以随时禁用安全策略中的此设置，禁用资源的自动设置。 强烈建议使用自动预配来获取有关系统更新、操作系统漏洞和 endpoint protection 的安全警报和建议。

若要禁用数据收集，请[登录到 Azure 门户](https://portal.azure.com)，依次选择“浏览”、“安全中心”和“策略”。 选择希望禁用自动设置的订阅。 选择订阅时，“安全策略 - 数据收集”将打开。 在“自动设置”下，选择“关闭”。


## <a name="how-do-i-enable-data-collection"></a>如何启用数据收集？

可在安全中心策略中对 Azure 订阅启用数据收集。 启用数据收集。 [登录到 Azure 门户](https://portal.azure.com)，依次选择“浏览”、“安全中心”和“策略”。 选择希望启用自动设置的订阅。 选择订阅时，“安全策略 - 数据收集”将打开。 在“自动设置”下，选择“打开”。


## <a name="what-happens-when-data-collection-is-enabled"></a>启用数据收集之后会发生什么？

启动自动设置后，安全中心可在所有受支持的 Azure VM 以及任何新建的 Azure VM 中预配 Microsoft Monitoring Agent。 建议使用自动设置，但也可以使用手动代理安装。 [了解如何安装 Microsoft Monitoring Agent 扩展](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)。 

该代理可启用进程创建事件 4688 和事件 4688 内的 *CommandLine* 字段。 VM 上创建的新进程由事件日志记录，并由安全中心检测服务监视。 有关为每个新进程记录的详细信息的详细信息，请参阅[4688 中的说明字段](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields)。 该代理还收集 VM 上创建的 4688 事件，并将这些事件存储在搜索中。

该代理还为[自适应应用程序控件](security-center-adaptive-application.md)启用数据收集，安全中心会在审核模式下配置本地 AppLocker 策略以允许所有应用程序。 此策略将导致 AppLocker 生成事件，并由安全中心收集和使用。 请务必注意，不会在已配置 AppLocker 策略的任何计算机上配置此策略。 

当安全中心检测到 VM 上的可疑活动时，如果已提供[安全联系信息](security-center-provide-security-contact-details.md)，将通过电子邮件通知客户。 警报也会显示在安全中心的安全警报仪表板中。


## <a name="will-security-center-work-using-an-oms-gateway"></a>安全中心将使用 OMS 网关吗？

可以。 Azure 安全中心利用 Microsoft Monitoring Agent 从 Azure Vm 和服务器 Azure Monitor 收集数据。
若要收集数据，每个 VM 和服务器都必须使用 HTTPS 连接到 Internet。 可以直接连接，也可以使用代理，也可以通过[OMS 网关](../azure-monitor/platform/gateway.md)连接。


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Monitoring Agent 会影响服务器性能吗？

代理使用系统资源的名义帐户，对性能的影响极小。 有关性能影响和代理及扩展的详细信息，请参阅[规划和操作指南](security-center-planning-and-operations-guide.md#data-collection-and-storage)。


## <a name="where-is-my-data-stored"></a>我的数据存储在哪？

通过此代理收集的数据存储在与订阅关联的现有 Log Analytics 工作区或新工作区中。 有关详细信息，请参阅[数据安全](security-center-data-security.md)。


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png