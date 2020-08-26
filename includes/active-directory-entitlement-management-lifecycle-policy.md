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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "76694422"
---
## <a name="lifecycle"></a>生命周期

在 " **生命周期** " 选项卡上，指定用户对访问包的分配何时过期。 你还可以指定用户是否可以扩展其分配。

1. 在 " **过期** " 部分中，将 " **访问包分配截止** 时间" 设置为 **"过期时间**"、" **天数**" 或 " **从不**"。

    对于“日期”，请选择将来的过期日期。****

    对于“天数”，请指定 0 到 3660 天的数字。****

    根据所做的选择，用户的访问包分配将在特定的日期过期、审批后经过特定的天数之后过期，或者永不过期。

1. 单击“显示高级过期时间设置”以显示其他设置。****

    ![访问包-生命周期过期设置](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. 若要允许用户延期其分配，请将“允许用户延期访问权限”设置为“是”。********

    如果策略中允许延期，在将用户的访问包分配设置为过期之前的 14 天以及 1 天，用户会收到一封电子邮件，其中提示他们是否要延期分配。 如果用户提交一个扩展访问请求，则该扩展日期必须在分配时过期，如用于向用户授予访问包访问权限的策略中定义的那样。 例如，如果策略指示分配设置为在6月30日过期，则用户可以请求的最大扩展为6月30日。

    如果扩展用户的访问权限，则在创建策略) 的用户的时区中，他们将不能请求访问包 (日期设置。

1. 若要要求批准授予扩展，请设置 "需要批准才能向 Yes **授予扩展** **"**。

    将使用在 " **请求** " 选项卡上指定的相同的审批设置。

1. 单击 " **下一步** " 或 " **更新**"。
