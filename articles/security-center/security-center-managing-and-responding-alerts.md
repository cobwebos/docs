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
ms.date: 08/27/2019
ms.author: memildin
ms.openlocfilehash: 834a097eb77cc7de2d0a3643bf1499ff7517ddb0
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912267"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>管理和响应 Azure 安全中心的安全警报

本主题说明如何查看和处理你收到的警报，以便保护你的资源。 

* 若要了解不同类型的警报，请参阅[安全警报类型](security-center-alerts-overview.md#security-alert-types)。
* 有关安全中心如何生成警报的概述，请参阅[Azure 安全中心如何检测和响应威胁](security-center-alerts-overview.md#detect-threats)。

> [!NOTE]
> 若要启用高级检测，请升级到 Azure 安全中心标准版。 有免费试用版可用。 要升级，请选择 [安全策略](tutorial-security-policy.md)中的“定价层”。 请参阅 [Azure 安全中心定价](security-center-pricing.md)，了解详细信息。

## <a name="what-are-security-alerts"></a>什么是安全警报？
安全中心会自动收集、分析以及整合 Azure 资源、网络和所连合作伙伴解决方案（如，防火墙和终结点保护解决方案）的日志数据，检测真正的威胁并减少误报。 安全中心显示了一系列安全警报（按严重程度排序），并显示了快速调查问题所需的信息以及修复攻击的建议。

> [!NOTE]
> 有关安全中心检测功能的工作原理的详细信息，请参阅[Azure 安全中心如何检测和响应威胁](security-center-alerts-overview.md#detect-threats)。

## <a name="manage-your-security-alerts"></a>管理安全警报

1. 在安全中心仪表板中，查看 "**威胁防护**" 磁贴以查看并概述警报。

    ![安全中心的“安全警报”磁贴](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. 若要查看有关警报的更多详细信息，请单击该磁贴。

   ![安全中心的“安全警报”](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. 要筛选显示的警报，请单击 "**筛选**器"，并从打开的 "**筛选器**" 边栏选项卡中选择要应用的筛选器选项。 列表会根据所选筛选器进行更新。 筛选可能非常有用。 例如，假设正在调查系统中的潜在危害，需要处理过去 24 小时内发生的安全警报。

    ![筛选安全中心的警报](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>响应安全警报

1. 在 "**安全警报**" 列表中，单击 "安全警报"。 其中显示了所涉及的资源以及修正攻击所需采取的步骤。

    ![响应安全警报](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. 查看信息后，单击被攻击的资源。

    ![有关如何处理安全警报的建议](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    "**常规信息**" 一节可以深入了解触发安全警报的内容。 如果警报仍处于活动状态，则它会显示目标资源、源 IP 地址（如果适用），以及有关如何修正的建议等信息。  

    > [!NOTE]
    >在某些情况下，源 IP 地址不可用，某些 Windows 安全事件日志不包含 IP 地址。

1. 安全中心建议的补救步骤根据安全警报而有所不同。 对于每个警报，请遵循它们。 

    在某些情况下，为了缓解安全警报，可能需要使用其他 Azure 控件或服务来实施建议的修正。 

    以下主题将指导你根据资源类型来完成不同的警报：
    
    * [IaaS Windows 计算机的警报](threat-protection.md#windows-machines)
    * [IaaS Linux 计算机的警报](threat-protection.md#linux-machines)
    * [Azure App Service 的警报](threat-protection.md#app-services)
    * [Azure 容器警报](threat-protection.md#azure-containers)
    * [SQL 数据库和 SQL 数据仓库的警报](threat-protection.md#data-sql)
    * [Azure 存储的警报](threat-protection.md#azure-storage)
    * [Cosmos DB 的警报](threat-protection.md#cosmos-db)

    以下主题介绍安全中心如何使用它从与 Azure 基础结构集成所收集的不同遥测，以便为 Azure 上部署的资源应用其他保护层：
    
    * [Azure 管理层警报（Azure 资源管理器）（预览版）](threat-protection.md#management-layer)
    * [Azure Key Vault 的警报（预览）](threat-protection.md#azure-keyvault)
    * [Azure 网络层警报](threat-protection.md#network-layer)
    * [来自其他服务的警报](threat-protection.md#alerts-other)    

## <a name="see-also"></a>另请参阅

本文档中已经介绍了如何在安全中心配置安全策略。 若要了解有关安全中心的详细信息，请参阅以下文章：

* [Azure 安全中心的安全警报](security-center-alerts-overview.md)。
* [处理安全事件](security-center-incident.md)
* [Azure 安全中心规划和操作指南](security-center-planning-and-operations-guide.md)