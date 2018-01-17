---
title: "Azure 安全中心故障排除指南 | Microsoft Docs"
description: "本文档可以帮助排除 Azure 安全中心中的问题。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 44462de6-2cc5-4672-b1d3-dbb4749a28cd
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.author: yurid
ms.openlocfilehash: e471f04a86cde73bbdb333826a5e0d25684a4547
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2018
---
# <a name="azure-security-center-troubleshooting-guide"></a>Azure 安全中心故障排除指南
本指南适用于信息技术 (IT) 专业人员、信息安全分析人员，以及那些组织中正在使用 Azure 安全中心并需要进行排除安全中心相关问题的云管理员。

>[!NOTE]
>自 2017 年 6 月初开始，安全中心使用 Microsoft Monitoring Agent 来收集和存储数据。 请参阅 [Azure 安全中心平台迁移](security-center-platform-migration.md)，了解详细信息。 本文中的信息表示转换到 Microsoft Monitoring Agent 后的安全中心功能。
>

## <a name="troubleshooting-guide"></a>故障排除指南
本指南介绍如何排除安全中心的相关问题。 安全中心完成的大部分故障排除首先通过查看失败组件的[审核日志](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/)记录展开。 通过审核日志，可以确定：

* 哪些操作已发生
* 谁启动了该操作
* 操作何时发生
* 操作的状态
* 其他可能有助于研究操作的属性值

审核日志包含针对资源执行的所有写入操作（PUT、POST、DELETE），但它不包含读取属性 (GET)。

## <a name="microsoft-monitoring-agent"></a>Microsoft Monitoring Agent
安全中心使用 Microsoft Monitoring Agent（Operations Management Suite 和 Log Analytics 服务同样适用此代理）从 Azure 虚拟机中收集安全数据。 启用数据收集并在目标计算机中正确安装代理后，应执行以下进程：

* HealthService.exe

如果打开服务管理控制台 (services.msc)，还会看到 Microsoft Monitoring Agent 服务正在运行，如下所示：

