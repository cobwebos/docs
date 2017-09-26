---
title: "Azure 安全中心的威胁智能 | Microsoft Docs"
description: "本文档可帮助利用 Azure 安全中心的威胁智能功能，确定 VM 和计算机中的潜在威胁。"
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
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: c492662aa3ee444347c55d9771790573ad969150
ms.contentlocale: zh-cn
ms.lasthandoff: 09/14/2017

---
# <a name="threat-intelligence-in-azure-security-center"></a>Azure 安全中心的威胁智能
本文档可帮助使用 Azure 安全中心威胁智能解决与安全性相关的问题。

## <a name="what-is-threat-intelligence"></a>什么是威胁智能？
通过使用安全中心内提供的“威胁智能”选项，IT 管理员可根据环境识别安全威胁，例如识别某台特定的计算机是否是僵尸网络的一部分。 当攻击者非法安装秘密将此计算机连接到命令和控制的恶意软件时，该计算机就成为了僵尸网络的节点。 它还可以识别来自地下通信通道（例如暗网）的潜在威胁。

为了构建这种威胁智能系统，安全中心使用 Microsoft 中来自多个源的数据。 安全中心利用此数据根据环境识别潜在威胁。 威胁智能窗格由三个主要选项构成：

- 检测到的威胁类型
- 威胁源
- 威胁智能地图


## <a name="when-should-i-use-threat-intelligence"></a>何时应使用威胁智能？
[安全事件响应过程](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response)中的一个步骤是确定危害系统的严重程度。 在此阶段中应执行以下任务：

- 确定攻击性质
- 确定攻击起始点
- 确定攻击意图。 攻击是专门针对组织以获取特定信息，还是随机攻击？
- 识别已破坏的系统
- 识别已访问的文件，并确定这些文件的敏感性；可以利用安全中心内的威胁智能信息来帮助完成这些任务。 

## <a name="how-to-access-the-threat-intelligence"></a>如何访问威胁智能？
若要直观显示环境的当前威胁智能，必须先选择信息所在的工作区。 如果没有多个工作区，将不会显示工作区选择器，并且将直接转到“威胁智能”仪表板。 按照以下步骤访问“威胁智能”仪表板：

1.  打开“安全中心”仪表板。
2.  在左窗格中，单击“检测”下的“威胁智能”。 此时将显示“威胁智能”仪表板。

    ![威胁智能](./media/security-center-threat-intel/security-center-threat-intel-fig1.png)

    > [!NOTE]
    > 如果最后一列显示“升级计划”，是因为此工作区使用的是免费订阅，需要先升级到标准订阅才能使用此功能。 如果显示“需要更新”，是因为需要更新 [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) 才能使用此功能。 有关定价计划的详细信息，请阅读“Azure 安全中心定价”。 
    > 
3. 如果要调查多个工作区，可以根据“恶意 IP”列（显示此工作区中的当前恶意 IP 数）设置调查优先级。 选择要使用的工作区，将显示“威胁智能”仪表板。

    ![威胁智能](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

4. 此仪表板分为四个磁贴：
    * **威胁类型**：汇总了所选工作区中检测到的威胁类型。
    * **源国家/地区**：根据流量的源位置聚合流量。
    * **威胁位置**：帮助确定全球范围内与你的环境进行通信的当前位置。 此地图中有橙色（传入）和红色（传出）箭头，标识流量方向；如果单击其中一个箭头，会显示威胁类型和流量方向。
    * **威胁详细信息**：显示有关地图中所选威胁的更多详细信息。

无论选择哪个选项磁贴，将显示的仪表板都取决于[日志搜索查询](https://docs.microsoft.com/azure/security-center/security-center-search)，唯一的区别是查询的类型和结果。

### <a name="threat-types"></a>威胁类型
单击“威胁类型”磁贴后，将显示“日志搜索”仪表板，在该仪表板上左侧显示筛选器选项，右侧显示查询结果。

![威胁智能搜索](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

查询结果按名称显示威胁，可以使用左窗格选择要筛选的属性，例如若要仅查看当前连接到计算机的威胁，请在 **SESSIONSTATE** 中选择“已连接”，然后单击“应用”按钮。

![会话状态](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

对于 Azure VM，只有流过代理的网络数据将显示在“威胁智能”仪表板中。 威胁智能也使用以下数据类型：

- CEF 数据（类型 = CommonSecurityLog）
- WireData（类型 = WireData）
- IIS 日志（类型 = W3CIISLog）
- Windows 防火墙（类型 = WindowsFirewall）
- DNS 事件（类型 = DnsEvents）


## <a name="see-also"></a>另请参阅
本文档介绍了如何使用安全中心内的威胁智能帮助确定可疑活动。 若要了解更多有关 Azure 安全中心的详细信息，请参阅以下内容：

* [Managing and responding to security alerts in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)（管理和响应 Azure 安全中心的安全警报）。 了解如何管理警报并响应安全中心的安全事件。
* [Security health monitoring in Azure Security Center](security-center-monitoring.md)（在 Azure 安全中心进行安全运行状况监视）。 了解如何监视 Azure 资源的运行状况。
* [了解 Azure 安全中心中的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 了解不同类型的安全警报。
* [Azure 安全中心故障排除指南](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 了解如何排查安全中心的常见问题。 
* [Azure Security Center FAQ](security-center-faq.md)（Azure 安全中心常见问题）。 查找有关如何使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/)。 查找关于 Azure 安全性及合规性的博客文章。


