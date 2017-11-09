---
title: "使用 Azure 安全中心保护个人数据 | Microsoft Docs"
description: "使用 Azure 安全中心保护个人数据"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 3a941389713a4d3dbffbbfe8a717409927d85c6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="protect-personal-data-from-breaches-and-attacks-azure-security-center"></a>防范个人数据遭受破解和攻击：Azure 安全中心

本文帮助读者了解如何使用 Azure 安全中心来防范个人数据遭受破解和攻击。

## <a name="scenario"></a>方案 

总部位于美国的一家大型邮轮公司正在拓展其运营，以便在地中海和波罗的海以及英属岛屿提供路线。 为帮助实现这些目的，该公司并购了意大利、德国、丹麦和英国的几家小型邮轮公司。

该公司使用 Microsoft Azure 在云中存储企业数据。 这些数据包括个人身份信息，例如姓名、地址、电话号码和信用卡信息。 此外，还包括如下所述的人力资源信息：

- 地址
- 电话号码
- 税务识别号
- 医疗信息

该邮轮公司还维护一个奖励和忠诚计划会员的大型数据库。 企业员工从公司的远程办公室访问网络，世界各地的旅行社有权访问某些公司资源。
个人数据通过网络在这些位置与 Microsoft 数据中心之间遍历。

## <a name="problem-statement"></a>问题陈述

该公司担心其 Azure 资源受到攻击的威胁。 他们想要防止向未经授权的人员透露客户和员工的个人数据。 他们想要获得有关预防和响应/补救措施，以及有效持续监视其云资源安全性的指导。
他们需要针对当今的老练、有组织的攻击者设立强大的防线。

## <a name="company-goal"></a>公司目标

该公司的一项目标是防范客户和员工的个人数据受到威胁，以确保其隐私性。 他们的另一项目标是在出现破解的迹象时立即做出响应，以缓解造成的影响。 这需要采用某种方法来评估当前安全状态、识别有漏洞的配置并采取补救措施。

## <a name="solutions"></a>解决方案

Microsoft Azure 安全中心 (ASC) 提供集成的安全监视和策略管理解决方案。 它提供易用且有效的威胁预防、检测和响应功能。

### <a name="prevention"></a>预防

借助 ASC 可以通过设置安全策略、提供实时访问和实施安全建议来防止破解。

安全策略定义一组控件，这些控件是针对指定订阅中的资源建议的。 使用实时访问可以锁定发往 Azure VM 的入站流量，减少向攻击者透露信息的可能性。 分析 Azure 资源的安全状态后，ASC 会创建安全建议。

#### <a name="how-do-i-set-security-policies-in-asc"></a>如何在 ASC 中设置安全策略？

可为每个订阅配置安全策略。 若要修改安全策略，用户必须是该订阅的所有者或参与者。 在 Azure 门户中执行以下操作：

1. 在 ASC 仪表板中选择“策略”。

2. 选择要在其上启用策略的订阅。

3. 选择“预防策略”，为每个订阅配置策略。 “从虚拟机收集数据”应设置为“打开”。

4. 在“预防策略”选项中，选择“打开”以启用此订阅相关的安全建议。

![](media/protect-personal-data-azure-security-center/prevention-policy.png)

