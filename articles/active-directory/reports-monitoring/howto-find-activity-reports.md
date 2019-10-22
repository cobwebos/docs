---
title: 查找 Azure 门户中的 Azure Active Directory 用户活动报告 | Microsoft Docs
description: 了解在 Azure 门户中何处查找 Azure Active Directory 用户活动报告。
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13f1746b710acd24316de3d294c1822ba108a378
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "70127392"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>在 Azure 门户中查找活动报告

在本文中，你将了解如何在 Azure 门户中查找 Azure Active Directory (Azure AD) 用户活动报告。

## <a name="audit-logs-report"></a>审核日志报表

审核日志报告将关于应用程序活动的多个报告组合到单个视图中以提供基于上下文的报告。 若要访问审核日志报告，请执行以下操作：

1. 导航到 [Azure 门户](https://portal.azure.com)。
2. 从右上角选择你的目录，然后从左侧导航窗格中选择“Azure Active Directory”边栏选项卡。
3. 从“Azure Active Directory”边栏选项卡的“活动”部分中选择“审核日志”。 

    ![审核日志](./media/howto-find-activity-reports/482.png "审核日志")

审核日志报告合并了以下报告：

* 审核报表
* 密码重置活动
* 密码重置注册活动
* 自助服务组活动
* Office365 组名称更改
* 帐户设置活动
* 密码滚动更新状态
* 帐户设置错误

### <a name="filtering-on-audit-logs"></a>根据审核日志进行筛选

您可以使用审核报告中的高级筛选来访问特定类别的审核数据，方法是在**类别**筛选器中指定该类别。 例如，若要查看与用户相关的所有活动，请选择 " **UserManagement** " 类别。 

类别包括：

- 所有
- AdministrativeUnit
- ApplicationManagement
- Authentication
- 授权
- 联系人
- 设备
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- 其他
- 策略
- Microsoft.resourcemanagement.service.exe.config
- RoleManagement
- UserManagement

你还可以使用**服务**下拉筛选器在特定服务上进行筛选。 例如，若要获取与自助服务密码管理相关的所有审核事件，请选择 "**自助服务密码管理**" 筛选器。

服务包括：

- 所有
- 访问评审
- 帐户预配 
- 应用程序 SSO
- 身份验证方法
- B2C
- 条件访问
- 核心目录
- 权利管理
- 标识保护
- 受邀用户
- PIM
- 自助服务组管理
- 自助服务密码管理
- 使用条款

## <a name="sign-ins-report"></a>登录报告 

“登录”视图包括所有用户登录，以及“应用程序使用情况”报告。 还可以在“企业应用程序”概述的“管理”部分中查看应用程序使用情况信息。

若要访问登录报告，请执行以下操作：

1. 导航到 [Azure 门户](https://portal.azure.com)。
2. 从右上角选择你的目录，然后从左侧导航窗格中选择“Azure Active Directory”边栏选项卡。
3. 从“Azure Active Directory”边栏选项卡的“活动”部分中选择“登录”。 

    ![登录视图](./media/howto-find-activity-reports/483.png "登录视图")


### <a name="filtering-on-application-name"></a>根据应用程序名称进行筛选

可以使用登录报告查看有关应用程序使用情况的详细信息，可以根据用户名或应用程序名称进行筛选。

!["筛选登录事件" 页](./media/howto-find-activity-reports/07.png ""筛选登录事件" 页")

## <a name="security-reports"></a>安全报告

### <a name="anomalous-activity-reports"></a>异常活动报告

异常活动报告提供 Azure AD 可以检测和报告的与安全相关的风险检测的相关信息。

下表列出了 Azure AD 异常活动安全报告和 Azure 门户中的相应风险检测类型。 有关详细信息，请参阅 [Azure Active Directory 风险检测](concept-risk-events.md)。  


| Azure AD 异常活动报告 |  标识保护风险检测类型|
| :--- | :--- |
| 具有已泄漏凭据的用户 | 已泄漏凭据 |
| 异常登录活动 | 不可能前往异常位置 |
| 从可能受感染的设备登录 | 从受感染的设备登录|
| 从未知源登录 | 从匿名 IP 地址登录 |
| 从具有可疑活动的 IP 地址登录 | 从具有可疑活动的 IP 地址登录 |
| - | 从不熟悉的位置登录 |

以下 Azure AD 异常活动安全报告未作为 Azure 门户中的风险检测包括：

* 多次失败后登录
* 从多个地理区域登录


### <a name="detected-risk-detections"></a>检测到的风险检测

可以在[Azure 门户](https://portal.azure.com)中的 " **Azure Active Directory** " 边栏选项卡上的 "**安全**" 部分中访问有关检测到的风险检测的报告。 在以下报表中跟踪检测到的风险检测：   

- [有风险的用户](concept-user-at-risk.md)
- [有风险的登录](concept-risky-sign-ins.md)

    ![安全报表](./media/howto-find-activity-reports/04.png "安全报告")

## <a name="troubleshoot-issues-with-activity-reports"></a>排查活动报告问题

### <a name="missing-data-in-the-downloaded-activity-logs"></a>下载的活动日志中缺少数据

#### <a name="symptoms"></a>症状 

我下载了活动日志（审核日志或登录日志），但发现所选时间范围内的记录不全。 为什么？ 

 ![报告](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>原因

下载 Azure 门户中的活动日志时，会将扩展限制为250000个记录，并按最新的顺序进行排序。 

#### <a name="resolution"></a>分辨率

可以随时通过 [Azure AD 报告 API](concept-reporting-api.md) 获取多达一百万条记录。

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Azure 门户中缺少有关最近操作的审核数据

#### <a name="symptoms"></a>症状

我在 Azure 门户中执行了一些操作，本应在`Activity logs > Audit Logs`边栏选项卡中看到这些操作的审核日志，但却找不到。

 ![报告](./media/troubleshoot-missing-audit-data/01.png)
 
#### <a name="cause"></a>原因

操作不会立即显示在活动日志中。 下表枚举了活动日志的延迟数字。 

| 报告 | &nbsp; | 延迟 (P95) | 延迟 (P99) |
|--------|--------|---------------|---------------|
| 目录审核 | &nbsp; | 2 分钟 | 5 分钟 |
| 登录活动 | &nbsp; | 2 分钟 | 5 分钟 | 

#### <a name="resolution"></a>分辨率

等待 15 分钟到 2 小时，再看操作是否显示在日志中。 如果 2 小时后仍未看到日志，请[提交支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，我们会进行调查。

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Azure AD 登录活动日志中缺少有关最近用户登录的日志

#### <a name="symptoms"></a>症状

我最近登录了 Azure 门户，本应在`Activity logs > Sign-ins`边栏选项卡中看到这些操作的登录日志，但却找不到。

 ![报告](./media/troubleshoot-missing-audit-data/02.png)
 
#### <a name="cause"></a>原因

操作不会立即显示在活动日志中。 下表枚举了活动日志的延迟数字。 

| 报告 | &nbsp; | 延迟 (P95) | 延迟 (P99) |
|--------|--------|---------------|---------------|
| 目录审核 | &nbsp; | 2 分钟 | 5 分钟 |
| 登录活动 | &nbsp; | 2 分钟 | 5 分钟 | 

#### <a name="resolution"></a>分辨率

等待 15 分钟到 2 小时，再看操作是否显示在日志中。 如果 2 小时后仍未看到日志，请[提交支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，我们会进行调查。

### <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>无法在 Azure 门户中查看 30 天以上的报表数据

#### <a name="symptoms"></a>症状

无法在 Azure 门户中查看 30 天以上的登录和审核数据。 为什么？ 

 ![报告](./media/troubleshoot-missing-audit-data/03.png)

#### <a name="cause"></a>原因

根据你持有的许可证，Azure Active Directory 操作会按以下持续时间存储活动报告：

| 报告           | &nbsp; |  Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ----   |  ---           | ---                 | ---                 |
| 目录审核  | &nbsp; |   7 天     | 30 天             | 30 天             |
| 登录活动 | &nbsp; | 不可用。 可以在单个用户配置文件边栏选项卡中访问自己在 7 天内的登录活动 | 30 天 | 30 天             |

有关详细信息，请参阅 [Azure Active Directory 报告保留策略](reference-reports-data-retention.md)。  

#### <a name="resolution"></a>分辨率

可以通过两个选项将数据保留 30 天以上。 可以使用 [Azure AD 报告 API](concept-reporting-api.md) 以编程方式检索数据并将其存储在数据库中。 也可将审核日志集成到第三方 SIEM 系统（例如 Splunk 或 SumoLogic）中。

## <a name="next-steps"></a>后续步骤

* [审核日志概述](concept-audit-logs.md)
* [登录概述](concept-sign-ins.md)
* [有风险事件概述](concept-risk-events.md)
