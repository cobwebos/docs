---
title: "Microsoft 威胁建模工具 - Azure | Microsoft 文档"
description: "Microsoft 威胁建模工具的主页，包含针对最常见的已发生威胁的缓解措施"
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
ms.date: 02/16/2017
ms.author: rodsan
translationtype: Human Translation
ms.sourcegitcommit: 2ad59f9b463671e2b74708871a9a9faae9bd24eb
ms.openlocfilehash: b75de472508bbf44c6e02e0d751054f37b0e1b8c
ms.lasthandoff: 02/21/2017


---

# <a name="microsoft-threat-modeling-tool"></a>Microsoft 威胁建模工具 
威胁建模工具是 Microsoft 安全开发生命周期 (SDL) 的核心要素。 当潜在安全问题处于无需花费过多成本即可相对容易解决的阶段，软件架构师可以使用威胁建模工具提前识别这些问题。 因此，它能大幅减少开发总成本。 此外，我们设计该工具时考虑到了非安全专家的体验，为他们提供有关创建和分析威胁模型的清晰指导，让所有开发人员都可以更轻松地使用威胁建模。 

任何人都可以使用该工具来实现以下目的：
* 交流系统的安全设计
* 使用经过证实的方法分析这些设计是否存在潜在安全问题
* 建议和管理针对安全问题的缓解措施

下面只是该工具的一部分功能和创新：
* **自动化：**有关绘制模型的指导和反馈
* **STRIDE per Element：**引导式威胁分析和缓解措施
* **报告：**安全活动与验证阶段的测试
* **独特的方法：**使用户能够更好地可视化和了解威胁
* **专为开发人员设计，以软件为中心：**许多方法是以资产或攻击者为中心。 我们是以软件为中心。 我们的解决方案构建在所有软件开发人员和架构师都很熟悉的活动基础之上 - 例如，为软件体系结构绘图
* **注重设计分析：**术语“威胁建模”可以表示某项要求或设计分析技术。 有时，它指的是两者的复杂混合形式。 Microsoft SDL 的威胁建模方法是一种有重点的设计分析技术

## <a name="threats"></a>威胁

威胁建模工具可帮助解答某些问题，例如：

* 攻击者如何更改身份验证数据？
* 如果攻击者可以读取用户配置文件数据，将造成哪种影响？
* 如果拒绝访问用户配置文件数据库，将发生什么情况？

为了更好地阐明此类突出问题，Microsoft 使用了 STRIDE 模型，它可以将不同类型的威胁分类，简化整体安全交流。

| 类别 | 说明 |
| -------- | ----------- |
| 欺骗 | 先进行非法访问，然后使用另一用户的身份验证信息，例如用户名和密码 |
| 篡改 | 恶意修改数据。 示例包括未经授权更改持久保存的数据（例如保存在数据库中的数据），更改通过开放网络（例如 Internet）在两台计算机之间传输的数据 |
| 否认性 | 指用户拒绝执行某个操作，但其他操作方无法证实这种拒绝无效 - 例如，某个用户在无法跟踪受禁操作的系统中执行非法操作。 不可否认性是指系统针对否认性威胁造成对策的能力。 例如，购买某个产品的用户可能需要在收货时签收该产品。 然后，供应商可以使用签收单来证明该用户确实收到了包裹 |
| 信息泄露 | 将信息透露给本应不该有权访问这些信息的个人 — 例如，用户能够读取他们未授权访问的文件，或者入侵者能够读取在两台计算机之间传输的数据 |
| 拒绝服务 | 拒绝服务 (DoS) 攻击会拒绝向有效用户提供服务 — 例如，使 Web 服务器暂时不可用。 必须防范某些类型的 DoS 威胁，这只是为了提高系统的可用性和可靠性 |
| 特权提升 | 无特权用户获得特权访问权限，从而拥有足够的访问权限来入侵或破坏整个系统。 特权提升威胁包括攻击者有效突破系统防御，成为受信任系统本身的一部分，这是非常危险的局面 |

## <a name="mitigations"></a>缓解措施

威胁建模工具缓解措施根据 Web 应用程序安全框架分类，包括：

| 类别 | 说明 |
| -------- | ----------- |
| [审核与日志记录](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-auditing-and-logging) | 谁在何时做了什么？ 审核与日志记录是指应用程序如何记录安全相关的事件 |
| [身份验证](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-authentication) | 你是谁？ 身份验证是某个实体证明另一实体的身份的过程，这通常是通过用户名和密码等凭据完成的。 |
| [授权](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-authorization) | 你该怎么办？ 授权是指应用程序如何提供对资源和操作的访问控制 | 
| [通信安全](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-communication-security) | 你在与谁对话？ 通信安全可确保以尽量安全的方式进行所有通信 |
| [配置管理](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-configuration-management) | 应用程序的运行身份是什么？ 它连接到哪些数据库？ 如何管理应用程序？ 如何保护这些设置？ 配置管理是指应用程序如何处理这些操作问题 | 
| [加密](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-cryptography) | 如何保守机密（保密性）？ 如何防止对数据或库（完整性）进行篡改？ 如何针对必须强加密的随机值提供种子？ 加密是指应用程序强制实施保密性和完整性 | 
| [异常管理](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-exception-management) | 当应用程序中的方法调用失败时，应用程序会采取什么措施？ 透露的信息量有多大？ 是否向最终用户返回友好的错误信息？ 是否向调用方传回有用的异常信息？ 应用程序是否正常失败？ |
| [输入验证](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-input-validation) | 如何知道应用程序接收的输入有效且安全？ 输入验证是指应用程序在进一步处理之前筛选、清理或拒绝输入。 请考虑通过入口点限制输入，通过出口点为输出编码。 是否信任数据库和文件共享等源中的数据？ |
| [敏感数据](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-sensitive-data) | 应用程序如何处理敏感数据？ 敏感数据是指应用程序如何处理必须在内存中、通过网络或在持久性存储中保护的任何数据 | 
| [会话管理](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-session-management) | 应用程序如何处理和保护用户会话？ 会话是指用户与 Web 应用程序之间的一系列相关交互 | 

它可以帮助识别： 

* 最常见的错误发生在哪个位置
* 可在哪个位置实施可行性最大的改进

因此，可以使用这些类别来重点完成并优化安全工作，以便在输入验证、身份验证和授权类别中出现最流行的安全问题时，可以从这些位置着手。 有关详细信息，请单击[此处](https://www.google.com/patents/US7818788)

## <a name="next-steps"></a>后续步骤
查看以下有用链接，帮助自己尽快入门：
* [下载链接](https://www.microsoft.com/download/details.aspx?id=49168)
* [入门](https://msdn.microsoft.com/magazine/dd347831.aspx)
* [核心培训](https://www.microsoft.com/download/details.aspx?id=16420)

在该页面中，可以学习一些威胁建模工具专家提供的作品：
* [威胁管理器](https://simoneonsecurity.com/threatsmanagersetup-v1-5-10/)
* [Simone Curzi 安全博客](https://simoneonsecurity.com/)
