---
title: '& Insights 报告（预览版）的身份验证方法用法-Azure Active Directory'
description: 报告 Azure AD 自助服务密码重置和多重身份验证方法使用情况
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ddfea07989f52c463816318276fd5b6643cb2041
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255065"
---
# <a name="authentication-methods-usage--insights-preview"></a>身份验证方法用法 & insights （预览版）

使用情况 & insights 使你能够了解 Azure 多重身份验证和自助服务密码重置等功能的身份验证方法在你的组织中的工作方式。 此报告功能为您的组织提供了一种了解哪些方法正在注册以及如何使用它们的方法。

## <a name="permissions-and-licenses"></a>权限和许可证

以下角色可以访问使用情况和见解：

- 全局管理员角色
- 安全读取者
- 安全管理员
- 报告读者

访问使用情况和见解不需要额外的许可。 Azure 多重身份验证和自助密码重置（SSPR）许可信息可在[Azure Active Directory 定价站点](https://azure.microsoft.com/pricing/details/active-directory/)上找到。

## <a name="how-it-works"></a>工作原理

若要访问身份验证方法使用情况和见解：

1. 浏览到 [Azure 门户](https://portal.azure.com)。
1. 浏览到**Azure Active Directory**@no__t 的**密码重置** > **用法 & insights**。
1. 从**注册**或**使用情况**概述中，你可以选择打开预先筛选的报表以根据你的需要进行筛选。

![使用情况 & 见解概述](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

若要直接访问 insights &，请参阅[https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade)。 此链接将显示注册概述。

已注册的用户、启用的用户和支持用户的磁贴会显示用户的以下注册数据：

- 注册如果用户（或管理员）已注册了足够的身份验证方法来满足组织的 SSPR 或多重身份验证策略，则会将该用户视为已注册用户。
- 已启用:如果用户在 SSPR 策略范围内，则认为该用户处于启用状态。 如果为某个组启用了 SSPR，则如果用户在该组中，则认为该用户处于启用状态。 如果为所有用户启用了 SSPR，则租户中的所有用户（不包括来宾）都将被视为已启用。
- 能够如果用户已注册并处于启用状态，则会将该用户视为能够使用。 此状态意味着它们可以在需要时随时执行 SSPR。

单击其中的任何磁贴或其中显示的见解会将你带到预筛选的注册详细信息列表。

"**注册**" 选项卡上的 **"注册" 图表显示**通过身份验证方法进行的身份验证方法注册失败和失败的次数。 "**使用情况**" 选项卡上的 "**重置**图表" 会显示通过身份验证方法进行密码重置流时的身份验证成功和失败的次数。

单击其中一个图表将显示注册或重置事件的预筛选列表。

使用右上角的控件，可以更改注册中显示的审核数据的日期范围，并将图表重置为24小时、7天或30天。

### <a name="registration-details"></a>注册详细信息

单击注册的**用户**、**启用**的用户或**支持**的磁贴或见解的用户将会获得注册详细信息。

"注册详细信息" 报表显示每个用户的以下信息：

- 名称
- 用户名
- 注册状态（所有、已注册、未注册）
- 启用状态（"全部"、"已启用"、"未启用"）
- 支持的状态（全部、功能、不支持）
- 方法（应用通知、应用代码、电话呼叫、短信、电子邮件、安全问题）

使用列表顶部的控件，可以搜索用户并根据显示的列筛选用户列表。

### <a name="reset-details"></a>重置详细信息

单击注册或重置图表会显示重置详细信息。

"重置详细信息" 报告显示过去30天内的注册和重置事件，其中包括：

- 名称
- 用户名
- 功能（全部、注册、重置）
- 身份验证方法（应用通知、应用代码、电话呼叫、办公室电话、短信、电子邮件、安全问题）
- 状态（全部、成功、失败）

使用列表顶部的控件，可以搜索用户并根据显示的列筛选用户列表。

## <a name="limitations"></a>限制

这些报告中显示的数据将延迟达60分钟。 "上次刷新时间" 字段存在于 Azure 门户中，用于识别最近的数据的方式。

使用情况和见解数据并不能替代 Azure 多重身份验证活动报告或 Azure AD 登录报告中包含的信息。

## <a name="next-steps"></a>后续步骤

- [使用身份验证方法使用情况报表 API](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [为你的组织选择身份验证方法](concept-authentication-methods.md)
- [组合注册体验](concept-registration-mfa-sspr-combined.md)
