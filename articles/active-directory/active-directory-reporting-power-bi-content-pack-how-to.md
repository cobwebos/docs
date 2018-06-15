---
title: 如何使用 Azure Active Directory Power BI 内容包 | Microsoft Docs
description: 了解如何使用 Azure Active Directory Power BI 内容包
services: active-directory
author: rolyon
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: get-started-article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: compliance-reports
ms.date: 12/06/2017
ms.author: rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: fd52f8a31d0fe54cce9b39d7de7eb45336efeb7c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588318"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>如何使用 Azure Active Directory Power BI 内容包

作为 IT 管理员，了解用户如何使用 Azure Active Directory 功能十分重要。这样才能规划 IT 基础结构和通信，提高使用率并充分利用 AAD 功能。 使用 Azure Active Directory Power BI 内容包可以进一步分析数据，了解如何针对极度依赖的各种功能，通过该数据深入洞察 Azure Active Directory 中发生的情况。  将 Azure Active Directory API 集成到 Power BI 中以后，即可轻松下载预建的内容包，并通过 Power BI 提供的丰富的可视化体验洞察 Azure Active Directory 中的所有活动。 可以轻松地创建自己的仪表板并将其与组织中的任何其他人共享。 

本主题提供的分步说明介绍如何在环境中安装和使用内容包。

## <a name="installation"></a>安装  

若要安装 Power BI 内容包，请执行以下步骤：

