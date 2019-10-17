---
title: include 文件
description: include 文件
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 88e11adadcc06875964146ad2046828267258038
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389378"
---
## <a name="lifecycle"></a>生命周期

在 "**生命周期**" 选项卡上，指定用户对访问包的分配何时过期。 你还可以指定用户是否可以扩展其分配。

1. 在 "**过期**" 部分中，将 "**访问包分配截止**时间" 设置为 **"过期时间**"、"**天数**" 或 "**从不**"。

    对于 "**日期**"，选择将来的到期日期。

    对于 "**天**"，请指定一个介于0到3660天之间的数字。

    根据你的选择，对访问包的用户分配将在特定日期过期，在批准后特定天数过期，或从不过期。

1. 单击 "**显示高级过期设置**" 以显示其他设置。

    ![访问包-生命周期过期设置](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. 若要允许用户扩展其分配，请将 "**允许用户扩展" 权限**设置为 **"是"** 。

    如果策略中允许使用扩展，则用户将收到电子邮件14天，并且在访问包分配设置为过期之前的1天内，会提示他们扩展分配。

    如果用户的访问权限已扩展，则他们将无法在指定的扩展日期之后（在创建策略的用户的时区中设置日期）请求访问包。

1. 若要要求批准授予扩展，请设置 "需要批准才能向 Yes**授予扩展** **"** 。

    将使用在 "**请求**" 选项卡上指定的相同的审批设置。

1. 单击 "**下一步**" 或 "**更新**"。
