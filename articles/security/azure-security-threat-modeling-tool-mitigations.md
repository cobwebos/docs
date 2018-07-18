---
title: 风险缓解 - Microsoft 威胁建模工具 - Azure | Microsoft 文档
description: Microsoft 威胁建模工具的风险缓解页，重点介绍了针对最常见的已发生威胁的可能解决方案。
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 07ef1fd3d81d795c9164741d22b5a689f86bd720
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23043432"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Microsoft 威胁建模工具风险缓解

威胁建模工具是 Microsoft 安全开发生命周期 (SDL) 的核心要素。 当潜在安全问题处于无需花费过多成本即可相对容易解决的阶段，软件架构师可以使用威胁建模工具提前识别这些问题。 因此，它能大幅减少开发总成本。 此外，我们设计该工具时考虑到了非安全专家的体验，为他们提供有关创建和分析威胁模型的清晰指导，让所有开发人员都可以更轻松地使用威胁建模。

请访问**[威胁建模工具](./azure-security-threat-modeling-tool.md)** 以立即开始！

## <a name="mitigation-categories"></a>风险缓解类别

威胁建模工具缓解措施根据 Web 应用程序安全框架分类，包括：

| 类别 | 说明 |
| -------- | ----------- |
| **[审核和日志记录](./azure-security-threat-modeling-tool-auditing-and-logging.md)** | 谁在何时做了什么？ 审核与日志记录是指应用程序如何记录安全相关的事件 |
| **[身份验证](./azure-security-threat-modeling-tool-authentication.md)** | 你是谁？ 身份验证是某个实体证明另一实体的身份的过程，这通常是通过用户名和密码等凭据完成的。 |
| **[授权](./azure-security-threat-modeling-tool-authorization.md)** | 该怎么办？ 授权是指应用程序如何提供对资源和操作的访问控制 |
| **[通信安全](./azure-security-threat-modeling-tool-communication-security.md)** | 在与谁对话？ 通信安全可确保以尽量安全的方式进行所有通信 |
| **[配置管理](./azure-security-threat-modeling-tool-configuration-management.md)** | 应用程序的运行身份是什么？ 它连接到哪些数据库？ 如何管理应用程序？ 如何保护这些设置？ 配置管理是指应用程序如何处理这些操作问题 |
| **[加密](./azure-security-threat-modeling-tool-cryptography.md)** | 如何保守机密（保密性）？ 如何防止对数据或库（完整性）进行篡改？ 如何针对必须强加密的随机值提供种子？ 加密是指应用程序强制实施保密性和完整性 |
| **[异常管理](./azure-security-threat-modeling-tool-exception-management.md)** | 当应用程序中的方法调用失败时，应用程序会采取什么措施？ 透露的信息量有多大？ 是否向最终用户返回友好的错误信息？ 是否向调用方传回有用的异常信息？ 应用程序是否正常失败？ |
| **[输入验证](./azure-security-threat-modeling-tool-input-validation.md)** | 如何知道应用程序接收的输入有效且安全？ 输入验证是指应用程序在进一步处理之前筛选、清理或拒绝输入。 请考虑通过入口点限制输入，通过出口点为输出编码。 是否信任数据库和文件共享等源中的数据？ |
| **[敏感数据](./azure-security-threat-modeling-tool-sensitive-data.md)** | 应用程序如何处理敏感数据？ 敏感数据是指应用程序如何处理必须在内存中、通过网络或在持久性存储中保护的任何数据 |
| **[会话管理](./azure-security-threat-modeling-tool-session-management.md)** | 应用程序如何处理和保护用户会话？ 会话是指用户与 Web 应用程序之间的一系列相关交互 |

它可以帮助识别：

* 最常见的错误发生在哪个位置
* 可在哪个位置实施可行性最大的改进

因此，可以使用这些类别来重点完成并优化安全工作，以便在输入验证、身份验证和授权类别中出现最流行的安全问题时，可以从这些位置着手。 有关详情，请访问[此专利链接](https://www.google.com/patents/US7818788)

## <a name="next-steps"></a>后续步骤

请访问[威胁建模工具威胁](./azure-security-threat-modeling-tool-threats.md)，以详细了解有关该工具用于生成可能的设计威胁的威胁类别。