---
title: Azure 安全中心常见问题解答 - 数据收集和代理
description: 有关 Azure 安全中心数据收集、代理和工作区的常见问题，Azure 安全中心是一种有助于防止、检测和响应威胁的产品
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
ms.openlocfilehash: 0a368e54c940ff580b7e8f49dd108fafddde5b17
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91441477"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>常见问题解答 - 有关数据收集、代理和工作区的问题

安全中心从 Azure 虚拟机 (VM)、虚拟机规模集、IaaS 容器和非 Azure 计算机（包括本地计算机）收集数据，以监视安全漏洞和威胁。 数据是使用 Log Analytics 代理收集的，该代理从计算机中读取各种与安全相关的配置和事件日志，然后将数据复制到工作区进行分析。


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>安全中心创建的工作区中的 Azure Monitor 日志是否会产生费用？

不是。 Azure Monitor 日志配置为按节点计费时，安全中心创建的工作区中的 Azure Monitor 日志不会产生费用。 安全中心的计费始终依据工作区上安装的以下安全中心安全策略和解决方案：

- **Azure Defender off** –安全中心在默认工作区中启用 "SecurityCenterFree" 解决方案。 如果 Azure Defender 处于关闭状态，则不会向你收费。

- **Azure Defender on** –安全中心在默认工作区中启用 "安全" 解决方案。