有关可启用的每条策略建议的详细说明和解释，请参阅[在 Azure 安全中心设置安全策略](https://docs.microsoft.com/azure/security-center/security-center-policies#set-security-policies)。

#### <a name="how-do-i-configure-just-in-time-access-jit"></a>如何配置实时访问 (JIT)？

启用 JIT 后，安全中心会通过创建 NSG 规则来锁定发往 Azure VM 的入站流量。 你需要选择要锁定 VM 上的哪些端口的入站流量。 若要使用 JIT 访问，请执行以下操作：

1. 在 ASC 边栏选项卡上，选择“实时 VM 访问”磁贴。

2. 选择“建议”选项卡。

3. 在“VM”下，选择要启用的 VM。 这会在 VM 旁边放置一个复选标记。 
4. 选择“在 VM 上启用 JIT”。
5. 选择“保存”。

然后，可以看到 ASC 建议对 JIT 启用的默认端口。 还可以添加和配置要在其上启用实时解决方案的新端口。 安全中心内的“实时 VM 访问”磁贴显示针对 JIT 访问配置的 VM 数目。 它还显示在过去一周发出的已批准访问请求数目。

![](media/protect-personal-data-azure-security-center/jit-vm.png)

有关如何执行此操作的说明，以及有关实时访问的其他信息，请参阅[使用实时功能管理虚拟机访问](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)。

#### <a name="how-do-i-implement-asc-security-recommendations"></a>如何实施 ASC 安全建议？

安全中心识别到潜在的安全漏洞时，会创建建议。 此建议指导完成配置所需控件的过程。 
1. 在 ASC 仪表板上选择“建议”磁贴。

2. 查看以表格格式显示的建议，其中每行表示一条建议。

3. 若要筛选建议，请选择“筛选”，并选择要查看的严重性和状态值。

4. 若要消除某条不适用的建议，可以单击右键并选择“消除”。

5. 评估应该先应用哪条建议。

6. 按优先顺序应用建议。

有关可能的建议的列表以及有关如何应用每条建议的演练，请参阅[管理 Azure 安全中心的安全建议](https://docs.microsoft.com/azure/security-center/security-center-recommendations)。

### <a name="detection-and-response"></a>检测和响应

检测和响应相辅相成，即，在检测到威胁后尽快做出响应。
ASC 可以自动从 Azure 资源、网络以及连接的合作伙伴解决方案收集安全信息，对威胁进行检测。 当攻击者发起越来越复杂的新攻击时，ASC 可以快速更新其检测算法。 有关 ASC 威胁检测工作原理的更多详细信息，请参阅 [Azure 安全中心检测功能](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)。

#### <a name="how-do-i-manage-and-respond-to-security-alerts"></a>如何管理和响应安全警报？

安全中心按优先级显示安全警报列表，并显示快速调查问题时所需的信息。 安全中心还包含有关如何针对攻击采取补救措施的建议。 若要管理安全警报，请执行以下操作：

1. 在 ASC 仪表板中选择“安全警报”磁贴。 此时会显示每条警报的详细信息。

2. 若要根据日期、状态和严重性筛选警报，请选择“筛选”，并选择想要查看的值。

3. 若要响应某条警报，请选择它并查看信息，再选择受到攻击的资源。

4. 在“说明”字段中可以看到详细信息，包括建议的补救措施。

![](media/protect-personal-data-azure-security-center/security-alerts.png)

有关如何响应安全警报的更多详细说明，请参阅[管理和响应 Azure 安全中心的安全警报](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。

若要进一步帮助调查安全警报，公司可以使用 [Azure 日志集成](https://aka.ms/AzLog)将 ASC 警报与其自己的 SIEM 解决方案相集成。

#### <a name="how-do-i-manage-security-incidents"></a>如何管理安全事件？

在 ASC 中，安全事件是针对某个资源发出的所有警报的聚合，与杀伤链模式保持一致。 事件会显示相关警报列表，可使你获取有关每个匹配项的详细信息。 事件显示在“安全警报”磁贴和边栏选项卡中。

若要查看和管理安全事件，请执行以下操作：

1. 选择“安全警报”磁贴。 如果检测到安全事件，它会显示在安全警报图形下面。 安全事件的图标不同于其他警报的图标。

2. 选择此安全事件可查看其更多详细信息。 其他详细信息包括其完整说明、严重性、当前状态、受攻击的资源、事件补救步骤，以及此事件中包含的警报。

可进行筛选，以便**仅查看事件**、**仅查看警报**，或**同时查看事件和警报**。

#### <a name="how-do-i-access-the-threat-intelligence-report"></a>如何访问威胁智能报告？

ASC 会分析多个来源的信息以识别威胁。 为帮助事件响应团队调查威胁和采取补救措施，安全中心提供威胁智能报告，其中包含有关已检测到的威胁的信息。

安全中心提供三种类型的威胁报告（根据攻击而异）。
可用报告有：

- 活动组报告：深入分析攻击者、其目标和策略。

- 活动报告：重点提供特定攻击活动的详细信息。

- 威胁摘要报告：包含前两个报告中的所有项。

在事件响应过程中，进行调查以了解攻击来源、攻击者的动机以及将来可缓解此问题的的操作时，这种类型的信息非常有用。

1. 若要访问威胁智能报告，请执行以下操作：

2. 在 ASC 仪表板中选择“安全警报”磁贴。

3. 选择要获取其详细信息的安全警报。

4. 在“报告”字段中，单击威胁智能报告的链接。

5. 此时会打开可供下载的 PDF 文件。

![](media/protect-personal-data-azure-security-center/security-alerts-suspicious-process.png)

有关 ASC 威胁智能报告的其他信息，请参阅 [Azure 安全中心威胁智能报告](https://docs.microsoft.com/azure/security-center/security-center-threat-report)。

### <a name="assessment"></a>评估

为帮助测试和评估安全态势，ASC 通过 Qualys 云代理（属于 ASC 的虚拟机建议组件）提供集成式漏洞评估。

Qualys 代理将漏洞数据报告给 Qualys 管理平台，后者再将漏洞和运行状况监视数据发回给 ASC。 有关添加漏洞评估解决方案的建议显示在 ASC 仪表板上的“建议”边栏选项卡中。

在目标 VM 上安装漏洞评估解决方案后，安全中心会扫描 VM，以便检测、标识系统和应用程序的漏洞。 删除的问题显示在“虚拟机建议”选项下。

#### <a name="how-do-i-implement-a-vulnerability-assessment-solution"></a>如何实施漏洞评估解决方案？ 

如果虚拟机上尚未部署集成式漏洞评估解决方案，安全中心会建议安装该解决方案。

1. 在 ASC 仪表板中的“建议”边栏选项卡上，选择“添加漏洞评估解决方案”。

2. 选择要在其上安装漏洞评估解决方案的 VM。

3. 单击“在 [数量] 个虚拟机上安装”。

4. 在 Azure Marketplace 中选择一个合作伙伴解决方案，或者在“使用现有解决方案”下选择“Qualys”。

5. 可在“合作伙伴解决方案”边栏选项卡中启用或禁用自动更新设置。

有关如何实施漏洞评估解决方案的其他说明，请参阅 [Azure 安全中心的漏洞评估](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)。

## <a name="next-steps"></a>后续步骤

- [Azure 安全中心快速入门指南](https://docs.microsoft.com/azure/security-center/security-center-get-started)

- [Azure 安全中心简介](https://docs.microsoft.com/azure/security-center/security-center-intro)

- [使用 Azure 日志集成来集成 Azure 安全中心警报](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration)

- [使用集成式漏洞评估大幅提升 Azure 安全中心的性能](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/16/boost-azure-security-center-with-integrated-vulnerability-assessment/)
