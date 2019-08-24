---
title: Azure 安全中心中的云智能警报关联 (事件) |Microsoft Docs
description: 本主题介绍了合成如何使用云智能警报相关来生成 Azure 安全中心的安全事件。
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
ms.openlocfilehash: 5afba1ddf2d25e079d9f742f4e403dab6c0c0c4b
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013397"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Azure 安全中心中的云智能警报关联 (事件)

Azure 安全中心使用高级分析和威胁智能提醒你了解恶意活动, 从而持续分析混合云工作负荷。

威胁范围广泛增长。 检测到少许泄露的需要很重要, 安全分析师需要对不同警报进行会审并识别实际攻击, 这是一项挑战。 安全中心帮助分析人员应对此警报疲劳。 它通过将不同的警报和低保真信号关联到安全事件来帮助诊断攻击。

合成 analytics 是支持安全中心事件的技术和分析后端, 使其能够将不同的警报和上下文信号关联起来。 合成会查看跨资源报告的针对订阅的不同信号。 合成查找使用共享上下文信息显示攻击进度或信号的模式, 这表明应为它们使用统一的响应过程。

合成 analytics 结合了安全域知识和 AI, 分析警报, 发现新的攻击模式。 

安全中心利用 MITRE 攻击矩阵将警报与其感知意向关联起来, 从而帮助将安全域的知识正式化。 此外, 通过使用为攻击的每个步骤收集的信息, 安全中心可以排除似乎是攻击步骤的活动, 但实际上并非如此。  

由于攻击通常发生在不同租户之间, 安全中心可以结合 AI 算法来分析每个订阅上报告的攻击顺序。 此方法将攻击序列标识为常见警报模式, 而不是只是相互关联的。

调查事件时, 分析人员通常需要额外的上下文来了解有关威胁性质和如何缓解威胁的结论。 例如, 即使在检测到网络异常的情况下也不了解网络上所发生的其他情况, 或对于目标资源, 很难理解接下来要采取的操作。 为了提供帮助, 安全事件可能包括项目、相关事件和信息。 根据检测到的威胁类型和环境的配置, 适用于安全事件的其他信息会有所不同。 

![检测到的安全事件的屏幕截图报表](./media/security-center-alerts-cloud-smart/security-incident.png)

若要更好地了解安全事件, 请参阅[如何处理 Azure 安全中心的安全事件](https://docs.microsoft.com/azure/security-center/security-center-incident)。

