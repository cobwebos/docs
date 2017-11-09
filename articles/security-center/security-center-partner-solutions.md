---
title: "在 Azure 安全中心内管理已连接的合作伙伴解决方案 | Microsoft Docs"
description: "本文档将对 Azure 安全中心如何让你轻松监视与 Azure 订阅集成的合作伙伴解决方案的运行状态进行指导。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: terrylan
ms.openlocfilehash: 181e1e00716987732ee809df6171c2f71087f3e1
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2017
---
# <a name="managing-connected-partner-solutions-with-azure-security-center"></a>在 Azure 安全中心内管理已连接的合作伙伴解决方案
本文介绍了如何在 Azure 安全中心内管理和监视已连接的安全解决方案。

## <a name="monitoring-partner-solutions"></a>监视合作伙伴解决方案
监视已连接的安全解决方案的运行状况并执行基本管理：

1. 在“安全中心 - 概述”下，选择“安全解决方案”。

  ![选择安全解决方案][1]

  “已连接的解决方案”部分包括连接到安全中心的安全解决方案，以及每个解决方案的运行状况信息。

  ![合作伙伴解决方案][2]

   合作伙伴解决方案的状态可能为：

   * 正常（绿色）- 没有运行状况问题。
   * 不正常（红色）- 有一个运行状况问题需要立即注意。
   * 运行状况问题（橙色）- 解决方案已停止报告其运行状况。
   * 未报告（灰色）- 解决方案未报告任何内容，如果它最近已连接且仍在部署或者没有运行状况数据可用，则可能不会报告解决方案的状态。

   > [!NOTE]
   > 如果没有运行状况数据可用，则安全中心会显示上次收到事件的日期和时间以指示解决方案是否正在报告。 如果没有运行状况数据可用且在过去 14 天内没有收到警报，则安全中心会指出解决方案不正常或未在报告。
   >
   >

2. 选择“查看”以了解其他信息和选项，其中包括：

  - **解决方案控制台**。 打开此解决方案的管理体验。
  - **链接 VM**。 打开“链接应用程序”边栏选项卡。 此处，可将资源连接到合作伙伴解决方案。
  - **删除解决方案**。
  - **配置**。

   ![合作伙伴解决方案详细信息][3]

## <a name="next-steps"></a>后续步骤
在本文中，你已学习了如何在安全中心内管理和监视已连接的安全解决方案。 若要了解有关安全中心的详细信息，请参阅以下文章：

* [安全解决方案概述](security-center-partner-integration.md) - 了解如何连接和管理安全解决方案。
* [连接 Microsoft 高级威胁分析 (ATA)](security-center-ata-integration.md) - 了解如何从 ATA 连接警报。
* [连接 Azure Active Directory (AD) Identity Protection](security-center-aadip-integration.md) - 了解如何从 Azure AD Identity Protection 连接警报。
* [合作伙伴和解决方案集成](security-center-partner-integration.md) - 获取集成其他安全解决方案的概述。
* [管理和响应安全警报](security-center-managing-and-responding-alerts.md) — 了解如何管理和响应安全警报。
* [Azure Security Center FAQ](security-center-faq.md) （Azure 安全中心常见问题）- 查找有关如何使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章。

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/partner-solutions.png
[3]: ./media/security-center-partner-solutions/partner-solutions-detail.png
