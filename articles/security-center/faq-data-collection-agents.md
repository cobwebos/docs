---
title: Azure 安全中心常见问题 - 数据收集和代理
description: 有关 Azure 安全中心的数据收集、代理和工作区的常见问题，该产品可帮助您预防、检测和响应威胁
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
ms.openlocfilehash: 0dbad1a94479430426dae47df7ca3a3ecd9dc980
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436200"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>常见问题解答 - 有关数据收集、代理和工作区的问题

安全中心从 Azure 虚拟机 （VM）、虚拟机规模集、IaaS 容器和非 Azure 计算机（包括本地计算机）收集数据，以监视安全漏洞和威胁。 数据使用 Log Analytics 代理进行收集，该代理从计算机读取各种与安全相关的配置和事件日志，并将数据复制到工作区进行分析。


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>安全中心创建的工作区中的 Azure Monitor 日志是否会产生费用？

不是。 安全中心创建的工作区（虽然为每个节点计费为 Azure 监视器日志配置）不产生 Azure 监视器日志费用。 安全中心的计费始终依据工作区上安装的以下安全中心安全策略和解决方案：

- **免费层** – 安全中心在默认工作区中启用“SecurityCenterFree”解决方案。 免费层不会产生费用。

- **标准层** – 安全中心在默认工作区中启用“Security”解决方案。

