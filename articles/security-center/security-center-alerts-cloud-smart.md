---
title: 云 Azure 安全中心 （事件） 中的智能警报关联 |Microsoft Docs
description: 本主题说明如何在 Azure 安全中心是合成使用云智能警报关联来生成安全事件。
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
ms.author: monhaber
ms.openlocfilehash: 2ab4dab8cb7729b0c2ca023f22066f7b5d166a02
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571772"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>在 Azure 安全中心 （事件） 的云智能警报关联

安全中心会持续分析混合云工作负荷使用高级的分析和威胁智能来提醒用户恶意活动。

随着广泛的威胁不断扩大和需求来检测甚至微小的指示泄露会增加，可能会比较困难安全分析人员确定级别不同的警报，并识别实际攻击。 安全中心可帮助分析人员应对警报疲劳和诊断攻击，发生时，通过将不同的警报和低保真信号到安全事件相关联。

合成是技术和分析后端为提供支持安全中心的事件，使其能够关联不同的警报和上下文信号组合在一起。 熔通过查看不同的信号的工作原理上订阅报告资源，并查找显示攻击进度或发出信号表示一个统一的响应过程的共享上下文信息的流行模式应为为其创建。

合成分析组合使用 AI 分析警报，发现新的攻击模式，在发生安全域知识。 

安全中心将利用 MITRE 攻击矩阵能够感知有意相关联的警报帮助安全域知识来形式化。 此外，通过使用每个步骤的攻击中收集的信息，安全中心可以出活动看起来好像是步骤的攻击，但不是规则。  

因为攻击通常会发生跨不同的租户，安全中心可以组合 AI 算法来分析对每个订阅报告它们标识为普遍警报模式而不是要顺便说一下关联与每个攻击序列其他。

在事故的调查，分析师通常需要额外的上下文来访问有关的性质威胁以及如何缓解此问题并且作出判定。 例如，即使检测到网络异常，则不了解什么发生在网络上或目标资源相关情况下很难了解接下来要采取的操作。 若要帮助，安全事件可以包括项目、 相关的事件和信息。 根据检测到的威胁类型和您的环境的配置，可用于安全事件的其他信息会有所不同。 

![安全事件的详细信息](./media/security-center-alerts-cloud-smart/security-incident.png)

若要更好地了解安全事件，请参阅[如何处理 Azure 安全中心中的安全事件](https://docs.microsoft.com/azure/security-center/security-center-incident)。

