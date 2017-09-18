---
title: "在 Azure 安全中心内监视标识和访问 | Microsoft Docs"
description: "本文档可帮助使用 Azure 安全中心内的标识和访问功能监视用户的访问活动和标识相关问题。"
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
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 2e351cd38e7baefc09fa36ceabffec92de22433b
ms.contentlocale: zh-cn
ms.lasthandoff: 09/14/2017

---
# <a name="monitoring-identity-and-access-in-azure-security-center"></a>在 Azure 安全中心内监视标识和访问
本文档可帮助使用 Azure 安全中心监视用户的标识和访问活动。

## <a name="why-monitor-identity-and-access"></a>为什么监视标识和访问？
身份应为企业的控制平面，保护身份应为头等大事。 在过去，组织周围存在边界，这些边界成为主要防御边界之一；现如今，越来越多的数据和应用移动到云中，身份则成为新的外围。

通过监视身份活动，能够在事件发生前主动采取措施，或事后采取针对性措施停止攻击。 “标识和访问”仪表板提供标识状态概述，包括登录尝试失败数、失败尝试期间所用的用户帐户、已锁定的帐户、具有已更改或已重置密码的帐户，以及当前登录的帐户数。

## <a name="how-to-monitor-identity-and-access-activities"></a>如何监视标识和访问活动？
按照以下步骤来直观地显示与当前活动相关的标识和访问，需要访问“标识和访问”仪表板：

1.  打开“安全中心”仪表板。
2.  在左窗格中，单击“防护”下的“标识和访问”。 如果有多个工作区，将显示工作区选择器。

    ![工作区选择](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > 如果最后一列显示“升级计划”，是因为此工作区使用的是免费订阅，需要先升级到标准订阅才能使用此功能。 如果显示“需要更新”，是因为需要更新 [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) 才能使用此功能。 有关定价计划的详细信息，请阅读“Azure 安全中心定价”。 
    > 
3. 如果要调查多个工作区，可以根据“失败登录次数”列（显示此工作区的当前失败登录尝试次数）设置调查优先级。 选择要使用的工作区，将显示“标识和访问”仪表板。

    ![标识和访问](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. 此仪表板中提供的信息可立即帮助识别潜在的可疑活动。 此仪表板分为三个主要区域：
    * **标识态势**：汇总了此工作区中发生的与标识相关的活动。
    * **失败登录**：可帮助快速确定登录尝试失败的主要原因，并显示登录尝试失败次数最多的前 10 个帐户的列表。
    * **登录超时**：可帮助快速确定登录超时量，并显示登录尝试次数最多的计算机帐户的列表。
    
无论选择哪个磁贴，将显示的仪表板都取决于[日志搜索查询](https://docs.microsoft.com/azure/security-center/security-center-search)，唯一的区别是查询的类型和结果。 仍可以选择一项（例如一台计算机），单击它并查看相关数据。 

## <a name="see-also"></a>另请参阅
本文档介绍了如何在 Azure 安全中心内监视标识和访问。 若要了解更多有关 Azure 安全中心的详细信息，请参阅以下内容：

* [Managing and responding to security alerts in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)（管理和响应 Azure 安全中心的安全警报）。 了解如何管理警报并响应安全中心的安全事件。
* [Security health monitoring in Azure Security Center](security-center-monitoring.md)（在 Azure 安全中心进行安全运行状况监视）。 了解如何监视 Azure 资源的运行状况。
* [了解 Azure 安全中心中的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 了解不同类型的安全警报。
* [Azure 安全中心故障排除指南](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 了解如何排查安全中心的常见问题。 
* [Azure Security Center FAQ](security-center-faq.md)（Azure 安全中心常见问题）。 查找有关如何使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/)。 查找关于 Azure 安全性及合规性的博客文章。


