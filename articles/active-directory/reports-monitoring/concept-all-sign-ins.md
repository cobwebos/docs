---
title: Azure Active Directory 登录活动报告-预览 |Microsoft Docs
description: Azure Active Directory 门户中的登录活动报表简介
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/23/2020
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1fc5051a6cc8b8f36a980ff86690ed4f8cbac60
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342756"
---
# <a name="azure-active-directory-sign-in-activity-reports---preview"></a>Azure Active Directory 登录活动报告-预览

Azure Active Directory (Azure AD) 中的报告体系结构由以下部分组成：

- **活动** 
    - **登录** -有关用户、应用程序和托管资源登录到 Azure AD 并访问资源的信息。
    - **审核日志** - [审核日志](concept-audit-logs.md) - 有关用户和组管理、托管应用程序和目录活动的系统活动信息。
- **安全性** 
    - 有**风险的登录**-有[风险的登录](concept-risky-sign-ins.md)是指不是用户帐户合法所有者的登录尝试。
    - **已标记为存在风险的用户** - [风险用户](concept-user-at-risk.md)是指可能已泄露的用户帐户。

Azure Active Directory 中的经典登录报告提供交互式用户登录概述。此外，你现在可以访问三个目前处于预览阶段的其他登录报告：

- 非交互式用户登录

- 服务主体登录

- Azure 资源登录的托管标识

本文概述了 "登录活动" 报表，其中包含 Azure 资源登录的非交互式、应用程序和托管标识的预览。有关无预览功能的登录报告的信息，请参阅  [Azure Active Directory 门户中的登录活动报告](concept-sign-ins.md)。



## <a name="prerequisites"></a>先决条件

在开始使用此功能之前，应了解以下内容的答案：

- 谁可以访问该数据？

- 访问登录活动需要什么 Azure AD 许可证？

### <a name="who-can-access-the-data"></a>谁可以访问该数据？

- 具有“安全管理员”、“安全读取者”和“报告读取者”角色的用户

- 全局管理员

- 任何用户（非管理员）都可以访问自己的登录活动 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>访问登录活动需要什么 Azure AD 许可证？

你的租户必须具有与之关联的 Azure AD Premium 许可证，才能查看登录活动。 请参阅 [Azure Active Directory Premium 入门](../fundamentals/active-directory-get-started-premium.md)来升级 Azure Active Directory 版本。 如果在升级之前没有数据活动，则在升级到高级版许可证后，数据需要经过几天才会显示在报表中。



## <a name="sign-ins-report"></a>登录报告

"登录" 报表提供以下问题的答案：

- 用户、应用程序或服务的登录模式是什么？
- 一周内有多少用户、应用或服务登录？
- 这些登录的状态怎样？


在 "登录报告" 边栏选项卡中，你可以在以下项之间切换：

- **交互式用户登录** -用户提供身份验证因素，例如密码、通过 MFA 应用的响应、生物识别因子或 QR 码。

- **非交互式用户** 登录-客户端代表用户执行的登录。 这些登录不需要用户进行任何交互或身份验证。 例如，使用 "刷新" 和 "访问令牌" 进行身份验证和授权，而不要求用户输入凭据。

- **服务主体登录** -由应用和不涉及任何用户的服务主体登录。 在这些登录中，应用程序或服务代表自己提供凭据以对资源进行身份验证或访问。

