---
title: Azure 安全中心中的云智能警报关联 (事件) |Microsoft Docs
description: 本主题介绍了如何使用云智能警报相关来生成安全事件 (在 Azure 安全中心中)。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 7ba2cf14d9fac100f44a1ef23997b27ba062bee0
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295866"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Azure 安全中心中的云智能警报关联 (事件)

安全中心使用高级分析和威胁智能来持续分析混合云工作负荷, 以提醒你有关恶意活动的信息。

随着威胁范围的广泛发展, 甚至需要检测到少许的增长, 这可能会使安全分析人员诊断不同警报并识别实际攻击, 这可能会很困难。 安全中心通过将不同的警报和低保真信号关联到安全事件, 帮助分析人员应对警报疲劳并诊断攻击。

合成是支持安全中心事件的技术和分析后端, 使其能够将不同的警报和上下文信号彼此关联起来。 合成工作方式: 查看跨资源报告的针对订阅的不同信号, 查找使用共享上下文信息显示攻击进度或信号的常见模式, 以指示统一的响应过程为其执行。

合成 analytics 结合了安全域知识和 AI, 分析警报, 发现新的攻击模式。 

安全中心利用 MITRE 攻击矩阵将警报与其感知意向关联起来, 从而帮助将安全域的知识正式化。 此外, 通过使用为攻击的每个步骤收集的信息, 安全中心可以将似乎是攻击的步骤的活动排除在外, 而不是。  

由于攻击通常发生在不同的租户中, 安全中心可以结合 AI 算法来分析每个订阅上报告的攻击顺序, 以将它们标识为常见警报模式, 而不是只是与每个订阅关联以外.

调查事件时, 分析人员通常需要额外的上下文来了解有关威胁性质和如何缓解威胁的结论。 例如, 即使在检测到网络异常的情况下也不了解网络上发生的其他情况, 或与目标资源有关的情况, 也很难理解接下来要执行的操作。 为了提供帮助, 安全事件可能包括项目、相关事件和信息。 可用于安全事件的其他信息取决于检测到的威胁类型以及环境的配置。 

![安全事件详细信息](./media/security-center-alerts-cloud-smart/security-incident.png)

若要更好地了解安全事件, 请参阅[如何处理 Azure 安全中心的安全事件](https://docs.microsoft.com/azure/security-center/security-center-incident)。

