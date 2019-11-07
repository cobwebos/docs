---
title: 威胁智能 & 安全警报映射-Azure 安全中心
description: 了解如何使用 Azure 安全中心的安全警报地图和威胁智能功能确定 VM 和计算机中的潜在威胁。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: 97975d72214426907a2ab91f0d3cd98d0ce6734b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693483"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>安全警报地图和威胁智能
本文可帮助你使用 Azure 安全中心安全警报冲突和基于安全事件的威胁智能地图来解决与安全相关的问题。

> [!NOTE]
> 安全*事件*映射按钮已于2019年7月31日停用。 有关详细信息和备用服务，请参阅用[安全中心功能的停用（2019 年 7 月）](security-center-features-retirement-july2019.md#menu_securityeventsmap)。


## <a name="how-the-security-alerts-map-works"></a>安全警报地图的工作原理
安全中心提供了可帮助识别针对环境的安全威胁的地图。 例如，可以识别特定计算机是否属于僵尸网络的一部分以及威胁来自何处。 当攻击者非法安装秘密与管理僵尸网络的命令和控件进行交互的恶意软件时，计算机就成为了僵尸网络的节点。 

为了构建此地图，安全中心使用 Microsoft 中来自多个源的数据。 安全中心使用此数据为针对环境的潜在威胁绘制地图。 

[安全事件响应过程](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response)中的一个步骤是确定受损系统的受损程度。 在此阶段中应执行以下任务：

- 确定攻击性质。
- 确定攻击起始点。
- 确定攻击意图。 攻击是针对组织以获取特定信息，还是随机攻击？
- 识别受损系统。
- 识别被访问的文件，并确定这些文件的敏感性。

可以使用安全中心的安全警报地图完成这些任务。

## <a name="access-the-security-alerts-map"></a>访问安全警报地图
若要可视化环境中的当前威胁，请打开安全警报地图：

1. 打开“安全中心”仪表板。
2. 在左窗格中，选择“威胁防护”下的“安全警报地图”。 地图随即打开。
3. 若要获取有关警报的详细信息并接收补救步骤，请单击地图上的警报圆点，并按照说明进行操作。 
 
安全警报地图基于警报。 这些警报基于其网络通信与已成功解析的 IP 地址关联的活动，无论 IP 地址是已知的有风险 IP 地址（例如，已知的加密矿机）还是以前未识别为有风险的 IP 地址。 地图提供之前在 Azure 中选择的任何订阅间的警报。 

地图上的警报根据检测到它们源自何处的地理位置进行显示，并按严重性进行颜色编码。 
    ![威胁智能信息](./media/security-center-threat-intel/security-center-alert-map.png)



## <a name="see-also"></a>另请参阅
本文介绍了如何使用安全中心的威胁智能来确定可疑活动。 若要详细了解安全中心，请参阅以下文章：

* [管理和响应 Azure 安全中心的安全警报](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 了解如何管理警报并响应安全中心的安全事件。
* [Security health monitoring in Azure Security Center](security-center-monitoring.md)（在 Azure 安全中心进行安全运行状况监视）。 了解如何监视 Azure 资源的运行状况。
* [了解 Azure 安全中心的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 了解不同类型的安全警报。
* [Azure 安全中心故障排除指南](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 了解如何排查安全中心的常见问题。
* [Azure Security Center FAQ](security-center-faq.md)（Azure 安全中心常见问题）。 查找服务使用方面的常见问题解答。
* [Azure 安全性博客](https://blogs.msdn.com/b/azuresecurity/)。 查找关于 Azure 安全性及合规性的博客文章。
