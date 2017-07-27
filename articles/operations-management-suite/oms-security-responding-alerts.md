---
title: "监视和响应 Operations Management Suite 安全和审核解决方案中的安全警报 | Microsoft 文档"
description: "本文档有助于使用 OMS 安全和审核中的威胁智能选项监视和响应安全警报。"
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 7d45a32b-1341-4bb5-a436-1f42a8a2590a
ms.service: operations-management-suite
ms.custom: oms-security
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/13/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 0cf9b83d7023641ec445a59a5e61d3da038695fa
ms.contentlocale: zh-cn
ms.lasthandoff: 06/17/2017


---
# <a name="monitoring-and-responding-to-security-alerts-in-operations-management-suite-security-and-audit-solution"></a>监视和响应 Operations Management Suite 安全和审核解决方案中的安全警报
本文档有助于使用 OMS 安全和审核中的威胁智能选项监视和响应安全警报。

## <a name="what-is-oms"></a>什么是 OMS？
Microsoft Operations Management Suite (OMS) 是 Microsoft 的基于云的 IT 管理解决方案，可帮助你管理和保护你的本地和云基础结构。 有关 OMS 的详细信息，请参阅文章 [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx)。

## <a name="threat-intelligence"></a>威胁情报
在用户可以广泛访问网络，并且使用各种设备连接到企业数据的企业环境中，必须主动监视资源，并且快速响应安全事件。 这从安全生命周期角度看尤为重要，因为某些网络安全威胁可能不会引发为传统安全技术控制所识别的警报或可疑活动。 

通过使用 OMS 安全和审核中的“威胁智能”选项，IT 管理员可根据环境识别安全威胁，例如识别某台特定的计算机是否是[僵尸网络](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection)的一部分。 当攻击者非法安装秘密将此计算机连接到命令和控制的恶意软件时，该计算机就成为了僵尸网络的节点。 它还可以识别来自地下通信通道（例如[暗网](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection_honeypots_darkents)）的潜在威胁。 

为了构建这种威胁智能系统，OMS 安全和审核使用 Microsoft 中多个来源的数据。 OMS 安全和审核将利用此数据根据环境识别潜在威胁。

威胁智能窗格由三个主要选项构成：

* 具有恶意出站流量的服务器
* 检测到的威胁类型
* 威胁智能地图

> [!NOTE]
> 有关所有这些选项的概述，请阅读 [Operations Management Suite 安全和审核解决方案入门](oms-security-getting-started.md)。
> 
> 

### <a name="responding-to-security-alerts"></a>响应安全警报
[安全事件响应](https://technet.microsoft.com/library/cc512623.aspx)过程的其中一个步骤是识别危害系统的严重程度。 在此阶段中应执行以下任务：

* 确定攻击性质
* 确定攻击起始点
* 确定攻击意图。 攻击是专门针对组织以获取特定信息，还是随机攻击？
* 识别已破坏的系统
* 识别已访问的文件，并确定这些文件的敏感性

可利用 OMS 安全和审核解决方案中的**威胁智能**信息帮助完成这些任务。 请遵循以下步骤，访问这些**威胁智能**选项：

1. 在“Microsoft Operations Management Suite”主仪表板中，单击“安全和审核”磁贴。
   
    ![安全和审核](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)
2. 在“安全和审核”仪表板中，在右侧可看到“威胁智能”选项，如下所示：
   
    ![威胁情报](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig2-ga.png)

这三个磁贴概述了当前威胁。 在“具有恶意出站流量的服务器”中，你将能够识别你在监视的计算机（在你所在网络之内或之外）是否将恶意流量发送到 Internet。 

“检测到的威胁类型”磁贴显示当前“野生”的威胁汇总，如果单击此磁贴，将看到关于这些威胁的详细信息，如下所示：

![检测到的威胁类型](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig3.png)

单击它可提取关于每个威胁的详细信息。 以下示例显示关于僵尸网络的详细信息：

![有关威胁的详细信息](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig4.png)

如本部分开头所述，此信息在事件响应情况下非常有用。 它在调查取证、需要查找攻击来源、识别被破坏系统和时间线时也很重要。 在此报告中可轻松识别关于攻击的某些关键详细信息，例如：攻击来源、被破坏的本地 IP 和连接的当前会话状态。 

**威胁智能地图**有助于识别全球范围内当前具有恶意流量的位置。 此地图中具有橙色（传入）和红色（传出）箭头，识别流量方向；如果单击其中一个箭头，将显示威胁类型和流量方向，如下所示：

![威胁情报地图](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig5.png)

> [!NOTE]
> 你可以观看演示，通过观看 Microsoft ignite 中提供的演示 [Mitigate datacenter security threats with guided investigation using Operations Management Suite](https://myignite.microsoft.com/videos/5000)（使用 Operations Management Suite，借助引导式调查缓解数据中心安全威胁），了解如何在事件响应过程中使用此功能。
> 

### <a name="responding-to-distinct-malicious-ip-accessed"></a>响应访问的非重复恶意 IP
在某些情况下，可能会注意到从一台受监视计算机访问的潜在恶意 IP：

![威胁情报地图](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig6.png)

OMS 安全利用 [Microsoft 威胁智能](https://youtu.be/O4WtxgUrDc8)生成此警报以及同一类别中的其他警报。 威胁智能数据由 Microsoft 收集，也可从领先的威胁智能提供商处购买。 此数据经常更新，适用于快速变化的威胁。 由于其自身的性质，[调查](https://blogs.technet.microsoft.com/msoms/2016/12/08/investigating-suspicious-activity-in-a-hybrid-cloud-with-oms-security/)安全警报时，应将它与其他来源的安全信息结合。 

## <a name="customize-alerts-received-via-e-mail"></a>自定义通过电子邮件接收的警报

可以自定义 OMS 安全触发安全警报时通知组织中哪些用户。 OMS 仪表板的“概述/设置”下提供了此选项：

![Email](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig7.png)

## <a name="see-also"></a>另请参阅
在本文档中，你学习了如何使用 OMS 安全和审核解决方案中的“威胁智能”选项响应安全警报。 若要了解有关 OMS 安全的详细信息，请参阅以下文章：

* [Operations Management Suite (OMS) 概述](operations-management-suite-overview.md)
* [Operations Management Suite 安全和审核解决方案入门](oms-security-getting-started.md)
* [监视 Operations Management Suite 安全和审核解决方案中的资源](oms-security-monitoring-resources.md)