- **Azure 资源的托管标识登录** -由 azure 管理密码的 azure 资源登录。 有关详细信息，请参阅[什么是 Azure 资源的托管标识？](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 


![登录报告类型](./media/concept-all-sign-ins/sign-ins-report-types.png)












## <a name="user-sign-ins"></a>用户登录

"登录" 边栏选项卡中的每个选项卡都显示下面的默认列。 某些选项卡具有其他列：

- 登录日期

- 请求 ID

- 用户名或用户 ID

- 应用程序名称或应用程序 ID

- 登录状态

- 用于登录的设备的 IP 地址



### <a name="interactive-user-sign-ins"></a>交互式用户登录


交互式用户登录是指用户提供身份验证因子的用户身份验证因素，以便直接与 Azure AD 或帮助程序应用程序（如 Microsoft Authenticator 应用程序）进行 Azure AD 或交互。 用户提供的因素包括：密码、对 MFA 质询的响应、生物特征或用户提供 Azure AD 给 helper 应用程序的 QR 码。

此报表还包括来自联合到 Azure AD 的标识提供者的联合登录。  


**报表大小：** 小 <br> 
**示例**

- 用户在 Azure AD 登录屏幕中提供用户名和密码。

- 用户通过 SMS MFA 质询。

- 用户提供生物识别手势，使用 Windows Hello 企业版来解锁其 Windows PC。

- 用户使用 AD FS SAML 断言与 Azure AD 联合。


除了默认字段，交互式登录报表还显示： 

- 登录位置

- 是否已应用条件性访问



单击工具栏中的“列”即可自定义列表视图。 

![交互式用户登录列](./media/concept-all-sign-ins/columns-interactive.png "交互式用户登录列")





自定义视图后，您可以显示其他字段或删除已显示的字段。

![所有交互式列](./media/concept-all-sign-ins/all-interactive-columns.png)


在列表视图中选择一项以获取有关相关登录的更多详细信息。

![登录活动](./media/concept-all-sign-ins/interactive-user-sign-in-details.png "交互式用户登录")



### <a name="non-interactive-user-sign-ins"></a>非交互式用户登录

非交互式用户登录是由客户端应用程序或操作系统组件代表用户执行的登录。 与交互式用户登录类似，这些登录代表用户执行。 与交互式用户登录不同，这些登录不要求用户提供身份验证因素。 相反，设备或客户端应用程序使用令牌或代码代表用户进行身份验证或访问资源。 通常情况下，用户会发现这些登录在用户活动的后台发生。


**报表大小：** 大型 <br>
**示例：** 

- 客户端应用使用 OAuth 2.0 刷新令牌来获取访问令牌。

- 客户端使用 OAuth 2.0 授权代码来获取访问令牌和刷新令牌。

- 用户 (SSO) 单一登录到 Azure AD 连接的 PC 上的 web 或 Windows 应用程序。

- 用户在移动设备上使用 FOCI 的客户端 Id (系列) 的会话时，登录到第二个 Microsoft Office 应用。




除了默认字段外，非交互式登录报表还显示： 

- 资源 ID

- 分组登录数




您无法自定义此报表中显示的字段。


![禁用的列](./media/concept-all-sign-ins/disabled-columns.png "禁用的列")

为了更轻松地对数据进行摘要，将对非交互式登录事件进行分组。 客户端通常会在短时间内代表同一个用户创建许多非交互式登录，这共享所有相同的特征，但尝试登录的时间除外。 例如，客户端可以代表用户每小时获取一个访问令牌。 如果用户或客户端未更改状态，则每个访问令牌请求的 IP 地址、资源和所有其他信息都是相同的。 当 Azure AD 记录与时间和日期完全相同的多个登录时，这些登录将来自同一实体，并聚合到单个行中。 除了发出) 的日期和时间外，具有多个相同登录 (的行在 # sign 列中的值将大于1。 您可以展开该行以查看所有不同的登录及其不同的时间戳。 当以下数据匹配时，将在非交互式用户中对登录进行聚合：


- 应用程序

- 用户

- IP 地址

- 状态

- 资源 ID


您可以：

- 展开节点可查看组中的单个项。  

- 单击单个项可查看所有详细信息 


![非交互式用户登录详细信息](./media/concept-all-sign-ins/non-interactive-sign-ins-details.png)




## <a name="service-principal-sign-ins"></a>服务主体登录

与交互式和非交互式用户登录不同，服务主体登录不涉及用户。 相反，它们是由任何非用户帐户（如 "应用" 或 "服务主体"）的登录 (除了托管标识登录（仅在 "托管标识登录" 报告中包含）) 。 在这些登录中，应用程序或服务提供自己的凭据（如证书或应用程序机密）来验证资源或访问资源。


**报表大小：** 大型 <br>
**示例：**

- 服务主体使用证书对 Microsoft Graph 进行身份验证和访问。 

- 应用程序使用客户端机密在 OAuth 客户端凭据流中进行身份验证。 


此报表有一个默认列表视图，其中显示：

- 登录日期

- 请求 ID

- 服务主体名称或 ID

- 状态

- IP 地址

- 资源名称

- 资源 ID

- 登录数

您无法自定义此报表中显示的字段。

![禁用的列](./media/concept-all-sign-ins/disabled-columns.png "禁用的列")

为了更轻松地对服务主体登录日志中的数据进行摘要，会将服务主体登录事件分组。 同一条件下的同一实体的登录将聚合到单个行中。 您可以展开该行以查看所有不同的登录及其不同的时间戳。 当以下数据匹配时，将在服务主体报表中聚合登录：

- 服务主体名称或 ID

- 状态

- IP 地址

- 资源名称或 ID

您可以：

- 展开节点可查看组中的单个项。  

- 单击单个项，查看所有详细信息 


![列详细信息](./media/concept-all-sign-ins/service-principals-sign-ins-view.png "列详细信息")




## <a name="managed-identity-for-azure-resources-sign-ins"></a>Azure 资源登录的托管标识 

Azure 资源的托管标识登录是由 Azure 管理其机密的资源执行的登录，用于简化凭据管理。

**报表大小：** 小规模 <br> 
**示例**

具有托管凭据的 VM 使用 Azure AD 获取访问令牌。   


此报表有一个默认列表视图，其中显示：


- 托管标识 ID

- 托管标识名称

- 资源

- 资源 ID

- 分组登录数

您无法自定义此报表中显示的字段。

为了更轻松地对数据进行摘要，Azure 资源登录日志、非交互式登录事件的分组。 将同一实体的登录聚合到单个行中。 您可以展开该行以查看所有不同的登录及其不同的时间戳。 当以下所有数据匹配时，将在 "托管标识" 报表中聚合登录：

- 托管标识名称或 ID

- 状态

- IP 地址

- 资源名称或 ID

在列表视图中选择一项以显示在节点下进行分组的所有登录。

选择某个分组项可查看登录的所有详细信息。 


## <a name="filter-sign-in-activities"></a>筛选登录活动

通过设置筛选器，可以缩小返回的登录数据的范围。 Azure AD 提供了一系列可以设置的其他筛选器。 设置筛选器时，应始终特别注意配置的 **日期** 范围筛选器。 正确的日期范围筛选器可确保 Azure AD 仅返回你真正关心的数据。     

使用 " **日期** 范围" 筛选器可以定义返回的数据的时间范围。
可能的值包括：

- 一个月

- 七天

- 二十四小时

- “自定义”

![日期范围筛选器](./media/concept-all-sign-ins/date-range-filter.png)





### <a name="filter-user-sign-ins"></a>筛选用户登录

交互式和非交互式登录的筛选器是相同的。 因此，为交互式登录配置的筛选器将持久保存，以实现非交互式登录，反之亦然。 






## <a name="access-the-new-sign-in-activity-reports"></a>访问新的登录活动报告 

Azure 门户中的 "登录活动" 报表提供了一个简单的方法来打开和关闭预览报表。 如果启用了预览报表，则会显示一个新菜单，使你能够访问所有登录活动报表类型。     


使用非交互式登录和应用程序登录访问新的登录报告： 

1. 在“Azure 门户”中，选择“Azure Active Directory”。

    ![选择 Azure AD](./media/concept-all-sign-ins/azure-services.png)

2. 在 " **监视** " 部分中，单击 " **登录**"。

    ![选择登录](./media/concept-all-sign-ins/sign-ins.png)

3. 单击 **预览** 栏。

    ![启用新视图](./media/concept-all-sign-ins/enable-new-preview.png)

4. 若要切换回默认视图，请再次单击 " **预览** " 栏。 

    ![还原经典视图](./media/concept-all-sign-ins/switch-back.png)







## <a name="download-sign-in-activity-reports"></a>下载登录活动报告

下载 "登录活动" 报表时，以下情况成立：

- 您可以将登录报表下载为 CSV 或 JSON 文件。

- 最多可下载 100-K 记录。 如果要下载更多数据，请使用报表 API。

- 下载内容基于所选筛选器。

- 可以下载的记录数受 [Azure Active Directory 报告保留策略](reference-reports-data-retention.md)的限制。 


![下载报表](./media/concept-all-sign-ins/download-reports.png "下载报表")


每个 CSV 下载包含六个不同的文件：

- 交互式登录

- 交互式登录的身份验证详细信息

- 非交互式登录

- 非交互式登录的身份验证详细信息

- 服务主体登录

- Azure 资源登录的托管标识

每个 JSON 下载都包含四个不同的文件：

- 交互式登录 (包括身份验证详细信息) 

- 非交互式登录 (包括身份验证详细信息) 

- 服务主体登录

- Azure 资源登录的托管标识

![下载文件](./media/concept-all-sign-ins/download-files.png "下载文件")




## <a name="next-steps"></a>后续步骤

* [登录活动报告错误代码](reference-sign-ins-error-codes.md)
* [Azure AD 数据保留策略](reference-reports-data-retention.md)
* [Azure AD 报告延迟](reference-reports-latencies.md)