有关详细信息，请参阅[安全中心 定价](https://azure.microsoft.com/pricing/details/security-center/)。

> [!NOTE]
> 安全中心创建的工作区的 Log Analytics 定价层不会影响安全中心计费。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>哪些 VM 适合自动预配日志分析代理安装？

Windows 或 Linux IaaS VM 的合格条件如下：

- 日志分析代理扩展当前未安装在 VM 上。
- 该 VM 处于正在运行状态。
- 安装了 Windows 或 Linux [Azure 虚拟机代理](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)。
- 该 VM 未用作应用程序，例如 Web 防火墙或下一代防火墙。


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>是否可以删除安全中心创建的默认工作区？

建议不要删除默认工作区****。 安全中心使用默认工作区存储 VM 的安全数据。 删除工作区后，安全中心无法收集此数据，因此无法提供某些安全建议和提示。

要恢复，请删除连接到已删除工作区的 VM 上的日志分析代理。 安全中心会重新安装代理并创建新的默认工作区。

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>我如何使用现有的 Log Analytics 工作区？

可以选择现有 Log Analytics 工作区，用于存储安全中心收集的数据。 使用现有 Log Analytics 工作区的先决条件：

- 工作区必须与选定 Azure 订阅相关联。
- 至少必须拥有读取权限，才能访问工作区。

选择现有 Log Analytics 工作区的具体步骤：

1. 在“安全策略 - 数据收集”**** 下，选择“使用另一个工作区”****。

    ![使用另一个工作区][4]

1. 从下拉菜单中，选择一个工作区，用于存储所收集的数据。

    > [!NOTE]
    > 下拉菜单中只显示用户有权访问且 Azure 订阅包含的工作区。

1. 选择“保存”。  系统将询问您是否要重新配置受监视的 VM。

    - 如果只希望在新 VM 上应用**** 新的工作区设置，请选择“否”****。 新的工作区设置仅适用于新的代理安装;新发现的 VM 未安装日志分析代理。
    - 如果要在所有**VM 上应用**新的工作区设置，请选择"**是**"。 此外，所有连接到安全中心创建的工作区的 VM 也都会重新连接到新的目标工作区。

    > [!NOTE]
    > 如果选择 **"是**"，则在所有 VM 都重新连接到新目标工作区之前，不要删除安全中心创建的任何工作区。 如果过早删除工作区，此操作将会失败。

    - 要取消操作，请选择 **"取消**"。

## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>如果日志分析代理已作为扩展安装在 VM 上，该怎么办？<a name="mmaextensioninstalled"></a>

将 Monitoring Agent 作为扩展安装时，扩展配置仅允许向单个工作区报告。 安全中心不会覆盖用户工作区的现有连接。 安全中心将来自 VM 的安全数据存储在已连接的工作区中，前提是已在该工作区中安装“Security”或“SecurityCenterFree”解决方案。 在此过程中，安全中心可将扩展版本升级到最新版本。

有关详细信息，请参阅[预先存在的代理安装情况下的自动预配](security-center-enable-data-collection.md#preexisting)。



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>如果日志分析代理直接安装在计算机上，而不是作为扩展（直接代理）安装，该怎么办？<a name="directagentinstalled"></a>

如果日志分析代理直接安装在 VM 上（不作为 Azure 扩展），安全中心将安装日志分析代理扩展，并可能将日志分析代理升级到最新版本。

已安装的代理将继续报告其已配置的工作区，此外，还会向安全中心配置的工作区报告（Windows 计算机上支持多宿主）。

如果配置的工作区是用户工作区（不是安全中心的默认工作区），则需要在它上安装"安全/安全中心"解决方案，以便安全中心从 VM 和报告到该工作区的计算机开始处理事件。

对于 Linux 计算机，尚未支持代理多宿主 - 因此，如果检测到现有代理安装，则不会发生自动预配，并且不会更改计算机的配置。

对于在 2019 年 3 月 17 日之前连接到安全中心订阅的现有计算机，当检测到现有代理时，将不会安装日志分析代理扩展，也不会影响计算机。 对于这些计算机，请参阅“解决计算机上的监视代理运行状况问题”中的建议来解决这些计算机上的代理安装问题

有关详细信息，请参阅下一部分[如果已在 VM 上安装 System Center Operations Manager 或 OMS 直接代理，会发生什么情况？](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>如果我的 VM 上已安装系统中心操作管理器代理，该怎么办？<a name="scomomsinstalled"></a>

安全中心将并行安装日志分析代理扩展到现有的系统中心操作管理器代理。 现有代理将继续正常向系统中心操作管理器服务器报告。 请注意，操作管理器代理和日志分析代理共享公共运行时库，这些库将在此过程中更新为最新版本。 注意 - 如果安装了操作管理器代理的版本 2012，请不要打开自动预配（当操作管理器服务器也是版本 2012 时，可管理性功能可能会丢失）。


## <a name="what-is-the-impact-of-removing-these-extensions"></a>删除这些扩展会有什么影响？

删除 Microsoft 监视扩展后，安全中心无法从 VM 收集安全数据，因此无法提供某些安全建议和提示。 安全中心会在 24 小时内确定 VM 缺少扩展并重新安装扩展。


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>如何停止自动安装代理和创建工作区？

可以在安全策略中为订阅禁用自动预配，但不建议这样做。 禁用自动预配会限制安全中心的建议和警报。 禁用自动预配的具体步骤：

1. 如果订阅配置为标准层，则打开该订阅的安全策略，并选择“免费”层****。

   ![定价层][1]

1. 接下来，通过在“安全策略 - 数据收集”**** 页上选择“关”****，禁用自动预配。
   ![数据收集][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>是否应选择禁用自动安装代理和创建工作区？

> [!NOTE]
> 如果选择禁用自动预配，请务必查看[选择禁用有何影响](#what-are-the-implications-of-opting-out-of-automatic-provisioning)和[选择禁用时的建议步骤](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)部分。

如果存在以下情况，可以选择禁用自动预配：

- 安全中心执行的自动代理安装应用到整个订阅。 无法将自动安装应用到一部分 VM。 如果日志分析代理无法安装关键 VM，则应选择退出自动预配。
- 日志分析代理扩展的安装将更新代理的版本。 这适用于直接代理和系统中心操作管理器代理（在后者中，操作管理器和日志分析代理共享公共运行时库 - 将在此过程中更新）。 如果已安装的操作管理器代理版本为 2012 版本并升级，则当操作管理器服务器也是版本 2012 时，可管理性功能可能会丢失。 如果已安装的操作管理器代理是版本 2012，请考虑选择退出自动预配。
- 如果订阅外部有自定义工作区（集中式工作区），则应选择退出自动预配。 您可以手动安装日志分析代理扩展，并在没有安全中心覆盖连接的情况下将其连接到工作区。
- 如果想要避免为每个订阅创建多个工作区，并且订阅中包含自己的自定义工作区，则可以采取以下两种做法：

   1. 可以选择禁用自动预配。 迁移后，根据[如何使用现有的 Log Analytics 工作区](#how-can-i-use-my-existing-log-analytics-workspace)中所述指定默认工作区设置。

   1. 或者，您可以允许完成迁移、在 VM 上安装日志分析代理以及连接到创建的工作区的 VM。 然后，在选择启用重新配置已安装代理的情况下，通过指定默认工作区设置来选择自己的自定义工作区。 有关详细信息，请参阅[如何使用现有的 Log Analytics 工作区？](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>选择禁用自动预配有何影响？

迁移完成后，安全中心将无法从 VM 收集安全数据，因此无法提供某些安全建议和提示。 如果选择退出，请手动安装日志分析代理。 请参阅[选择禁用时的建议步骤](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)。


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>选择禁用自动预配时的建议步骤是什么？

手动安装日志分析代理扩展，以便安全中心可以从 VM 收集安全数据并提供建议和警报。 有关安装的指导，请参阅 [Windows VM 的代理安装](../virtual-machines/extensions/oms-windows.md)或 [Linux VM 的代理安装](../virtual-machines/extensions/oms-linux.md)。

可将代理连接到任何现有的自定义工作区或安全中心创建的工作区。 如果自定义工作区未启用"安全"或"安全中心免费"解决方案，则需要应用解决方案。 要应用，请选择自定义工作区或订阅，并通过 **"安全策略和定价层**"页面应用定价层。

   ![定价层][1]

安全中心将会根据选定的定价层在工作区中启用正确的解决方案。


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>如何删除由安全中心安装的 OMS 扩展？<a name="remove-oms"></a>

您可以手动删除日志分析代理。 不建议这样操作，因为这会限制安全中心的建议和提示。

> [!NOTE]
> 启用数据收集后，安全中心会删除代理，然后重新安装。  需要禁用数据收集，然后再手动删除代理。 请参阅“如何停止自动代理安装和工作区创建？”，查看有关禁用数据收集的说明。

手动删除代理：

1.    在门户中，打开“Log Analytics”****。

1.    在“Log Analytics”页上，选择工作区：

1.    选择您不想监视的 VM，然后选择 **"断开连接**"。

   ![删除代理][3]

> [!NOTE]
> 如果 Linux VM 已具有非扩展 OMS 代理，则删除扩展程序也会删除代理，您必须重新安装它。


## <a name="how-do-i-disable-data-collection"></a>如何禁用数据收集？

默认情况下自动设置处于关闭状态。 可以随时禁用安全策略中的此设置，禁用资源的自动设置。 强烈建议使用自动预配，以获取有关系统更新、OS 漏洞和终结点保护的安全警报和建议。

若要禁用数据收集，请[登录到 Azure 门户](https://portal.azure.com)，依次选择“浏览”、“安全中心”和“策略”************。 选择希望禁用自动设置的订阅。 选择订阅时，“安全策略 - 数据收集”将打开****。 在“自动设置”下，选择“关闭”********。


## <a name="how-do-i-enable-data-collection"></a>如何启用数据收集？

可在安全中心策略中对 Azure 订阅启用数据收集。 启用数据收集。 [登录到 Azure 门户](https://portal.azure.com)，依次选择“浏览”、“安全中心”和“策略”************。 选择希望启用自动设置的订阅。 选择订阅时，“安全策略 - 数据收集”将打开****。 在“自动设置”下，选择“打开”********。


## <a name="what-happens-when-data-collection-is-enabled"></a>启用数据收集之后会发生什么？

启用自动预配后，安全中心会在所有支持的 Azure VM 和创建的任何新 VM 上预配日志分析代理。 建议自动预配，但也提供手动代理安装。 [了解如何安装日志分析代理扩展](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)。 

该代理可启用进程创建事件 4688 和事件 4688 内的 *CommandLine* 字段。 在 VM 上创建的新进程由 EventLog 记录，并由安全中心的检测服务进行监视。 有关每个新流程记录的详细信息，请参阅[4688 中的描述字段](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields)。 该代理还收集 VM 上创建的 4688 事件，并将这些事件存储在搜索中。

该代理还为[自适应应用程序控件](security-center-adaptive-application.md)启用数据收集，安全中心会在审核模式下配置本地 AppLocker 策略以允许所有应用程序。 此策略将导致 AppLocker 生成事件，然后由安全中心收集和利用这些事件。 请务必注意，不会在已配置 AppLocker 策略的任何计算机上配置此策略。 

当安全中心检测到 VM 上的可疑活动时，如果已提供[安全联系信息](security-center-provide-security-contact-details.md)，将通过电子邮件通知客户。 警报在安全中心的安全警报仪表板中也可见。


## <a name="will-security-center-work-using-an-oms-gateway"></a>安全中心是否会使用 OMS 网关工作？

是的。 Azure 安全中心利用 Azure 监视器使用日志分析代理从 Azure VM 和服务器收集数据。
要收集数据，每个 VM 和服务器都必须使用 HTTPS 连接到 Internet。 连接可以是直接的，使用代理，或通过[OMS 网关](../azure-monitor/platform/gateway.md)。


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Monitoring Agent 会影响服务器性能吗？

代理使用系统资源的名义帐户，对性能的影响极小。 有关性能影响和代理及扩展的详细信息，请参阅[规划和操作指南](security-center-planning-and-operations-guide.md#data-collection-and-storage)。


## <a name="where-is-my-data-stored"></a>我的数据存储在哪？

通过此代理收集的数据存储在与订阅关联的现有 Log Analytics 工作区或新工作区中。 有关详细信息，请参阅[数据安全](security-center-data-security.md)。


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png