1. 使用 Power BI 帐户（与 O365 或 Azure AD 帐户属于同一帐户）登录到 [Power BI](https://app.powerbi.com/groups/me/getdata/services)。

2. 在左侧导航窗格底部选择“获取数据”。

    ![Azure Active Directory Power BI 内容包](./media/active-directory-reporting-power-bi-content-pack-how-to/01.png)
 
3. 在“服务”框中，单击“获取”。
   
    ![Azure Active Directory Power BI 内容包](./media/active-directory-reporting-power-bi-content-pack-how-to/02.png)

4.  搜索“Azure Active Directory”。

    ![Azure Active Directory Power BI 内容包](./media/active-directory-reporting-power-bi-content-pack-how-to/03.png)
 
5.  出现提示时，键入 Azure AD 租户 ID，然后单击“下一步”。

    > [!TIP] 
    > 若要获取 Office 365 / Azure AD 租户的租户 ID，一种快速的方式是登录到 Azure AD 门户，向下钻取到目录，然后从[**属性**页](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)复制**目录 ID**。

    ![Azure Active Directory Power BI 内容包](./media/active-directory-reporting-power-bi-content-pack-how-to/04.png) 

6.  单击“登录”。 
 
    ![Azure Active Directory Power BI 内容包](./media/active-directory-reporting-power-bi-content-pack-how-to/05.png) 



7.  输入用户名和密码，然后单击“登录”。
 
    ![Azure Active Directory Power BI 内容包](./media/active-directory-reporting-power-bi-content-pack-how-to/06.png) 

8.  在应用的“同意”对话框中，单击“接受”。
 
9.  创建 Azure Active Directory 活动日志仪表板后，请单击该仪表板。
 
    ![Azure Active Directory Power BI 内容包](./media/active-directory-reporting-power-bi-content-pack-how-to/08.png) 

## <a name="what-can-i-do-with-this-content-pack"></a>此内容包有哪些功能？

在介绍此内容包的功能之前，让我们快速预览一下内容包中的各种报表。 报表数据可回溯到“过去 30 天”。

### <a name="reports-included-in-this-version-of-azure-active-directory-logs-content-pack"></a>包括在此版 Azure Active Directory 日志内容包中的报表

应用使用情况和趋势报表：了解在组织中使用的应用、哪些应用使用最频繁以及相应的使用时间。 可以通过此报表了解最近在组织中推出的应用的使用情况，或者了解哪些应用常用。 这样可以发现用户不使用的应用，从而改进使用情况。

按位置和用户划分的登录：了解使用 Azure Identity 执行的所有登录以及相应的用户身份。 可以通过此功能更深入地了解单个登录，并获取下面这样的问题的答案：

- 该用户从何处登录？
- 哪个用户的登录次数最多？从何处登录？ 
- 登录是否成功？  
 
单击具体日期或位置即可获取详细信息。

每个应用的唯一用户：了解使用给定应用的所有唯一用户。 这只包括“成功”登录应用程序的用户。

设备登录：了解组织中用户所使用的 OS 和浏览器的类型，以及用户的详细信息，其中包括：

- 用户名
- IP 地址
- 位置 
- 登录状态 

可以通过此报表了解在组织中使用的各种设备配置文件，从而确定设备策略

SSPR 漏斗图：了解如何在组织中进行密码重置。 通过 SSPR 工具了解用户进行密码重置尝试的次数，以及其中有多少次获得成功。 通过 SSPR 漏斗图深入了解密码重置失败情况，并了解某些失败的发生原因。 可以通过此报表更深入地了解组织中 SSPR 工具的使用情况，以便进行正确的决策。

## <a name="customizing-azure-ad-activity-content-pack"></a>自定义 Azure AD 活动内容包

更改可视化效果：可以通过单击“编辑报表”并选择所要的可视化效果来更改报表可视化效果。
 
![Azure Active Directory Power BI 内容包](./media/active-directory-reporting-power-bi-content-pack-how-to/09.png) 
 
![Azure Active Directory Power BI 内容包](./media/active-directory-reporting-power-bi-content-pack-how-to/10.png) 

包括其他字段：若要添加或删除报表的字段，选择要添加/删除字段的视觉对象即可。 在下面的示例中，我将向表视图添加“登录状态”字段。 
 
![Azure Active Directory Power BI 内容包](./media/active-directory-reporting-power-bi-content-pack-how-to/11.png) 

将可视化效果固定到仪表板：可以自定义仪表板，使报表包含自己的可视化效果，然后将该效果固定到仪表板。 在下面的示例中，我添加了名为“登录状态”的新筛选器并将其包括到了报表中。 我还将可视化效果从条形图改成了折线图，因此可以将这个新的视觉对象固定到仪表板。

![Azure Active Directory Power BI 内容包](./media/active-directory-reporting-power-bi-content-pack-how-to/12.png) 

![Azure Active Directory Power BI 内容包](./media/active-directory-reporting-power-bi-content-pack-how-to/13.png) 
 

 


共享仪表板：创建所要的内容以后，即可将仪表板与组织中的用户共享。 请记住，在你共享报表以后，用户可以看到你在报表中选择的字段。
 
![Azure Active Directory Power BI 内容包](./media/active-directory-reporting-power-bi-content-pack-how-to/14.png) 



## <a name="scheduling-a-daily-refresh-of-your-power-bi-report"></a>计划每日刷新 Power BI 报表

若要计划每日刷新 Power BI 报表，请转到“数据集”>“设置”>“计划刷新”，然后对其进行设置，如下所示。
 
![Azure Active Directory Power BI 内容包](./media/active-directory-reporting-power-bi-content-pack-how-to/15.png) 

## <a name="updating-to-newer-version-of-content-pack"></a>更新到较新版本的内容包

若要更新内容包以获取较新的版本，请执行以下操作：

- 下载新的内容包，按本文所列说明对其进行设置：

- 设置完以后，请转到“数据源”>“设置”>“数据源凭据”，然后重新输入凭据，如下所示

    ![Azure Active Directory Power BI 内容包](./media/active-directory-reporting-power-bi-content-pack-how-to/16.png) 

新版内容包能够正常使用以后，即可根据需要删除旧的版本，方法是：删除与该内容包关联的基础报表和数据集。

## <a name="still-having-issues"></a>仍然存在问题？ 

请查看[故障排除指南](active-directory-reporting-troubleshoot-content-pack.md)。 有关 Power BI 的常规帮助，请查看这些[帮助文章](https://powerbi.microsoft.com/en-us/documentation/powerbi-service-get-started/)。
 

## <a name="next-steps"></a>后续步骤

有关报告的概述，请参阅 [Azure Active Directory 报告](active-directory-reporting-azure-portal.md)。