有关详细信息，请参阅[安全中心 定价](https://azure.microsoft.com/pricing/details/security-center/)。

> [!NOTE]
> 安全中心创建的工作区的 Log Analytics 定价层不会影响安全中心计费。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-is-the-log-analytics-agent"></a>什么是 Log Analytics 代理？

为了监视安全漏洞和威胁，Azure 安全中心依赖于 [Log Analytics 代理](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent) -这是 Azure Monitor 服务所使用的同一代理。 

代理有时称为 Microsoft Monitoring Agent (或 "MMA" ) 。 

代理从连接的计算机收集各种与安全相关的配置详细信息和事件日志，然后将数据复制到 Log Analytics 工作区以便进一步分析。 此类数据的示例包括：操作系统类型和版本、操作系统日志（Windows 事件日志）、正在运行的进程、计算机名称、IP 地址和已登录的用户。

确保计算机运行的是代理支持的操作系统之一，如以下页面所述：

* [适用于 Windows 的 Log Analytics 代理支持的操作系统](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

* [适用于 Linux 的 Log Analytics 代理支持的操作系统](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

详细了解 [Log Analytics 代理收集的数据](security-center-enable-data-collection.md)。




## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>VM 需要满足哪些条件才能自动预配 Log Analytics 代理安装？

Windows 或 Linux IaaS VM 的合格条件如下：

- 当前未在该 VM 上安装 Log Analytics 代理扩展。
- 该 VM 处于正在运行状态。
- 安装了 Windows 或 Linux [Azure 虚拟机代理](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)。
- 该 VM 未用作应用程序，例如 Web 防火墙或下一代防火墙。


## <a name="where-is-the-default-log-analytics-workspace-created"></a>创建的默认 Log Analytics 工作区的位置是哪里？

默认工作区的位置取决于 Azure 区域：

- 对于美国和巴西的 VM，默认工作区的位置为美国
- 对于加拿大的 VM，工作区位置为加拿大
- 对于欧洲的 VM，工作区位置为欧洲
- 对于英国的 VM，工作区位置为英国
- 对于东亚和东南亚的 VM，工作区位置为亚洲
- 对于韩国的 VM，工作区位置为韩国
- 对于印度的 VM，工作区位置为印度
- 对于日本的 VM，工作区位置为日本
- 对于中国的 VM，工作区位置为中国
- 对于澳大利亚的 VM，工作区位置为澳大利亚


## <a name="what-data-is-collected-by-the-log-analytics-agent"></a>Log Analytics 代理收集哪些数据？

有关代理所监视的应用程序和服务的完整列表，请参阅 [什么是受 Azure Monitor？](https://docs.microsoft.com/azure/azure-monitor/monitor-reference#azure-services)。

> [!IMPORTANT]
> 请注意，对于某些服务（例如 Azure 防火墙），如果已启用日志记录，并选择了一个要记录 (的繁忙资源，则将日志设置为 " *详细* ") 你可能会看到 Log Analytics 的工作区存储需求的严重影响。 


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>是否可以删除安全中心创建的默认工作区？

建议不要删除默认工作区。 安全中心使用默认工作区存储 VM 的安全数据。 删除工作区后，安全中心无法收集此数据，因此无法提供某些安全建议和提示。

若要恢复，请删除连接到已删除工作区的 VM 上的 Log Analytics 代理。 安全中心会重新安装代理并创建新的默认工作区。

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

1. 选择“保存”。 系统会询问是否要重新配置受监视的 VM。

    - 如果只希望在新 VM 上应用新的工作区设置，请选择“否”。 新的工作区设置只会应用于新的代理安装；新发现的 VM 没有安装 Log Analytics 代理。
    - 如果希望在所有 VM 上应用新的工作区设置，请选择“是”。 此外，所有连接到安全中心创建的工作区的 VM 也都会重新连接到新的目标工作区。

    > [!NOTE]
    > 如果选择“是”，不得删除安全中心创建的任何工作区，除非所有 VM 都已重新连接到新的目标工作区。 如果过早删除工作区，此操作将会失败。

    - 若要取消该操作，请选择“取消”。

## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>如果 Log Analytics 代理已作为扩展安装到 VM 上，会怎样？<a name="mmaextensioninstalled"></a>

当监视代理作为扩展安装时，扩展配置仅允许向单个工作区进行报告。 安全中心不会覆盖用户工作区的现有连接。 如果已连接的工作区中安装了“Security”或“SecurityCenterFree”解决方案，则安全中心会将来自 VM 的安全性数据存储在该工作区中。 在此过程中，安全中心可以将扩展版本升级到最新版本。

有关详细信息，请参阅[对预先存在的代理安装进行自动预配](security-center-enable-data-collection.md#preexisting)。



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>如果 Log Analytics 代理直接安装在计算机上，而不是作为扩展（直接代理）安装，该怎么办？<a name="directagentinstalled"></a>

如果 Log Analytics 代理直接安装在 VM 上（而不是作为 Azure 扩展安装），则安全中心将安装 Log Analytics 代理扩展，可能还会将 Log Analytics 代理升级到最新版本。

安装的代理将继续向其已配置的工作区报告，此外，它还会向安全中心上配置的工作区报告（Windows 计算机支持多主页）。

如果配置的工作区是一个用户工作区（而不是安全中心的默认工作区），则需要在该工作区上安装“Security”/“SecurityCenterFree”解决方案，以便安全中心开始处理向该工作区报告的 VM 和计算机中的事件。

对于 Linux 计算机，尚不支持代理多主页，因此，如果检测到现有的代理安装，则不会进行自动预配，并且不会更改计算机的配置。

对于在 2019 年 3 月 17 日之前载入到安全中心的订阅上的现有计算机，检测到现有代理时，将不会安装 Log Analytics 代理扩展，并且计算机将不会受到影响。 对于这些计算机，请参阅“解决计算机上的监视代理运行状况问题”建议，以解决这些计算机上的代理安装问题

有关详细信息，请参阅下一部分[如果已在 VM 上安装 System Center Operations Manager 或 OMS 直接代理，会发生什么情况？](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>如果已在 VM 上安装 System Center Operations Manager 代理，会发生什么情况？<a name="scomomsinstalled"></a>

安全中心会将 Log Analytics 代理扩展并行安装到现有 System Center Operations Manager 代理。 现有代理将继续正常向 System Center Operations Manager 服务器报告。 请注意，Operations Manager 代理和 Log Analytics 代理共享公共运行时库，在此过程中这些库将更新为最新版本。 注意：如果安装了 2012 版的 Operations Manager 代理，请不要启用自动预配（如果 Operations Manager 服务器也是 2012 版本，可管理性功能可能会丢失）。


## <a name="what-is-the-impact-of-removing-these-extensions"></a>删除这些扩展会有什么影响？

删除 Microsoft 监视扩展后，安全中心无法从 VM 收集安全数据，因此无法提供某些安全建议和提示。 安全中心会在 24 小时内确定 VM 缺少扩展并重新安装扩展。


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>如何停止自动安装代理和创建工作区？

可以在安全策略中为订阅禁用自动预配，但不建议这样做。 禁用自动预配会限制安全中心的建议和警报。 禁用自动预配的具体步骤：

1. 如果订阅已启用 Azure Defender，请打开该订阅的安全策略，并选择 " **Azure defender**"。

    :::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="启用或禁用 Azure Defender":::

1. 接下来，在“安全策略 - 数据收集”页上选择“关”，禁用自动预配。
   ![数据收集][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>是否应选择禁用自动安装代理和创建工作区？

> [!NOTE]
> 如果选择禁用自动预配，请务必查看[选择禁用有何影响](#what-are-the-implications-of-opting-out-of-automatic-provisioning)和[选择禁用时的建议步骤](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)部分。

如果存在以下情况，可以选择禁用自动预配：

- 安全中心执行的自动代理安装应用到整个订阅。 无法将自动安装应用到一部分 VM。 如果某些重要 VM 上无法安装 Log Analytics 代理，则应选择禁用自动预配。
- 安装 Log Analytics 代理扩展会更新代理的版本。 这种方法适用于直接代理和 System Center Operations Manager 代理（在后一种代理中，Operations Manager 和 Log Analytics 代理共享公共运行时库，这些库在此过程中将更新）。 如果已安装的 Operations Manager 代理版本为 2012 并已将其升级，则当 Operations Manager 服务器的版本也是 2012 时，可管理性功能可能会丢失。 如果已安装的 Operations Manager 代理版本为 2012，则应考虑选择禁用自动预配。
- 如果自定义工作区在订阅的外部（集中式工作区），则应选择禁用自动预配。 可以手动安装 Log Analytics 代理扩展并将其连接到工作区，无需安全中心覆盖连接。
- 如果想要避免为每个订阅创建多个工作区，并且订阅中包含自己的自定义工作区，则可以采取以下两种做法：

   1. 可以选择禁用自动预配。 迁移后，根据[如何使用现有的 Log Analytics 工作区](#how-can-i-use-my-existing-log-analytics-workspace)中所述指定默认工作区设置。

   1. 也可以让迁移完成，在 VM 上安装 Log Analytics 代理，并将 VM 连接到所创建的工作区。 然后，在选择启用重新配置已安装代理的情况下，通过指定默认工作区设置来选择自己的自定义工作区。 有关详细信息，请参阅[如何使用现有的 Log Analytics 工作区？](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>选择禁用自动预配有何影响？

完成迁移后，安全中心无法从 VM 收集安全数据，因此无法提供某些安全建议和警报。 如果选择禁用自动预配，请手动安装 Log Analytics 代理。 请参阅[选择禁用时的建议步骤](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)。


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>选择禁用自动预配时的建议步骤是什么？

手动安装 Log Analytics 代理扩展，使安全中心能够从 VM 收集安全数据并提供建议和警报。 有关安装的指导，请参阅 [Windows VM 的代理安装](../virtual-machines/extensions/oms-windows.md)或 [Linux VM 的代理安装](../virtual-machines/extensions/oms-linux.md)。

可将代理连接到任何现有的自定义工作区或安全中心创建的工作区。 如果自定义工作区未启用“Security”或“SecurityCenterFree”解决方案，则需要应用解决方案。 若要应用解决方案，请选择自定义工作区或订阅，并通过“安全策略 – 定价层”页面应用定价层。

:::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="启用或禁用 Azure Defender":::

安全中心将会根据选定的定价层在工作区中启用正确的解决方案。


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>如何删除由安全中心安装的 OMS 扩展？<a name="remove-oms"></a>

可以手动删除 Log Analytics 代理。 不建议这样操作，因为这会限制安全中心的建议和提示。

> [!NOTE]
> 启用数据收集后，安全中心会删除代理，然后重新安装。  需要禁用数据收集，然后再手动删除代理。 请参阅“如何停止自动代理安装和工作区创建？”，查看有关禁用数据收集的说明。

手动删除代理：

1.    在门户中，打开“Log Analytics”。

1.    在 Log Analytics 页面上选择工作区：

1.    选择每个不需要监视的 VM 并选择“断开连接”。

   ![删除代理][3]

> [!NOTE]
> 如果 Linux VM 已有非扩展 OMS 代理，则删除该扩展也会删除代理，因此需要重新安装代理。


## <a name="how-do-i-disable-data-collection"></a>如何禁用数据收集？

强烈建议使用自动预配，以获取有关系统更新、OS 漏洞和终结点保护的安全警报和建议。 默认情况下，自动预配处于禁用状态。

如果已启用该功能，但现在想要将其禁用：

1. 在 [Azure 门户](https://portal.azure.com)中，打开 " **安全中心** " 并选择 " **安全策略**"。

1. 选择要对其禁用自动设置的订阅。

    **安全策略-** 将打开 "数据收集"。

1. 在“自动设置”下，选择“关闭” 。


## <a name="how-do-i-enable-data-collection"></a>如何启用数据收集？

可在安全中心策略中对 Azure 订阅启用数据收集。 启用数据收集。 [登录到 Azure 门户](https://portal.azure.com)，依次选择“浏览”、“安全中心”和“策略”  。 选择希望启用自动设置的订阅。 选择订阅时，“安全策略 - 数据收集”将打开。 在“自动设置”下，选择“打开” 。


## <a name="what-happens-when-data-collection-is-enabled"></a>启用数据收集之后会发生什么？

启用自动预配后，安全中心可在所有受支持的 Azure VM 以及任何新建的 Azure VM 中预配 Log Analytics 代理。 建议进行自动预配，但也可以手动完成代理安装。 [了解如何安装 Log Analytics 代理扩展](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)。 

该代理可启用进程创建事件 4688 和事件 4688 内的 *CommandLine* 字段。 VM 上创建的新进程由事件日志记录，并由安全中心检测服务监视。 若要详细了解针对每个新进程记录的详细信息，请参阅 [4688 中的说明字段](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields)。 该代理还收集 VM 上创建的 4688 事件，并将这些事件存储在搜索中。

该代理还为[自适应应用程序控件](security-center-adaptive-application.md)启用数据收集，安全中心会在审核模式下配置本地 AppLocker 策略以允许所有应用程序。 该策略将导致 AppLocker 生成事件，然后由安全中心收集和利用这些事件。 请务必注意，不会在已配置 AppLocker 策略的任何计算机上配置此策略。 

当安全中心检测到 VM 上的可疑活动时，如果已提供[安全联系信息](security-center-provide-security-contact-details.md)，将通过电子邮件通知客户。 警报也会显示在安全中心的安全警报仪表板中。


## <a name="will-security-center-work-using-an-oms-gateway"></a>安全中心是否可以使用 OMS 网关？

是的。 Azure 安全中心利用 Azure Monitor，使用 Log Analytics 代理从 Azure VM 和服务器收集数据。
若要收集数据，每个 VM 和服务器都必须连接到使用 HTTPS 的 Internet。 可直接、通过代理或 [OMS 网关](../azure-monitor/platform/gateway.md)完成此连接。


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Monitoring Agent 会影响服务器性能吗？

代理使用系统资源的名义帐户，对性能的影响极小。 有关性能影响和代理及扩展的详细信息，请参阅[规划和操作指南](security-center-planning-and-operations-guide.md#data-collection-and-storage)。




<!--Image references-->
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png
