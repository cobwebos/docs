---
title: "Azure 安全中心的威胁智能 | Microsoft Docs"
description: "了解如何使用 Azure 安全中心的威胁智能功能，确定 VM 和计算机中的潜在威胁。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: b5bdeb1c70893c2477413df02c7b1a4a07999e93
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="threat-intelligence-in-azure-security-center"></a>Azure 安全中心的威胁智能
本文介绍如何使用 Azure 安全中心威胁智能解决与安全性相关的问题。

## <a name="what-is-threat-intelligence"></a>什么是威胁智能？
通过使用安全中心内提供的“威胁智能”选项，IT 管理员可根据环境识别安全威胁。 例如，可以识别某台特定的计算机是否是僵尸网络的一部分。 当攻击者非法安装秘密将此计算机连接到命令和控制的恶意软件时，该计算机就成为了僵尸网络的节点。 威胁智能还可以识别来自地下通信通道（例如暗网）的潜在威胁。

为了构建这种威胁智能系统，安全中心使用 Microsoft 中来自多个源的数据。 安全中心使用此数据根据环境识别潜在威胁。 “威胁智能”窗格由三个主要选项构成：

- 检测到的威胁类型
- 威胁源
- 威胁智能地图


## <a name="when-should-you-use-threat-intelligence"></a>何时应使用威胁智能？
[安全事件响应过程](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response)中的一个步骤是确定受损系统的受损程度。 在此阶段中应执行以下任务：

- 确定攻击性质。
- 确定攻击起始点。
- 确定攻击意图。 攻击是针对组织以获取特定信息，还是随机攻击？
- 识别受损系统。
- 识别被访问的文件，并确定这些文件的敏感性。

可以使用安全中心的威胁智能信息来完成这些任务。 

## <a name="access-the-threat-intelligence"></a>访问威胁智能
若要直观显示环境的当前威胁智能，必须先选择信息所在的工作区。 如果没有多个工作区，请绕过工作区选择器，直接转到“威胁智能”仪表板。 若要访问仪表板，请执行以下操作：

1. 打开“安全中心”仪表板。

2. 在左窗格中，选择“检测”下的“威胁智能”。 此时将显示“威胁智能”仪表板。

    ![“威胁智能”仪表板](./media/security-center-threat-intel/security-center-threat-intel-fig1.png)

    > [!NOTE]
    > 如果最右侧的栏显示“升级计划”，则该工作区是在使用免费订阅。 升级到“标准”即可使用此功能。 如果最右侧的栏显示“需要更新”，则更新 [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) 即可使用此功能。 有关定价计划的详细信息，请阅读“Azure 安全中心定价”。 
    > 
3. 如果有多个工作区需要调查，则根据“恶意 IP”栏来确定调查的优先级。 该栏显示此工作区中恶意 IP 的当前数目。 选择要使用的工作区，将显示“威胁智能”仪表板。

    ![威胁智能信息](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

4. 仪表板分为四个磁贴：

    a.  威胁类型。 汇总了所选工作区中检测到的威胁类型。

    b.  源国家/地区。 根据流量的源位置聚合流量。

    c.  威胁位置。 帮助确定全球范围内与你的环境进行通信的当前位置。 在显示的地图中，橙色（传入）和红色（传出）箭头标识流量方向。 如果选择其中某个箭头，则会显示威胁类型和流量方向。

    d.单击“下一步”。  威胁详细信息。 显示有关地图中所选威胁的更多详细信息。

不管所选选项磁贴为何，显示的仪表板始终基于日志搜索查询。 唯一区别是查询类型和结果。

### <a name="threat-types"></a>威胁类型
选择“威胁类型”磁贴，打开“日志搜索”仪表板。 筛选器选项显示在左侧，查询结果显示在右侧。

![日志搜索](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

查询结果按名称显示威胁。 可以使用左窗格选择要筛选的属性。 例如，若只要查看当前连接到计算机的威胁，请在“SESSIONSTATE”中选择“已连接” > “应用”。

![会话状态](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

对于 Azure VM，只有流过代理的网络数据显示在“威胁智能”仪表板中。 威胁智能也使用以下数据类型：

- CEF 数据（类型 = CommonSecurityLog）
- WireData（类型 = WireData）
- IIS 日志（类型 = W3CIISLog）
- Windows 防火墙（类型 = WindowsFirewall）
- DNS 事件（类型 = DnsEvents）


## <a name="see-also"></a>另请参阅
本文介绍了如何使用安全中心的威胁智能来确定可疑活动。 若要详细了解安全中心，请参阅以下文章：

* [管理和响应 Azure 安全中心的安全警报](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 了解如何管理警报并响应安全中心的安全事件。
* [Security health monitoring in Azure Security Center](security-center-monitoring.md)（在 Azure 安全中心进行安全运行状况监视）。 了解如何监视 Azure 资源的运行状况。
* [了解 Azure 安全中心的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 了解不同类型的安全警报。
* [Azure 安全中心故障排除指南](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 了解如何排查安全中心的常见问题。 
* [Azure Security Center FAQ](security-center-faq.md)（Azure 安全中心常见问题）。 查找服务使用方面的常见问题解答。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/)。 查找关于 Azure 安全性及合规性的博客文章。

