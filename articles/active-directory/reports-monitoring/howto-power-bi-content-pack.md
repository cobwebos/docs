---
title: 如何使用 Azure Active Directory Power BI 内容包 | Microsoft Docs
description: 了解如何使用 Azure Active Directory Power BI 内容包
services: active-directory
author: cawrites
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d56802e96028b6b01b6be749405c56df2648161
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988234"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>如何使用 Azure Active Directory Power BI 内容包

|  |
|--|
|目前，Azure AD Power BI 内容包使用 Azure AD Graph API 检索 Azure AD 租户中的数据。 因此，你可能会发现内容包中可用数据与使用[用于报告的 Microsoft Graph API](concept-reporting-api.md) 检索的数据之间存在一些差异。 |
|  |

Azure Active directory (Azure AD) 的 Power BI 内容包包含预生成的报表，可帮助你了解用户如何采用和使用 Azure AD 功能。 这样，你便可以使用 Power BI 中的丰富可视化体验来洞察目录中的所有活动。 还可以创建自己的仪表板并将其与组织中的任何其他人共享。 

## <a name="prerequisites"></a>先决条件

需要 Azure AD premium (P1/P2) 许可证才能使用内容包。 请参阅 [Azure Active Directory Premium 入门](../fundamentals/active-directory-get-started-premium.md)来升级 Azure Active Directory 版本。

## <a name="install-the-content-pack"></a>安装内容包

查看[快速入门](quickstart-install-power-bi-content-pack.md)以安装 Azure AD Power BI 内容包。

### <a name="reports-included-in-this-version-of-azure-ad-logs-content-pack"></a>包括在此版 Azure AD 日志内容包中的报表

Azure AD Power BI 内容包中包含以下报表。 这些报表包含**过去 30 天**的数据。

**应用用法和趋势报表**：此报表可让你洞察组织中使用的应用程序。 可以获取最常用应用程序的列表，或了解最近在组织中推出的应用程序的用法。 这样，便可以跟踪用法并不断改进。

**按位置和用户列出的登录活动**：此报表提供有关使用 Azure 标识执行的所有登录的数据。 借助此报表可以深入到单个登录，并回答如下所述的问题：

- 此用户从何处登录？
- 哪个用户的登录次数最多？从何处登录？ 
- 登录是否成功？  
 
还可以通过选择特定的日期或位置来筛选结果。

**每个应用的唯一用户**：此报表提供使用给定应用的所有唯一用户的视图。 其中只包含“成功”登录到应用程序的用户。

**设备登录**：此报表可帮助你了解组织中使用的各种设备配置文件，并基于用法确定设备策略。 其中提供了有关 OS 类型和用于登录应用程序的浏览器的数据，以及有关用户的详细信息，包括：

- 用户名
- IP 地址
- Location 
- 登录状态 

**SSPR 漏斗图**：此报表可帮助了解如何在组织中使用 SSPR 工具。 可以查看通过 SSPR 工具尝试了多少次密码重置，以及多少次尝试成功。 还可以深入了解密码重置失败情况，并了解某些失败的发生原因。 

## <a name="customize-azure-ad-activity-content-pack"></a>自定义 Azure AD 活动内容包

**更改可视化效果**：可以通过单击“编辑报表”并选择所要的可视化效果来更改报表可视化效果。
 
![Azure Active Directory Power BI 内容包](./media/howto-power-bi-content-pack/09.png) 
 
![Azure Active Directory Power BI 内容包](./media/howto-power-bi-content-pack/10.png) 

**包括其他字段**：若要添加或删除报表的字段，选择要添加/删除字段的视觉对象即可。 例如，可将“登录状态”字段添加到下面所示的表视图。 
 
![Azure Active Directory Power BI 内容包](./media/howto-power-bi-content-pack/11.png) 

**将可视化效果固定到仪表板**：可以自定义仪表板，使报表包含自己的可视化效果，然后将该效果固定到仪表板。 

