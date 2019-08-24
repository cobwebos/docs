---
title: Azure 安全中心中的安全警报 |Microsoft Docs
description: 本主题说明 Azure 安全中心提供的安全警报和不同类型。
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
ms.date: 8/20/2019
ms.author: v-mohabe
ms.openlocfilehash: d905096432c6e05e5386631480e5cc45b5c890c5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013296"
---
# <a name="security-alerts-in-azure-security-center"></a>Azure 安全中心的安全警报

在 Azure 安全中心, 许多不同的资源类型都有多种不同的警报。 安全中心为部署在 Azure 上的资源以及部署在本地和混合云环境中的资源生成警报。 

## <a name="what-are-security-alerts"></a>什么是安全警报？

警报是安全中心检测到对资源的威胁时生成的通知。 它划分优先级并列出警报, 以及快速调查问题所需的信息。 安全中心还提供了有关如何修正攻击的建议。

## <a name="how-does-security-center-detect-threats"></a>安全中心如何检测威胁？

为了检测真正的威胁并减少误报, 安全中心从 Azure 资源和网络收集、分析和集成日志数据。 它还适用于连接的合作伙伴解决方案, 如防火墙和 endpoint protection 解决方案。 安全中心会分析此信息, 通常会将来自多个来源的信息关联起来来识别威胁。

安全中心监视其可能的任何环境中的资源。 若要了解有关检测和响应威胁的详细信息, 请参阅[安全中心如何检测和响应威胁](security-center-detection-capabilities.md#asc-detects)。

## <a name="security-alert-types"></a>安全警报类型

以下主题将指导你根据资源类型来完成不同的警报:

* [IaaS Vm 和服务器警报](security-center-alerts-iaas.md)
* [本机计算警报](security-center-alerts-compute.md)
* [数据服务警报](security-center-alerts-data-services.md)

以下主题介绍安全中心如何使用它从与 Azure 基础结构集成所收集的不同遥测, 以便为 Azure 上部署的资源应用其他保护层:

* [服务层警报](security-center-alerts-service-layer.md)
* [与 Azure 安全产品集成](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>什么是安全事件？

安全事件是相关警报的集合, 而不是单独列出每个警报。 安全中心使用[云智能警报相关](security-center-alerts-cloud-smart.md), 将不同的警报和低保真信号关联到安全事件。

通过使用事件, 安全中心可提供攻击活动的单一视图和所有相关警报。 利用此视图, 您可以快速了解攻击者采取的操作以及受影响的资源。 有关详细信息, 请参阅[云智能警报相关性](security-center-alerts-cloud-smart.md)。

## <a name="get-started-with-alerts"></a>警报入门

以下文章可帮助你了解有关安全中心监视的资源的详细信息, 并提供有关如何响应显示的警报的指南。

* [Platforms and features supported by Azure Security Center](security-center-os-coverage.md)（Azure 安全中心支持的平台和功能）  
* [如何处理 Azure 安全中心的安全事件](security-center-incident.md) 
* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Azure 安全中心内的警报验证](security-center-alert-validation.md)  


## <a name="upgrade-to-standard-for-advanced-detections"></a>升级到标准版进行高级检测

若要设置高级检测，请升级到 Azure 安全中心标准版。 

1. 在 "**安全中心**" 菜单中, 选择 "**安全策略**"。
2. 对于想要移动到标准层的订阅, 请选择 "**编辑设置**"。 
3. 从 "设置" 页上, 选择 "**定价层**"。 
   一个月内提供了免费试用版。 若要了解详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/security-center/)。 

## <a name="next-steps"></a>后续步骤

本文介绍了安全中心提供的不同类型的警报。 有关详细信息，请参阅：

* [Azure 安全中心规划和操作指南](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure 安全中心常见问题](https://docs.microsoft.com/azure/security-center/security-center-faq)

