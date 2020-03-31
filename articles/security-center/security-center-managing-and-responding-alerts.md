---
title: 在 Azure 安全中心管理安全警报 | Microsoft 文档
description: 本文档旨在帮助使用 Azure 安全中心功能来管理和响应安全警报。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 6ea951b542d893b8fef3cdf19a964ce2ef8a034d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415704"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>管理和响应 Azure 安全中心中的安全警报

本主题介绍如何查看和处理您收到的警报，以保护资源。 

* 要了解不同类型的警报，请参阅[安全警报类型](alerts-reference.md)。
* 有关安全中心如何生成警报的概述，请参阅[Azure 安全中心如何检测和响应威胁](security-center-alerts-overview.md)。

> [!NOTE]
> 若要启用高级检测，请升级到 Azure 安全中心标准版。 有免费试用版可用。 要升级，请在[安全策略](tutorial-security-policy.md)中选择定价层。 请参阅 [Azure 安全中心定价](security-center-pricing.md)，了解详细信息。

## <a name="what-are-security-alerts"></a>什么是安全警报？
安全中心会自动收集、分析以及整合 Azure 资源、网络和所连合作伙伴解决方案（如，防火墙和终结点保护解决方案）的日志数据，检测真正的威胁并减少误报。 安全中心显示了一系列安全警报（按严重程度排序），并显示了快速调查问题所需的信息以及修复攻击的建议。

> [!NOTE]
> 有关安全中心检测功能工作方式的详细信息，请参阅[Azure 安全中心如何检测和响应威胁](security-center-alerts-overview.md#detect-threats)。

## <a name="manage-your-security-alerts"></a>管理安全警报

1. 在"安全中心"仪表板中，请参阅 **"威胁保护**"磁贴以查看警报并概述警报。

    ![安全中心的“安全警报”磁贴](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. 要查看有关警报的更多详细信息，请单击磁贴。

   ![安全中心的“安全警报”](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. 要筛选显示的警报，请单击 **"筛选**"，然后从打开的 **"筛选器"** 边栏选项卡中选择要应用的筛选器选项。 列表根据所选筛选器更新。 筛选非常有用。 例如，假设正在调查系统中的潜在危害，需要处理过去 24 小时内发生的安全警报。

    ![筛选安全中心的警报](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>响应安全警报

1. 从 **"安全警报"** 列表中，单击安全警报。 将显示所涉及的资源和修复攻击所需的步骤。

    ![响应安全警报](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. 查看信息后，单击受到攻击的资源。

    ![有关如何处理安全警报的建议](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    "**常规信息**"部分可以深入了解触发安全警报的原因。 它显示目标资源、源 IP 地址（如果适用）、警报仍处于活动状态以及有关如何修复的建议等信息。  

    > [!NOTE]
    >在某些情况下，源 IP 地址不可用，某些 Windows 安全事件日志不包含 IP 地址。

1. 安全中心建议的修正步骤因安全警报而异。 每个警报都跟随它们。 

    在某些情况下，为了缓解安全警报，您可能需要使用其他 Azure 控件或服务来实现建议的修正。 

## <a name="see-also"></a>请参阅

本文档中已经介绍了如何在安全中心配置安全策略。 若要了解有关安全中心的详细信息，请参阅以下文章：

* [Azure 安全中心中的安全警报](security-center-alerts-overview.md)。
* [处理安全事件](security-center-incident.md)