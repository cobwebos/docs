---
title: 在 Azure 安全中心保护计算机和应用程序 | Microsoft Docs
description: 本文档介绍 Azure 安全中心内提供的，可帮助保护虚拟机和计算机以及 Web 应用和应用服务环境的建议。
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/28/2018
ms.author: rkarlin
ms.openlocfilehash: 7cf5f86d9a2d121ff54c40e27c6bc50847a4dfdf
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2018
ms.locfileid: "43132487"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>在 Azure 安全中心保护计算机和应用程序
Azure 安全中心可分析 Azure 资源的安全状态。 在安全中心识别潜在的安全漏洞时，它会创建一些建议，这些建议会指导完成配置所需控件的过程。 建议适用于以下 Azure 资源类型：虚拟机 (VM) 和计算机、应用程序、网络、SQL，以及“标识和访问”。

本文介绍适用于计算机和应用程序的建议。

## <a name="monitoring-security-health"></a>监视安全运行状况
可以在“安全中心 - 概述”仪表板上监视资源的安全状态。 “资源”部分提供识别到的问题数，以及每种资源类型的安全状态。

可以选择“建议”来查看所有问题的列表。 有关如何应用建议的详细信息，请参阅[在 Azure 安全中心实施安全建议](security-center-recommendations.md)。

有关计算和应用服务建议的完整列表，请参阅[建议](security-center-virtual-machine-recommendations.md)。

若要继续，请在“资源”或安全中心主菜单中选择“计算和应用”。
![安全中心仪表板][1]

## <a name="monitor-compute-and-app-services"></a>监视计算和应用服务
“计算”下面有四个选项卡：

- **概述**：安全中心识别的监视问题和建议。
- **VM 和计算机**：列出 VM 和计算机及其当前安全状态。
- **云服务**：列出安全中心监视的 Web 角色和辅助角色。
- **应用服务(预览版)**：列出应用服务环境及其当前安全状态。
若要继续，请在“资源”或安全中心主菜单中选择“计算和应用”。

![计算][2]

每个选项卡可能包含多个部分，在每个部分，可以选择单个选项详细查看可解决该特定问题的建议步骤。

### <a name="monitoring-recommendations"></a>监视建议
此部分显示已进行自动预配初始化的 VM 和计算机的总数及其当前状态。 此示例中有一个建议：**监视代理运行状况问题**。 选择此建议。

![监视代理运行状况问题][3]

此时会打开“监视代理运行状况问题”。 会列出安全中心无法成功监视的 VM 和计算机。 选择要获取其详细信息的 VM 或计算机。 “监视状态”提供安全中心不能进行监视的原因。 查看[安全中心故障排除指南](security-center-troubleshooting-guide.md)可获取一个列表，其中包含“监视状态”的值、说明和解决步骤。

### 不受监视的 VM 和计算机 <a name="unmonitored-vms-and-computers"></a>
如果某个 VM 或计算机未运行 Microsoft Monitoring Agent 扩展，则它不受安全中心的监视。 计算机上可能已安装了本地代理，例如 OMS 直接代理或 SCOM 代理。 装有这些代理的计算机被标识为未受监视，因为安全中心不完全支持这些代理。 若要充分利用安全中心的所有功能，需要使用 Microsoft Monitoring Agent 扩展。

除了已安装的本地代理，还可以在未受监视的 VM 或计算机上安装该扩展。 对两个代理进行相同的配置，将二者连接到同一工作区。 这样，安全中心就能与 Microsoft Monitoring Agent 扩展交互并收集数据。 请参阅[启用 VM 扩展](../log-analytics/log-analytics-quick-collect-azurevm.md)，获取如何安装 Microsoft Monitoring Agent 扩展的说明。

