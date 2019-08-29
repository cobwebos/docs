---
title: 如何调查 Azure Active Directory identity protection 中的风险 (已刷新) |Microsoft Docs
description: 了解如何在 Azure Active Directory identity protection (已刷新) 中调查有风险的用户、检测和登录。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e9715b63ccfbf1da3bdaedf947ea4cd65109b7
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129713"
---
# <a name="how-to-investigate-risk"></a>如何：调查风险

使用有风险的登录、有风险的用户和风险检测报告, 你可以调查并深入了解你的环境中的风险。 能够对有风险的登录、用户和检测进行筛选和排序, 可以更好地了解组织中的潜在入侵。 

## <a name="risky-users-report"></a>有风险的用户报告

通过“有风险用户”报告提供的信息，可以找到下面这些问题的答案：

- 哪些用户的风险高？
- 哪些用户的风险状态为“已修正”？

此报告的第一个入口点是“安全性”页上的“调查”部分。

![“有风险用户”报告](./media/howto-investigate-risky-users-signins/01.png)

“有风险用户”报告的默认视图显示以下信息：

- 姓名
- 风险状态
- 风险级别
- 风险详细信息
- 风险上次更新时间
- type
- 状态

![“有风险用户”报告](./media/howto-investigate-risky-users-signins/03.png)

单击工具栏中的“列”即可自定义列表视图。

![“有风险用户”报告](./media/howto-investigate-risky-users-signins/04.png)

使用“列”对话框，可以显示其他字段或删除已显示的字段。

通过单击列表视图中的项，可以在水平视图中获得有关该项的所有可用详细信息。

![“有风险用户”报告](./media/howto-investigate-risky-users-signins/05.png)

“详细信息”视图显示：

- 基本信息
- 最新有风险登录
- 风险检测未链接到登录
- 风险历史记录

除此之外，还可以：

![“有风险用户”报告](./media/howto-investigate-risky-users-signins/08.png)

- 查看所有登录快捷方式，以查看相应用户的登录报告。
- 查看所有有风险登录，以查看相应用户所有被标记为有风险的登录。
- 重置用户密码，如果你认为用户身份已被盗用的话。
- 如果你认为用户的活动风险检测为误报, 则消除用户风险。 有关详细信息, 请参阅文章[Azure AD Identity Protection 中的风险检测提供反馈](howto-provide-risk-event-feedback.md)。

### <a name="filter-risky-users"></a>筛选有风险用户

若要将报告数据的范围缩小到适当级别，可使用下面的默认字段来筛选有风险用户数据：

- 姓名
- 用户名
- 风险状态
- 风险级别
- 类型
- 状态

![“有风险用户”报告](./media/howto-investigate-risky-users-signins/06.png)

使用“名称”筛选器，可以指定你关注用户的用户名或用户主体名称 (UPN)。

使用“风险状态”筛选器，可以选择：

- 存在风险
- 已修正
- 已消除

使用“风险级别”筛选器，可以选择：

- 高
- 中型
- 低

使用“类型”筛选器，可以选择：

- 来宾
- 成员

使用“状态”筛选器，可以选择：

- 已删除
- 活跃

### <a name="download-risky-users-data"></a>下载有风险用户数据

若要在 Azure 门户外部使用有风险用户数据，可以下载此类数据。 单击“下载”会创建包含最近 2,500 条记录的 CSV 文件。 

![有风险的用户报告](./media/howto-investigate-risky-users-signins/07.png)

单击工具栏中的“列”可以自定义列表视图。
 
用于显示其他字段，或者删除已显示的字段。
 
若要详细了解有风险用户，请单击“详细信息”导航抽屉以展开它

## <a name="risky-sign-ins-report"></a>风险登录报告

通过“有风险登录”报告提供的信息，可以找到下面这些问题的答案：

- 在过去的一周中, 有多少次成功登录有匿名 IP 地址的风险检测？
- 已确认哪些用户在上个月遭入侵？
- 哪些用户的 Office 365 门户登录有风险？

此报告的第一个入口点是“安全性”页上的“调查”部分。

![风险登录报告](./media/howto-investigate-risky-users-signins/02.png)

“有风险登录”报告的默认视图显示以下信息：

- Date
- 用户
- 应用程序
- 登录状态
- 风险状态
- 风险级别(总计)
- 风险级别(实时)
- 条件访问
- 需要 MFA  

![风险登录报告](./media/howto-investigate-risky-users-signins/09.png)

单击工具栏中的“列”即可自定义列表视图。

![“有风险用户”报告](./media/howto-investigate-risky-users-signins/11.png)

使用“列”对话框，可以显示其他字段或删除已显示的字段。

通过单击列表视图中的项，可以在水平视图中获得有关该项的所有可用详细信息。

![“有风险用户”报告](./media/howto-investigate-risky-users-signins/12.png)

