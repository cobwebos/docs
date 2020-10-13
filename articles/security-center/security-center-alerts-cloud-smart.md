---
title: Azure 安全中心事件 - 警报的智能关联
description: 本主题介绍 Fusion 如何使用云智能警报关联在 Azure 安全中心中生成安全事件。
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
ms.openlocfilehash: 742d7122c60b383e8ab7aa73f73b11b47843a9d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613810"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Azure 安全中心中的云智能警报关联（事件）

Azure 安全中心使用高级分析和威胁情报来持续分析混合云工作负载，在存在恶意活动时发出警报。

威胁的范围正在不断扩大。 检测哪怕最微小的攻击的需求也是很重要的，而安全分析人员对不同的警报进行会审并识别实际攻击可能非常具有挑战性。 安全中心可以帮助分析人员处理这些疲于应付的警报。 通过将不同的警报和低保真度信号关联到安全事件中，它有助于诊断发生的攻击。

Fusion 分析是为安全中心事件提供支持的技术和分析后端，它使安全中心能够将不同的警报和上下文信号关联在一起。 Fusion 查看跨资源订阅上报告的不同信号。 Fusion 查找具有共享上下文信息的攻击进度或信号的模式，指示你应该对它们使用统一的响应过程。

Fusion 分析将安全域知识与 AI 相结合，用于分析警报，发现新的攻击模式。 

安全中心利用 MITRE 攻击矩阵将警报与其感知意图相关联，有助于形成规范化的安全域知识。 此外，通过使用为攻击的每个步骤收集的信息，安全中心可以排除看似是攻击步骤但实际上不是的活动。

由于攻击通常发生在不同的租户之间，安全中心可以结合 AI 算法来分析每个订阅上报告的攻击序列。 此技术将攻击序列标识为常见的警报模式，而不是只是偶然地相互关联。

在调查事件期间，分析员经常需要额外的上下文，以便得出有关威胁的性质以及如何缓解威胁的裁定。 例如，即使检测到网络异常，但不了解网络上发生的其他情况或者目标资源相关情况，很难知道接下来要采取什么操作。 为了提供帮助，安全事件可以包括工件、相关事件和信息。 可用于安全事件的其他信息因检测到的威胁类型和环境配置而异。 

> [!TIP]
> 有关可通过合成分析生成的安全事件警报的列表，请参阅 [警报的引用表](alerts-reference.md#alerts-fusion)。

:::image type="content" source="./media/security-center-alerts-cloud-smart/security-incident.png" alt-text="检测到的安全事件的屏幕截图报表":::


若要更好地了解安全事件，请参阅 [如何在 Azure 安全中心管理安全事件](security-center-incident.md)。