请参阅[监视代理运行状况问题](security-center-troubleshooting-guide.md#mon-agent)，详细了解安全中心无法成功监视那些已针对自动预配初始化的 VM 和计算机的原因。

### <a name="recommendations"></a>建议
此部分包含安全中心监视的每个 VM 和计算机、Web 和辅助角色、Azure 应用服务 Web 应用以及 Azure 应用服务环境适用的一系列建议。 第一列列出了建议。 第二列显示受该建议影响的资源总数。 第三列显示问题的严重性，如以下屏幕截图所示：

![建议][4]

每条建议附带一组操作，可在选择该建议后执行这些操作。 例如，如果选择“缺失的系统更新”，则会显示缺少修补程序的 VM 和计算机数目，以及所缺更新的严重性，如以下屏幕截图所示：

![应用系统更新][5]

“应用系统更新”以图形格式汇总了关键更新，分别适用于 Windows 和 Linux。 第二部分有一个表，其中包含以下信息：

- 名称：所缺更新的名称。
- VM 和计算机数：缺少该更新的 VM 和计算机的总数。
- **更新严重性**：描述该特定建议的严重性：

    - **关键**：重要资源（应用程序、虚拟机或网络安全组）存在漏洞，需要引起注意。
    - **重要**：需要采取非关键步骤或额外步骤来完成某个过程或消除某个漏洞。
    - **中**：应解决漏洞，但不需要立即处理。 （默认情况下，不显示严重性低的建议，但如果用户需要查看这些建议，可以将其筛选出来。）


- **状态**：该建议的当前状态：

    - **未解决**：建议尚未得到处理。
    - **正在进行**：目前已将建议应用到相关资源，不需用户采取行动。
    - **已解决**：已完成建议。 （解决问题后，此条目将变暗）。

若要查看建议详细信息，请单击列表中缺失更新的名称。

![建议详细信息][6]

> [!NOTE]
> 此处提供的安全建议与“建议”磁贴下的内容相同。 请参阅[在 Azure 安全中心实施安全建议](security-center-recommendations.md)，详细了解如何应用建议。
>
>

### <a name="vms-and-computers"></a>VM 和计算机
“VM 和计算机”部分提供所有 VM 和计算机建议的概述。 每一列代表一组建议，如以下屏幕截图所示：

![VM 和计算机建议][7]

此列表中显示四种类型的图标：

![非 Azure 计算机][8] 非 Azure 计算机。

![Azure 资源管理器 VM][9] Azure 资源管理器 VM。

![Azure 经典 VM][10] Azure 经典 VM。

![从工作区中识别的 VM][11] 只能通过工作区（属于所查看的订阅）标识的 VM。 其中包括其他订阅中向此订阅中的工作区报告的 VM，以及使用 SCOM 直接代理安装的 VM（没有资源 ID）。

每条建议下面显示的图标有助于快速识别需要关注的 VM 和计算机以及建议的类型。 也可使用“筛选器”选项来选择需要在此屏幕上显示的具体选项。

![筛选器][12]

在上面的示例中，一个 VM 有一条关于终结点保护的关键建议。 选择该 VM 可获取更多相关信息：

![关键建议][13]

此处显示 VM 或计算机的安全详细信息。 在底部可以看到建议的操作以及每个问题的严重性。

### <a name="cloud-services"></a>云服务
对于云服务，会在操作系统版本过期时创建建议，如以下屏幕截图所示：

![云服务][14]

如果确实提供了建议（在上一示例中不是这样），则需要遵循建议中的步骤更新操作系统版本。 如果有可用的更新，会出现警报（红色或橙色 - 取决于问题的严重性）。 如果选择 WebRole1（运行 Windows Server，将 Web 应用自动部署到 IIS）或 WorkerRole1（运行 Windows Server，将 Web 应用自动部署到 IIS）行中的此警报，则会显示有关此建议的更多详细信息，如以下屏幕截图所示：

![WorkerRole1][15]

若要查看有关此建议的更全面的说明，请在“说明”栏下单击“更新 OS 版本”。

![更新 OS 版本][16]

### <a name="app-services-preview"></a>应用服务(预览)

> [!NOTE]
> 监视应用服务的功能以预览版提供，仅适用于安全中心的标准层。 若要详细了解安全中心的定价层，请参阅[定价](security-center-pricing.md)。
>
>

在“应用服务”下，可以看到应用服务环境的列表，以及安全中心执行评估后提供的运行状况摘要。

![应用程序服务][17]

此列表中显示三种类型的图标：

![应用服务环境][18] 应用服务环境。

![Web 应用程序][19] Web 应用程序。

![函数应用程序][24] 函数应用程序。

1. 选择 Web 应用程序。 此时会打开摘要视图，其中包含三个选项卡：

  - **建议**：基于安全中心执行的失败的评估。
  - **已通过的评估**：安全中心执行的、已通过的评估列表。
  - **不可用的评估**：由于出错或者建议与特定的应用服务不相关，而未能运行的评估列表。

  “建议”下面是选定 Web 应用程序的建议列表，以及每条建议的严重性。

  ![摘要视图][20]

2. 选择一条建议可以查看其说明、不正常和正常资源以及未扫描的资源的列表。

  ![建议说明][21]

  “已通过的评估”下面是已通过的评估列表。  这些评估的严重性始终为绿色。

  ![已通过的评估][22]

3. 从评估说明列表、不正常和正常资源列表以及未扫描的资源列表中选择一个已通过的评估。 不正常的资源有一个对应的选项卡，但其中的列表始终为空，因为评估已经通过。

    ![正常资源][23]



## <a name="next-steps"></a>后续步骤
若要了解有关适用于其他 Azure 资源类型的建议的详细信息，请参阅以下内容：


* [了解虚拟机的 Azure 安全中心建议](security-center-virtual-machine-recommendations.md)
* [在 Azure 安全中心监视标识和访问](security-center-identity-access.md)
* [保护 Azure 安全中心中的网络](security-center-network-recommendations.md)
* [保护 Azure 安全中心中的 Azure SQL 服务](security-center-sql-service-recommendations.md)

若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [Azure 安全中心常见问题](security-center-faq.md) - 查找有关使用服务的常见问题。

<!--Image references-->
[1]: ./media/security-center-virtual-machine-recommendations/overview.png
[2]: ./media/security-center-virtual-machine-recommendations/compute.png
[3]: ./media/security-center-virtual-machine-recommendations/monitoring-agent-health-issues.png
[4]: ./media/security-center-virtual-machine-recommendations/compute-recommendations.png
[5]: ./media/security-center-virtual-machine-recommendations/apply-system-updates.png
[6]: ./media/security-center-virtual-machine-recommendations/missing-update-details.png
[7]: ./media/security-center-virtual-machine-recommendations/vm-computers.png
[8]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png
[9]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png
[10]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png
[11]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png
[12]: ./media/security-center-virtual-machine-recommendations/filter.png
[13]: ./media/security-center-virtual-machine-recommendations/vm-detail.png
[14]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png
[15]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new3.png
[16]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png
[17]: ./media/security-center-virtual-machine-recommendations/app-services.png
[18]: ./media/security-center-virtual-machine-recommendations/ase.png
[19]: ./media/security-center-virtual-machine-recommendations/web-app.png
[20]: ./media/security-center-virtual-machine-recommendations/recommendation.png
[21]: ./media/security-center-virtual-machine-recommendations/recommendation-desc.png
[22]: ./media/security-center-virtual-machine-recommendations/passed-assessment.png
[23]: ./media/security-center-virtual-machine-recommendations/healthy-resources.png
[24]: ./media/security-center-virtual-machine-recommendations/function-app.png