“详细信息”视图显示：

- 基本信息
- 设备信息
- 风险信息
- MFA 信息
- 条件访问

除此之外，还可以：

![“有风险用户”报告](./media/howto-investigate-risky-users-signins/13.png)

- 确认是否已遭入侵 
- 确认安全

有关详细信息, 请参阅文章[Azure AD Identity Protection 中的风险检测提供反馈](howto-provide-risk-event-feedback.md)。

### <a name="filter-risky-sign-ins"></a>筛选有风险登录

若要将报告数据的范围缩小到适当级别，可使用下面的默认字段来筛选有风险用户数据：

- 用户
- 应用程序
- 登录状态
- 风险状态
- 风险级别(总计)
- 风险级别(实时)
- 条件访问
- Date
- 风险级别类型

![风险登录报告](./media/howto-investigate-risky-users-signins/14.png)

使用“名称”筛选器，可以指定你关注用户的用户名或用户主体名称 (UPN)。

使用“应用”筛选器，可以指定用户已尝试访问的云应用。

“登录状态”筛选器用于选择：

- 全部
- Success
- 失败

使用“风险状态”筛选器，可以选择：

- 存在风险
- 已确认遭入侵
- 已确认安全
- 已消除
- 已修正

使用“风险级别(总计)”筛选器，可以选择：

- 高
- 中型
- 低

使用“风险级别(实时)”筛选器，可以选择：

- 高
- 中型
- 低

使用“条件访问”筛选器，可以选择：

- 全部
- 未应用
- Success
- 失败

“日期”筛选器用于定义已返回数据的时间范围。
可能的值包括：

- 过去 1 个月
- 最近 7 天
- 前 24 小时
- 自定义时间范围

### <a name="download-risky-sign-ins-data"></a>下载有风险登录数据

若要在 Azure 门户外部使用有风险登录数据，可以下载此类数据。 单击“下载”会创建包含最近 2,500 条记录的 CSV 文件。 

![有风险的用户报告](./media/howto-investigate-risky-users-signins/15.png)


## <a name="risk-detections-report"></a>风险检测报告

风险检测报告可让你深入了解租户中的每个标识保护风险检测。


风险检测报告有一个默认视图, 其中显示:

- Date

- 用户

- IP 地址

- Location

- 检测类型

- 风险状态

- 风险级别

- 请求 ID
 

单击工具栏中的“列”即可自定义列表视图。 使用“列”对话框，可以显示其他字段或删除已显示的字段。

通过单击列表视图中的项，可以在水平视图中获得有关该项的所有可用详细信息。


详细信息视图显示有关风险检测的其他信息。

详细信息视图还包括指向

- 查看用户的风险报告
- 查看用户的登录
- 查看用户的风险登录
- 查看链接的有风险登录
- 查看用户的风险检测


### <a name="filter-risk-detections"></a>筛选风险检测

若要将报告的数据缩小到适合您的级别, 可以使用以下默认字段筛选风险检测数据:

- 检测时间
- 检测类型
- 风险状态
- 风险级别
- 用户
- 用户名
- IP 地址
- Location
- 检测时间
- activities 
- Source
- 令牌颁发者类型
- 请求 ID
- 相关 ID


使用 "**检测时间**" 筛选器可以定义返回数据的时间范围。 此筛选器允许您选择:
- 最近 90 天
- 过去 30 天
- 最近 7 天
- 前 24 小时
- 自定义时间范围

"**检测类型**" 筛选器使你可以指定风险检测的类型 (如不熟悉的登录属性)。

使用“风险状态”筛选器，可以选择：

- 存在风险
- 已确认遭入侵
- 已确认安全
- 已消除
- 已修复

使用“风险级别”筛选器，可以选择：

- 高
- 中型
- 低

**用户**筛选器可用于指定所关注的用户的名称或对象 ID。

**用户名**筛选器可用于指定所关注的用户的用户主体名称 (UPN)。

"**检测计时**" 筛选器可用于指定检测是实时还是脱机。 注意:你可以使用登录风险策略来质询使用实时检测的登录。 

**活动**筛选器使你可以指定是否已将检测链接到登录 (例如, 匿名 IP 地址) 或用户 (例如泄漏的凭据)。

**源**筛选器可用于指定风险检测的源 (如 Azure AD 或 Microsoft Cloud App Security)。 

**令牌颁发者类型**筛选器允许你指定颁发令牌的位置 (例如 AZURE AD 或 AD 联合身份验证服务)


### <a name="download-risk-detections-data"></a>下载风险检测数据

如果你想要在 Azure 门户之外使用它, 则可以下载风险检测数据。 单击 "下载" 将创建最新5000记录的 CSV 文件。 

## <a name="next-steps"></a>后续步骤

若要获取“Azure AD 标识保护”的概述，请参阅 [Azure AD 标识保护概述](overview-v2.md)。
