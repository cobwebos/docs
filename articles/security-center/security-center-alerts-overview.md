---
title: Azure 安全中心的安全警报 |Microsoft Docs
description: 本主题说明什么是安全警报和 Azure 安全中心中可用的不同类型。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 06f41bbfd97d5deb59e7bfd08615b2f28e256070
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571668"
---
# <a name="security-alerts-in-azure-security-center"></a>Azure 安全中心的安全警报

本文介绍在 Azure 安全中心 (ASC) 提供的安全警报的不同类型。 有许多的许多不同的资源类型的警报。 ASC 生成部署到 Azure，这两个资源的警报和云环境的本地和混合版上部署资源。 

## <a name="what-are-security-alerts"></a>什么是安全警报？

警报是时检测到你的资源上的威胁，安全中心将生成的通知。 它优先处理，并列出警报以及您可以快速调查问题所需的信息。 安全中心还提供了有关如何修正攻击的建议。

## <a name="how-does-security-center-detect-threats"></a>安全中心如何检测威胁？

若要检测真正的威胁并减少误报，安全中心收集、 分析并整合日志数据从 Azure 资源、 网络和连接的合作伙伴解决方案，如防火墙和终结点保护解决方案。 安全中心将分析此信息通常关联来自多个源，用来识别威胁的信息。

ASC 监视资源是否在 Azure 上部署或部署其他本地和混合云环境。 若要了解有关检测和响应威胁的详细信息，请参阅[如何安全中心检测和应对威胁](security-center-detection-capabilities.md#asc-detects)。

## <a name="security-alert-types"></a>安全警报类型

以下主题将指导您完成根据资源类型的不同 ASC 警报：

* [IaaS Vm 和服务器警报](security-center-alerts-iaas.md)
* [本机计算警报](security-center-alerts-compute.md)
* [数据服务警报](security-center-alerts-data-services.md)

以下主题说明了安全中心如何利用它从与 Azure 基础结构集成以便应用在 Azure 上部署的资源的附加保护层收集的不同遥测数据：

* [服务层警报](security-center-alerts-service-layer.md)
* [与 Azure 安全产品的集成](security-center-alerts-integration.md)

## <a name="what-are-alert-incidents"></a>警报事件有哪些？

安全事件是相关的警报，而不是分别列出每个警报的集合。 安全中心使用合成用于关联到安全事件的不同的警报和低保真信号。

使用事件，安全中心将提供攻击活动及其所有相关的警报的单一视图。 此视图，可快速了解攻击者所采取的操作以及哪些资源受到了影响。 有关详细信息，请参阅[云智能警报关联](security-center-alerts-cloud-smart.md)。

## <a name="get-started-with-alerts"></a>开始使用警报

请参阅以下主题，以更深入地了解监视 asc 情况下，和有关如何响应主讲人 ASC 警报的指导原则的资源。

* 若要查看哪些平台和功能受 ASC，请参阅[平台和支持的 Azure 安全中心功能](security-center-os-coverage.md)。  
* 若要了解什么是安全事件和 ASC 如何对它们进行响应，请参阅[如何处理 Azure 安全中心中的安全事件](security-center-incident.md)。 
* 若要了解如何管理你接收的警报，请参阅[管理和响应 Azure 安全中心的安全警报的](security-center-managing-and-responding-alerts.md)。
* 有关如何信息验证是否已正确配置安全中心和发展测试警报，请参阅[Azure 安全中心中的警报验证](security-center-alert-validation.md)。  


## <a name="upgrade-to-standard-for-advanced-detections"></a>升级到标准层的高级检测

若要设置高级检测，请升级到 Azure 安全中心标准版。 

1. 从安全中心菜单中，选择**安全策略**。
2. 你想要移到标准层的订阅，请单击**编辑设置**。 
3. 从设置页中，选择**定价层**。 
   一个月免费试用版不可用。 若要了解详细，请参阅[定价页](https://azure.microsoft.com/pricing/details/security-center/)。 

## <a name="next-steps"></a>后续步骤

在本文中，已了解什么是安全警报和不同类型的安全中心中可用的通知。 有关详细信息，请参阅下列主题：

* [Azure 安全中心规划和操作指南](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure 安全中心常见问题解答](https://docs.microsoft.com/azure/security-center/security-center-faq)：查找有关如何使用服务的常见问题。

