---
title: Azure 安全中心内的威胁检测 | Microsoft Docs
description: " 通过将 Microsoft Cloud App Security 与 Azure 安全中心集成来检测威胁和恶意软件。 "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: 2a747bdd8de41283b9cba1e40e2652aa826e9c60
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2018
ms.locfileid: "48044444"
---
# <a name="ueba-for-azure-resources-and-users"></a>用于 Azure 资源和用户的 UEBA 

Azure 安全中心与 Microsoft Cloud App Security 合作，可根据 Azure 资源和用户（Azure 活动）的用户与实体行为分析 (UEBA) 发出警报。 这些警报基于用户和实体行为分析与机器学习 (ML)，可检测用户行为中的异常情况，使你能够立即针对订阅的活动运行高级威胁检测。 由于这些警报是自动启用的，新的异常情况检测功能可通过提供即时检测和瞄准与订阅关联的用户和资源中的大量行为异常情况，来提供即时结果。 此外，这些警报利用 Microsoft Cloud App Security 检测引擎中已存在的附加数据，来帮助你加速调查过程和遏制今后的威胁。 

> [!NOTE]
> Azure 安全中心可以存储安全相关的客户数据的副本。这些数据是从某个客户资源（例如虚拟机或 Azure Active Directory 租户）收集的或者与其相关联，其存储位置为：(a) 该资源所在的同一地理位置，但 Microsoft 尚未部署 Azure 安全中心的地理位置除外，对于这种情况，此类数据的副本将存储在美国；(b) 如果 Azure 安全中心使用另一个 Microsoft 联机服务来处理此类数据，则可以根据该联机服务的地理位置规则来存储此类数据。
>

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>先决条件

- 有效且已激活的 [Microsoft Cloud App Security 许可证](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)
- [安全中心标准层](https://azure.microsoft.com/pricing/details/security-center/)
 
## <a name="threat-detection-alerts"></a>威胁检测警报

安全中心支持 Cloud App Security 异常情况检测警报，例如：

**不可能的旅行**
-  此项检测可识别来源于保持一定地理距离的位置的两个用户活动（一个或多个会话），而完成这两个活动的时间段短于该用户从第一个位置旅行到第二个位置所需的时间，这表示另一个用户在使用相同的凭据。 此项检测利用的机器学习算法会忽略明显的“误报”，从而改善不可能前往条件，例如组织中其他用户定期使用的 VPN 和位置。 此项检测的初始学习期限为 7 天，在此期间，它会学习新用户的活动模式。

**来自不常见国家/地区的活动**
- 此项检测考虑过去的活动位置，以确定新的和不常见的位置。 异常情况检测引擎将存储组织中用户以往用过的位置的相关信息。 当组织中的任何用户最近未访问或从未访问过的位置发生某个活动时，会触发警报。 

**来自匿名 IP 地址的活动**
- 此项检测可以识别使用已标识为匿名代理 IP 地址的 IP 地址展开活动的用户。 这些代理通常是想要隐藏其设备 IP 地址的用户才会使用，他们可能意图不良。 此项检测利用的机器学习算法可以减少“误报”，例如，错误地标记组织中用户广泛使用的 IP 地址。
 
  ![威胁检测警报](./media/security-center-ueba-mcas/security-center-mcas-alert.png)

## <a name="disabling-threat-detection-alerts"></a>禁用威胁检测警报

这些警报默认已启用，但可将其禁用：

1. 在“安全中心”边栏选项卡中，选择“安全策略”。 针对要更改的订阅，单击“编辑设置”。
2.  单击“威胁检测”。
3. 在“启用集成”下，取消选中“允许 Microsoft Cloud App Security 访问我的数据”，然后单击“保存”。

   ![威胁检测警报](./media/security-center-ueba-mcas/security-center-mcas-optout.png)

> [!NOTE]
> 有 7 天的初始学习期，在此期间，不一定会引发所有异常情况检测警报。 此期限过后，每个会话将与过去一个月检测到的活动、用户活跃时间、IP 地址、设备等以及这些活动的风险评分进行比较。 这些检测是机器学习异常情况检测引擎的一部分，该引擎可以分析环境，并根据在组织活动中学习到的基线触发警报。 这些检测还利用旨在分析用户和登录模式，以减少误报的机器学习算法。
>
  
## <a name="next-steps"></a>后续步骤
本文介绍了如何使用 Azure 安全中心内的威胁检测。 若要了解有关安全中心的详细信息，请参阅以下文章：

* [Azure 安全中心常见问题解答](security-center-faq.md)-- 查找有关使用服务的常见问题。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况。



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
