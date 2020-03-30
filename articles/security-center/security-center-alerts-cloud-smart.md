---
title: Azure 安全中心事件 - 警报的智能关联
description: 本主题介绍融合如何使用云智能警报关联在 Azure 安全中心生成安全事件。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: b26f0bab073ce248ca23bb8a815fa3e293ddba51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73686488"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Azure 安全中心中的云智能警报关联（事件）

Azure 安全中心使用高级分析和威胁情报不断分析混合云工作负载，以提醒您有关恶意活动。

威胁覆盖的广度正在扩大。 需要检测哪怕是丝毫的妥协也非常重要，安全分析人员对不同的警报进行会审并识别实际攻击可能具有挑战性。 安全中心可帮助分析师应对这种警报疲劳。 通过将不同的警报和低保真度信号关联到安全事件中，它有助于诊断攻击发生时的攻击。

Fusion 分析是为安全中心事件提供支持的技术和分析后端，使其能够将不同的警报和上下文信号关联在一起。 Fusion 查看跨资源订阅上报告的不同信号。 Fusion 发现具有共享上下文信息的攻击进度或信号的模式，指示您应该对它们使用统一的响应过程。

Fusion 分析将安全域知识与 AI 相结合，以分析警报，发现新的攻击模式。 

安全中心利用 MITRE 攻击矩阵将警报与其感知意图相关联，帮助正式化安全域知识。 此外，通过使用为攻击的每个步骤收集的信息，安全中心可以排除看似攻击步骤但实际上不是的活动。

由于攻击通常发生在不同的租户之间，因此安全中心可以组合 AI 算法来分析每个订阅上报告的攻击序列。 此技术将攻击序列标识为流行的警报模式，而不是只是偶然地相互关联。

在调查事件期间，分析人员通常需要额外的上下文才能就威胁的性质以及如何减轻威胁做出判断。 例如，即使检测到网络异常，也不了解网络上或目标资源上还发生了什么，也很难理解接下来要执行哪些操作。 为了提供帮助，安全事件可以包括工件、相关事件和信息。 可用于安全事件的其他信息因检测到的威胁类型和环境配置而异。 

![检测到的安全事件报告的屏幕截图](./media/security-center-alerts-cloud-smart/security-incident.png)

要更好地了解安全事件，请参阅[如何在 Azure 安全中心中处理安全事件](https://docs.microsoft.com/azure/security-center/security-center-incident)。

