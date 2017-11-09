---
title: "在 Azure 安全中心监视标识和访问 | Microsoft Docs"
description: "了解如何使用 Azure 安全中心的标识和访问功能监视用户的访问活动和标识相关问题。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: 842045fbcb5b4f661cc006a207f4087370a7b4ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-identity-and-access-in-azure-security-center"></a>在 Azure 安全中心监视标识和访问
本文介绍如何使用 Azure 安全中心监视用户的标识和访问活动。

## <a name="why-monitor-identity-and-access"></a>为什么监视标识和访问？
标识应为企业的控制平面，保护标识应为头等大事。 在过去，组织存在外围，这些些外围是主要防御边界之一。 而现在，随着越来越多的数据和应用移到云中，标识成为新的外围。

监视标识活动即可在事件发生前主动采取措施，或事后采取针对性措施来阻止攻击尝试。 “标识和访问”仪表板概述了标识状态，其中包括：

* 登录尝试失败次数。 
* 在尝试时使用的用户帐户。
* 已锁定的帐户。
* 密码已更改或重置的帐户。 
* 当前已登录的帐户数目。

## <a name="monitor-identity-and-access-activities"></a>监视标识和访问活动
若要查看与标识和访问相关的当前活动，需要访问“标识和访问”仪表板。

1. 打开“安全中心”仪表板。

2. 在左窗格中，选择“防护”下的“标识和访问”。 如果有多个工作区，将显示工作区选择器。

    ![工作区选择](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > 如果最右侧的栏显示“升级计划”，则该工作区是在使用免费订阅。 升级到“标准”订阅即可使用此功能。 如果最右侧的栏显示“需要更新”，则更新 [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) 即可使用此功能。 有关定价计划的详细信息，请阅读“安全中心定价”。 
    > 
3. 如果有多个工作区需要调查，则可根据“失败的登录”栏来确定调查的优先级。 该栏显示目前此工作区中登录尝试不成功的数目。 选择要使用的工作区，将显示“标识和访问”仪表板。

    ![标识和访问](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. 此仪表板中提供的信息可立即用于识别潜在的可疑活动。 仪表板分为三个主要区域：

    a. 标识态势。 汇总了此工作区中发生的与标识相关的活动。

    b. 失败的登录。 用于快速确定登录尝试失败的主要原因。 显示一个列表，其中包含登录尝试失败次数最多的 10 大帐户。

    c. 一段时间内的登录数。 用于快速确定一段时间内的登录数。 它显示一个列表，其中包含排在前面的计算机帐户登录尝试次数。
    
不管所选选项磁贴为何，显示的仪表板始终基于日志搜索查询。 唯一区别是查询类型和结果。 仍可以选择一项（例如一台计算机），并查看相关数据。 

## <a name="see-also"></a>另请参阅
本文介绍了如何在安全中心监视标识和访问。 若要详细了解安全中心，请参阅以下文章：

* [管理和响应 Azure 安全中心的安全警报](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 了解如何管理警报并响应安全中心的安全事件。
* [Security health monitoring in Azure Security Center](security-center-monitoring.md)（在 Azure 安全中心进行安全运行状况监视）。 了解如何监视 Azure 资源的运行状况。
* [了解 Azure 安全中心的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 了解不同类型的安全警报。
* [Azure 安全中心故障排除指南](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 了解如何排查安全中心的常见问题。 
* [Azure Security Center FAQ](security-center-faq.md)（Azure 安全中心常见问题）。 查找安全中心使用方面的常见问题解答。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/)。 查找关于 Azure 安全性及合规性的博客文章。

