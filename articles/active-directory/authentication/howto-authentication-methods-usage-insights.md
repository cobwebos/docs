---
title: 身份验证方法使用情况和见解报告 （预览版）-Azure Active Directory
description: 报告 Azure AD 自助服务密码重置和多重身份验证身份验证方法使用情况
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
ms.openlocfilehash: a0f6a74308f1bc4a7b77576fb9f39f965de0a4f8
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561031"
---
# <a name="authentication-methods-usage--insights-preview"></a>身份验证方法使用情况和见解 （预览）

根据使用情况和见解，可了解你的组织中的功能，如 Azure 多重身份验证和自助服务密码重置的身份验证方法的工作方式。 此报告功能提供了您的组织使用来了解正在注册哪些方法以及如何使用它们的方式。

## <a name="permissions-and-licenses"></a>权限和许可证

使用情况和见解，可以访问以下角色：

- 全局管理员角色
- 安全读取者
- 安全管理员
- 报告读者

没有额外的许可，则需要访问的使用情况和见解。 Azure 多重身份验证和自助服务密码重置 (SSPR) 授权信息，可[Azure Active Directory 定价站点](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="how-it-works"></a>工作原理

若要访问身份验证方法使用情况和见解：

1. 浏览到 [Azure 门户](https://portal.azure.com)。
1. 浏览到**Azure Active Directory** > **密码重置** > **根据使用情况和见解**。
1. 从**注册**或**用法**概述，可以选择以打开预筛选报表以筛选根据你的需求。

![根据使用情况和见解概述](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

若要直接访问使用情况和见解，请转到[ https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade)。 此链接将转到注册概述。

用户已注册，用户启用，并且用户能够磁贴显示你的用户的以下注册数据：

- 注册：用户是被视为已注册如果他们 （或管理员） 已注册足够的身份验证方法，以满足您组织的 SSPR 或多重身份验证策略。
- 已启用:将用户视为启用是否 SSPR 策略的作用域。 如果组已启用 SSPR，用户被视为已启用它们是否在该组中。 如果 （不包括来宾） 的租户中的所有用户都被视为已启用，将为所有用户启用 SSPR。
- 支持：如果它们是已注册和启用，用户被视为支持。 此状态表示，他们可以执行 SSPR 随时根据需要。

单击任何磁贴或它们所示的见解将使注册详细信息的预筛选列表。

**登记**图表上**注册**选项卡显示了成功和失败的身份验证数方法注册的身份验证方法。 **重置**图表上**用法**选项卡显示成功数和在密码的身份验证失败的身份验证方法重置流。

单击图表的任何一个将转到相应的注册预筛选列表或重置事件。

使用中左上、 右上角的控件，可以更改为 24 小时、 7 天或 30 天的注册和重置图表中显示的审核数据的日期范围。

从注册数据 

### <a name="registration-details"></a>注册详细信息

单击**注册的用户**，**已启用的用户**，或**用户能够**磁贴或见解可转到注册详细信息。

注册详细信息报表显示每个用户的以下信息：

- Name
- 用户名
- 注册状态，所有已注册的 (未注册）
- 已启用状态 （所有，已启用，未启用）
- 支持的状态 (所有，能够，不支持)
- 方法 （应用通知、 应用程序代码、 电话呼叫、 短信、 电子邮件，安全问题）

在列表的顶部使用的控件，可以搜索用户和筛选的基础显示的列的用户列表。

### <a name="reset-details"></a>重置的详细信息

单击注册或重置图表上将使重置的详细信息。

重置的详细信息报告显示从包括在过去 30 天的注册和重置事件：

- 名称
- 用户名
- （所有注册，重置） 的功能
- 身份验证方法 （应用通知、 应用程序代码、 电话、 办公呼叫、 短信、 电子邮件，安全问题）
- 状态 （全部，如果成功，失败）

在列表的顶部使用的控件，可以搜索用户和筛选的基础显示的列的用户列表。

## <a name="limitations"></a>限制

这些报表中显示的数据将最多 60 分钟的时间延迟。 若要确定你的数据的最近在 Azure 门户中存在的"上次刷新时间"字段。

使用情况和见解的数据不是替换为 Azure 多重身份验证活动报告或 Azure AD 登录报告中包含的信息。

## <a name="next-steps"></a>后续步骤

- [使用身份验证方法使用情况报告 API](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [选择你的组织的身份验证方法](concept-authentication-methods.md)
- [结合使用注册体验](concept-registration-mfa-sspr-combined.md)