![服务](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

若要查看拥有的代理版本，请打开“任务管理器”，在“进程”选项卡中找到“Microsoft Monitoring Agent 服务”，右键单击此选项，然后单击“属性”。 在“详细信息”选项卡中，查看文件版本，如下所示：

![文件](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)


## <a name="microsoft-monitoring-agent-installation-scenarios"></a>Microsoft Monitoring Agent 安装方案
在计算机上安装 Microsoft Monitoring Agent 时，有两种可产生不同结果的安装方案。 支持的方案有：

* 安全中心自动安装代理：在此方案中，可在“安全中心”和“日志搜索”这两个位置查看警报。 系统将向资源所属订阅的安全策略中配置的电子邮件地址，发送电子邮件通知。
。
* 在位于 Azure 中的 VM 上手动安装代理：在此方案中，如果使用 2017 年 2 月前手动下载和安装的代理，则仅当对工作区所属订阅进行筛选时，才可在安全中心门户中查看警报。 如果对资源所属订阅进行筛选，则无法看到任何警报。 系统将向工作区所属订阅的安全策略中配置的电子邮件地址，发送电子邮件通知。

>[!NOTE]
> 若要避免第二个方案中所述的行为，请确保下载最新版本的代理。
>

## <a name="monitoring-agent-health-issues"></a>监视代理运行状况问题
“监视状态”定义安全中心无法成功监视那些已针对自动预配初始化的 VM 和计算机的原因。 下表显示了“监视状态”的值、说明和解决步骤。

| 监视状态 | 说明 | 解决步骤 |
|---|---|---|
| 代理待安装 | Microsoft Monitoring Agent 安装仍在运行。  安装可能需要长达数小时的时间。 | 等待自动安装完成。 |
| 电源状态为关闭 | VM 已停止。  Microsoft Monitoring Agent 只能安装在正在运行的 VM 上。 | 重启 VM。 |
| Azure VM 代理缺失或无效 | Microsoft Monitoring Agent 尚未安装。  需要提供有效的 Azure VM 代理才能通过安全中心安装此扩展。 | 在 VM 上安装、重新安装或升级 Azure VM 代理。 |
| VM 状态表明尚未做好安装准备  | Microsoft Monitoring Agent 尚未安装，因为 VM 尚未做好安装准备。 VM 尚未做好安装准备是因为 VM 代理或 VM 预配出现问题。 | 检查 VM 的状态。 返回到门户中的“虚拟机”，选择要获取状态信息的 VM。 |
|安装失败 - 常规错误 | Microsoft Monitoring Agent 已安装，但因错误而失败。 | [手动安装扩展](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)，或者卸载扩展，让安全中心尝试再次安装。 |
| 安装失败 - 已安装本地代理 | Microsoft Monitoring Agent 安装失败。 安全中心标识了一个已在 VM 上安装的本地代理（OMS 或 SCOM）。 为了避免多宿主配置，在 VM 向两个不同的工作区报告的情况下，会停止 Microsoft Monitoring Agent 安装。 | 有两种解决方法：[手动安装扩展](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)并将其连接到所需工作区。 或者，将所需工作区设置为默认工作区，并启用自动预配代理的功能。  请参阅[启用自动预配](security-center-enable-data-collection.md)。 |
| 代理无法连接到工作区 | Microsoft Monitoring Agent 已安装，但因网络连接原因而失败。  检查是否可以进行 Internet 访问，或者是否已为代理配置有效的 HTTP 代理。 | 请参阅[监视代理网络要求](#troubleshooting-monitoring-agent-network-requirements)。 |
| 代理连接到缺失或未知的工作区 | 安全中心确定，安装在 VM 上的 Microsoft Monitoring Agent 连接到的是其没有访问权限的工作区。 | 两种情况可能会发生这样的错误。 第一种情况是工作区已删除，不再存在。 请使用正确的工作区重新安装代理，或者卸载代理，让安全中心完成其自动预配安装。 第二种情况是工作区属于某个订阅的一部分，而安全中心没有该订阅的访问权限。 安全中心要求提供允许 Microsoft 安全资源提供程序访问的订阅。 若要启用此功能，请将订阅注册到 Microsoft 安全资源提供程序。 为此，可以使用 API、PowerShell、门户，或者直接在安全中心的“概览”仪表板中对订阅进行筛选。 有关详细信息，请参阅[资源提供程序和类型](../azure-resource-manager/resource-manager-supported-services.md#portal)。 |
| 代理无响应或缺少 ID | 安全中心无法从 VM 检索扫描的安全数据，即使代理已安装。 | 代理未报告包括检测信号在内的任何数据。 代理可能已损坏，或者有不明因素在阻止流量。 或者，代理是在报告数据但却缺少 Azure 资源 ID，因此不可能将数据与 Azure VM 匹配。 |
| 未安装代理 | 数据收集已禁用。 | 在安全策略中启用数据收集，或者手动安装 Microsoft Monitoring Agent。 |


## <a name="troubleshooting-monitoring-agent-network-requirements"></a>监视代理网络要求故障排除
若要让代理连接和注册到安全中心，必须允许其访问包括端口号和域 URL 在内的网络资源。

- 对于代理服务器，需确保在代理设置中配置适当的代理服务器资源。 阅读本文，详细了解[如何更改代理设置](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents#configure-proxy-settings)。
- 对于限制 Internet 访问的防火墙，需要将其配置为允许访问 OMS。 代理设置中不需要任何操作。

下表显示通信所需的资源。

| 代理资源 | 端口 | 绕过 HTTPS 检查 |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | 是 |
| *.oms.opinsights.azure.com | 443 | 是 |
| *.blob.core.windows.net | 443 | 是 |
| *.azure-automation.net | 443 | 是 |

如果遇到代理载入问题，请务必阅读[如何排查 Operations Management Suite 载入问题](https://support.microsoft.com/en-us/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues)一文。


## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>有关终结点保护无法正常工作的疑难解答

来宾代理是 [Microsoft 反恶意软件](../security/azure-security-antimalware.md)扩展进行的所有操作的父进程。 当来宾代理进程故障时，作为来宾代理子进程运行的 Microsoft 反恶意软件也可能发生故障。  这类情况下，建议验证以下选项：

- 目标 VM 是否是自定义映像，VM 的创建者是否从未安装过来宾代理。
- 如果目标是 Linux VM 而非 Windows VM，则在 Linux VM 上安装 Windows 版反恶意软件扩展会失败。 Linux 来宾代理对操作系统版本和所需程序包有特定要求，如不满足这些要求，则 VM 代理也无法正常工作。
- 是否使用旧版来宾代理创建了 VM。 如果是，请注意某些旧版代理不会自动更新到较新版本，这可能导致出现此问题。 创建自己的映像时，请始终使用最新版本的来宾代理。
- 某些第三方管理软件可能会禁用来宾代理，或阻止对某些文件位置的访问。 如果 VM 上安装有第三方管理软件，请确保代理在排除列表中。
- 某些防火墙设置或网络安全组 (NSG) 可能会阻止发送至来宾代理的流量和来宾代理发送出的流量。
- 某些访问控制列表 (ACL) 可能会阻止磁盘访问。
- 磁盘空间不足可能会阻止来宾代理正常工作。

默认情况下禁用 Microsoft 反恶意软件用户界面，请参阅[部署后在 Azure 资源管理器 VM 上启用 Microsoft 反恶意软件用户界面](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/09/enabling-microsoft-antimalware-user-interface-post-deployment/)，深入了解如何在需要时启用该用户界面。

## <a name="troubleshooting-problems-loading-the-dashboard"></a>排查加载仪表板的问题

如果在加载“安全中心”仪表板时遇到问题，请确保将订阅注册到安全中心的用户（即第一个通过订阅打开安全中心的用户）以及需要启用数据收集功能的用户为订阅的*所有者* 或*参与者*。 从该时刻开始，在订阅上为*读者* 的 also user 即可查看 dashboard/alerts/recommendation/policy。

## <a name="contacting-microsoft-support"></a>请联系 Microsoft 支持人员
可以使用本文中提供的指南来识别一些问题，还可以在安全中心公共 [论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter)中查找记录的其他问题。 但是，如果需要进一步故障排除，则可使用 Azure 门户打开新的支持请求，如下所示：

![Microsoft 支持部门](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)


## <a name="see-also"></a>另请参阅
在本文档中，已经学习了如何在 Azure 安全中心中配置安全策略。 若要了解更多有关 Azure 安全中心的详细信息，请参阅以下内容：

* [Azure 安全中心规划和操作指南](security-center-planning-and-operations-guide.md) - 了解如何规划和了解设计注意事项，以适应 Azure 安全中心。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) - 了解如何管理和响应安全警报
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) - 了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题解答](security-center-faq.md) - 查找有关使用服务的常见问题
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章
