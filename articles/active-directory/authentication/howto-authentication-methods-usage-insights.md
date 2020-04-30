---
title: 身份验证方法用法 & insights-Azure Active Directory
description: Azure AD 自助式密码重置和多重身份验证方法使用情况的报告
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 053b052abf6f23c385dc7447639aa40b6c2c58a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680190"
---
# <a name="authentication-methods-usage--insights-preview"></a>身份验证方法的使用情况和见解（预览版）

借助使用情况和见解，可以了解针对 Azure 多重身份验证和自助式密码重置等功能的身份验证方法在组织中的运行方式。 此报告功能可让组织了解正在注册哪些方法，以及这些方法的使用方式。

## <a name="permissions-and-licenses"></a>权限和许可证

以下角色可以访问使用情况和见解：

- 全局管理员角色
- 安全读取者
- 安全管理员
- 报告读者

访问使用情况和见解无需获得额外的许可。 可在 [Azure Active Directory 定价站点](https://azure.microsoft.com/pricing/details/active-directory/)上找到 Azure 多重身份验证和自助式密码重置 (SSPR) 许可信息。

## <a name="how-it-works"></a>工作原理

若要访问身份验证方法使用情况和见解：

1. 浏览到 [Azure 门户](https://portal.azure.com)。
1. 浏览到“Azure Active Directory” > “密码重置” > “使用情况和见解”。   
1. 在“注册”或“使用情况”概述中，可以选择打开预先筛选的报告，以根据需要进行筛选。  

![使用情况和见解概述](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

若要直接访问使用情况和见解，请转到 [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade)。 单击此链接会转到注册概述。

“已注册用户”、“已启用用户”和“已赋能用户”磁贴显示用户的以下注册数据：

- 已注册：如果某个用户（或管理员）已根据组织的 SSPR 或多重身份验证策略注册了足够的身份验证方法，则认为该用户是已注册的用户。
- 已启用：如果某个用户在 SSPR 策略的范围内，该认为该用户是已启用的用户。 如果为某个组启用了 SSPR，而某个用户在该组中，则认为该用户是已启用的用户。 如果为所有用户启用了 SSPR，则认为租户中的所有用户（不包括来宾）都是已启用的用户。
- 已赋能：如果某个用户既已注册，也已启用，则认为该用户是已赋能的用户。 此状态意味着该用户随时可按需执行 SSPR。

单击其中的任一磁贴或磁贴中显示的见解会转到注册详细信息的预筛选列表。

“注册”选项卡上的“注册”图表按身份验证方法显示成功和失败的身份验证方法注册数。   “使用情况”选项卡上的“重置”图表按身份验证方法显示执行密码重置流期间成功和失败的身份验证数。  

单击其中的任一图表会转到注册或重置事件的预筛选列表。

使用右上角的控件可将“注册”和“重置”图表中显示的审核数据的日期范围更改为 24 小时、7 天或 30 天。

### <a name="registration-details"></a>注册详细信息

单击“已注册用户”、“已启用用户”或“已赋能用户”磁贴或见解会转到注册详细信息。   

注册详细信息报告显示每个用户的以下信息：

- 名称
- 用户名
- 注册状态（“全部”、“已注册”、“未注册”）
- 启用状态（“全部”、“已启用”、“未启用”）
- 赋能状态（“全部”、“已赋能”、“未赋能”）
- 方法（“应用通知”、“应用代码”、“电话呼叫”、“短信”、“电子邮件”、“安全提问”）

使用列表顶部的控件可以搜索用户，并根据显示的列筛选用户列表。

### <a name="reset-details"></a>重置详细信息

单击“注册”或“重置”图表会转到重置详细信息。

重置详细信息报告显示过去 30 天的注册和重置事件，包括：

- 名称
- 用户名
- 功能（“全部”、“注册”、“重置”）
- 身份验证方法（“应用通知”、“应用代码”、“电话呼叫”、“呼叫办公电话”、“短信”、“电子邮件”、“安全提问”）
- 状态（“全部”、“成功”、“失败”）

使用列表顶部的控件可以搜索用户，并根据显示的列筛选用户列表。

## <a name="limitations"></a>限制

这些报告中显示的数据最长会延迟 60 分钟。 "上次刷新时间" 字段存在于 Azure 门户中，用于识别最近的数据的方式。

使用情况和见解数据不能取代 Azure 多重身份验证活动报告，也不能取代 Azure AD 登录报告中包含的信息。

当前无法筛选报表以排除外部用户。

## <a name="next-steps"></a>后续步骤

- [使用身份验证方法使用情况报告 API](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [为你的组织选择身份验证方法](concept-authentication-methods.md)
- [组合注册体验](concept-registration-mfa-sspr-combined.md)
