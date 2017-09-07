---
title: "威胁 - Microsoft 威胁建模工具 - Azure | Microsoft 文档"
description: "Microsoft 威胁建模工具的威胁类别页，包含所有常见的已发生威胁的类别。"
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
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 704f9995828866d4d2e4969e3aa922ed1e23c4ea
ms.contentlocale: zh-cn
ms.lasthandoff: 08/28/2017

---

# <a name="microsoft-threat-modeling-tool-threats"></a>Microsoft 威胁建模工具威胁

威胁建模工具是 Microsoft 安全开发生命周期 (SDL) 的核心要素。 当潜在安全问题处于无需花费过多成本即可相对容易解决的阶段，软件架构师可以使用威胁建模工具提前识别这些问题。 因此，它能大幅减少开发总成本。 此外，我们设计该工具时考虑到了非安全专家的体验，为他们提供有关创建和分析威胁模型的清晰指导，让所有开发人员都可以更轻松地使用威胁建模。

> 请访问**[威胁建模工具](./azure-security-threat-modeling-tool.md)**以立即开始！

威胁建模工具可帮助解答某些问题，例如：

* 攻击者如何更改身份验证数据？
* 如果攻击者可以读取用户配置文件数据，将造成哪种影响？
* 如果拒绝访问用户配置文件数据库，将发生什么情况？

## <a name="stride-model"></a>STRIDE 模型

为了更好地阐明此类突出问题，Microsoft 使用了 STRIDE 模型，它可以将不同类型的威胁分类，简化整体安全交流。

| 类别 | 说明 |
| -------- | ----------- |
| **欺骗** | 先进行非法访问，并使用另一用户的身份验证信息，例如用户名和密码 |
| **篡改** | 恶意修改数据。 示例包括未经授权更改持久保存的数据（例如保存在数据库中的数据），更改通过开放网络（例如 Internet）在两台计算机之间传输的数据 |
| **否认性** | 指用户拒绝执行某个操作，但其他操作方无法证实这种拒绝无效 - 例如，某个用户在无法跟踪受禁操作的系统中执行非法操作。 不可否认性是指系统对抗否认性威胁的能力。 例如，购买某个产品的用户可能需要在收货时签收该产品。 然后，供应商可以使用签收单来证明该用户确实收到了包裹 |
| **信息泄露** | 将信息透露给本应不该有权访问这些信息的个人 — 例如，用户能够读取他们未授权访问的文件，或者入侵者能够读取在两台计算机之间传输的数据 |
| **拒绝服务** | 拒绝服务 (DoS) 攻击会拒绝向有效用户提供服务 — 例如，使 Web 服务器暂时不可用。 必须防范某些类型的 DoS 威胁，这只是为了提高系统的可用性和可靠性 |
| **特权提升** | 无特权用户获得特权访问权限，从而拥有足够的访问权限来入侵或破坏整个系统。 特权提升威胁包括攻击者有效突破系统防御，成为受信任系统本身的一部分，这是非常危险的局面 |

## <a name="next-steps"></a>后续步骤

继续执行**[威胁建模工具缓解措施](./azure-security-threat-modeling-tool-mitigations.md)**，以了解使用 Azure 来缓解这些威胁可采取的不同方式。