![Azure Active Directory Power BI 内容包](./media/howto-power-bi-content-pack/13.png) 
 
**共享仪表板**：还可以与组织中的用户共享仪表板。 在你共享报表以后，用户可以看到你在报表中选择的字段。
 
![Azure Active Directory Power BI 内容包](./media/howto-power-bi-content-pack/14.png) 

## <a name="schedule-a-daily-refresh-of-your-power-bi-report"></a>计划每日刷新 Power BI 报表

若要计划每日刷新 Power BI 报表，请转到“数据集” > “设置” > “计划刷新”，然后对其进行设置，如下所示。
 
![Azure Active Directory Power BI 内容包](./media/howto-power-bi-content-pack/15.png) 

## <a name="update-to-newer-version-of-content-pack"></a>更新到较新版本的内容包

若要更新内容包以获取较新的版本，请执行以下操作：

- 下载新的内容包，并使用本文中的说明安装该包：

- 安装后，转到“数据源” > “设置” > “数据源凭据”，然后重新输入凭据。

    ![Azure Active Directory Power BI 内容包](./media/howto-power-bi-content-pack/16.png) 

确认新版内容包可按预期方式工作后，可根据需要删除旧的版本，方法是删除与该内容包关联的基础报表和数据集。

## <a name="troubleshoot-content-pack-errors"></a>对内容包错误进行故障排除

在使用内容包时，可能会遇到以下错误： 

- [刷新失败](#refresh-failed) 
- [无法更新数据源凭据](#failed-to-update-data-source-credentials) 
- [导入数据的时间过长](#data-import-is-too-slow) 

有关 Power BI 的常规帮助，请查看这些[帮助文章](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)。

### <a name="refresh-failed"></a>刷新失败 
 
**此错误是如何发现的**：根据 Power BI 的电子邮件或刷新历史记录中的失败状态。 


| 原因 | 如何解决 |
| ---   | ---        |
| 当连接到内容包的用户的凭据被重置，但在内容包的连接设置中未更新时，可能会导致刷新失败错误。 | 在 Power BI 中，找到与 Azure AD 活动日志仪表板（**Azure Active Directory 活动日志**）相对应的数据集，选择计划刷新，然后输入你的 Azure AD 凭据。 |
| 由于基础内容包中的数据问题可能会导致刷新失败。 | [提交一个支持票证](../fundamentals/active-directory-troubleshooting-support-howto.md)。|
 
 
### <a name="failed-to-update-data-source-credentials"></a>无法更新数据源凭据 
 
**此错误是如何发现的**：在 Power BI 中，当连接到 Azure AD 活动日志内容包时，会出现此错误。 

| 原因 | 如何解决 |
| ---   | ---        |
| 连接的用户既不是全局管理员，也不是安全读取者或安全管理员。 | 使用全局管理员、安全读取者或安全管理员的帐户来访问内容包。 |
| 你的租户不是高级租户，或者至少没有一个拥有高级许可文件的用户。 | [提交一个支持票证](../fundamentals/active-directory-troubleshooting-support-howto.md)。|
 
### <a name="data-import-is-too-slow"></a>数据导入速度太慢 
 
**此错误是如何发现的**：在 Power BI 中，在连接内容包后，数据导入过程开始为 Azure AD 活动日志准备仪表板。 将显示以下消息：**正在导入数据...** 且没有进一步的进展。  

| 原因 | 如何解决 |
| ---   | ---        |
| 根据租户大小，此步骤可能会需要从几分钟到 30 分钟不等的时间。 | 如果该消息在一个小时内没有更改显示你的仪表板，请[提交一个支持票证](../fundamentals/active-directory-troubleshooting-support-howto.md)。|
  
## <a name="next-steps"></a>后续步骤

* [安装 Power BI 内容包](quickstart-install-power-bi-content-pack.md)。
* [什么是 Azure AD 报告？](overview-reports.md)。
