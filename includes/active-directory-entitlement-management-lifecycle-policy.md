---
title: 包含文件
description: 包含文件
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: e971af5d415ea788f97ab11b58541e629c7eb0ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "76694422"
---
## <a name="lifecycle"></a>生命周期

在“生命周期”选项卡上，指定用户的访问包分配何时过期。 还可以指定是否允许用户将其分配延期。

1. 在“过期时间”部分，将“访问包分配过期时间”设置为“日期”、“天数”或“永不”。    

    对于“日期”，请选择将来的过期日期。****

    对于“天数”，请指定 0 到 3660 天的数字。****

    根据所做的选择，用户的访问包分配将在特定的日期过期、审批后经过特定的天数之后过期，或者永不过期。

1. 单击“显示高级过期时间设置”以显示其他设置。****

    ![访问包 - 生命周期过期时间设置](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. 若要允许用户延期其分配，请将“允许用户延期访问权限”设置为“是”。********

    如果策略中允许延期，在将用户的访问包分配设置为过期之前的 14 天以及 1 天，用户会收到一封电子邮件，其中提示他们是否要延期分配。 如果用户提交将访问权限延期的请求，则延期日期不得迟于分配过期日期，如用于向用户授予访问包访问权限的策略中定义的那样。 例如，如果策略指示分配设置为在 6 月 30 日过期，则用户最多可以请求延期到 6 月 30 日。

    如果用户的访问延期，则用户将无法在指定的延期日期（在创建了策略的用户的时区中设置的日期）之后请求访问包。

1. 如果要求在获批后才能延迟，则请将“要求获批才能延期”设置为“是”。 

    将使用已在“请求”选项卡上指定的审批设置。

1. 单击“下一步”或“更新”。 
