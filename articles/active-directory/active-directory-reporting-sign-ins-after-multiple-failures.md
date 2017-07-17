---
title: "多次失败后的登录"
description: "指示在多次连续登录尝试失败后成功登录的用户的报表。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: e4ec1a39-9c20-418f-8a75-6497d0117176
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e55e0145adbdb1f41a8b8753d5555f20e96bf161
ms.contentlocale: zh-cn
ms.lasthandoff: 11/17/2016

---
# 多次失败后登录
<a id="sign-ins-after-multiple-failures" class="xliff"></a>
此报告指示在多次连续登录尝试失败后成功登录的用户。 可能的原因包括：

* 用户忘记了自己的密码</li><li>用户是成功密码猜测暴力攻击的受害者

来自此报告的结果将显示在成功登录前连续登录尝试失败次数和与第一次成功登录关联的时间戳。

**报告设置：**你可以配置在报告中显示连续登录尝试失败次数之前必须至少发生的连续登录尝试失败次数。 对此设置进行更改时，必须注意这些更改不会应用于当前报告中显示的任何现有失败登录。 但是，这些更改将应用于所有将来的登录。 对此报告的更改只能由经过许可的管理员进行。

![多次失败后的登录](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)


