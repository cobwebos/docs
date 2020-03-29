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
ms.openlocfilehash: e971af5d415ea788f97ab11b58541e629c7eb0ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "76694422"
---
## <a name="lifecycle"></a>生命周期

在 **"生命周期"** 选项卡上，您可以指定用户对访问包的分配何时过期。 您还可以指定用户是否可以扩展其分配。

1. 在 **"过期"** 部分中 **，"访问包分配"将过期**为 **"打开日期**"、**天数**或 **"从不**"。

    对于“日期”，请选择将来的过期日期。****

    对于“天数”，请指定 0 到 3660 天的数字。****

    根据所做的选择，用户的访问包分配将在特定的日期过期、审批后经过特定的天数之后过期，或者永不过期。

1. 单击“显示高级过期时间设置”以显示其他设置。****

    ![访问包 - 生命周期过期设置](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. 若要允许用户延期其分配，请将“允许用户延期访问权限”设置为“是”。********

    如果策略中允许延期，在将用户的访问包分配设置为过期之前的 14 天以及 1 天，用户会收到一封电子邮件，其中提示他们是否要延期分配。 如果用户提交扩展访问权限的请求，则扩展日期必须位于或之前分配过期，如用于授予用户访问包的策略中定义的。 例如，如果策略指示分配设置为在 6 月 30 日过期，则用户可以请求的最大扩展是 6 月 30 日。

    如果用户的访问权限被扩展，他们将无法在指定的扩展日期（在创建策略的用户的时区中设置的日期）之后请求访问包。

1. 要需要批准才能授予扩展，需要**批准才能授予****"是**"。

    将使用"**请求"** 选项卡上指定的相同审批设置。

1. 单击 **"下一步**"或 **"更新**"。
