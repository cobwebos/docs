---
title: 将 Azure Monitor 工作簿与 Azure AD 域服务一起使用 |Microsoft Docs
description: 了解如何使用 Azure Monitor 工作簿来查看安全审核并了解 Azure Active Directory 域服务托管域中的问题。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: 879138d882913b8ab43c5689ff72a40e6987c104
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223034"
---
# <a name="review-security-audit-events-in-azure-active-directory-domain-services-using-azure-monitor-workbooks"></a>使用 Azure Monitor 工作簿查看 Azure Active Directory 域服务中的安全审核事件

为了帮助您了解 Azure Active Directory 域服务 (Azure AD DS) 托管域的状态，您可以启用安全审核事件。 然后，可以使用将文本、分析查询和参数合并到丰富的交互式报表 Azure Monitor 工作簿来查看这些安全审核事件。 Azure AD DS 包括安全概述和帐户活动的工作簿模板，使你能够深入了解审核事件并管理你的环境。

本文介绍如何使用 Azure Monitor 工作簿来查看 Azure AD DS 中的安全审核事件。

## <a name="before-you-begin"></a>准备阶段

需要拥有以下资源和权限才能完成本文中的操作：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请完成[创建和配置 Azure Active Directory 域服务托管域][create-azure-ad-ds-instance]的教程。
* 为托管域启用的将数据流式传输到 Log Analytics 工作区的安全审核事件。
    * 如果需要，[为 AZURE AD DS 启用安全审核][enable-security-audits]。

## <a name="azure-monitor-workbooks-overview"></a>Azure Monitor 工作簿概述

如果在 Azure AD DS 中打开安全审核事件，则很难分析和识别托管域中的问题。 Azure Monitor 允许聚合这些安全审核事件和查询数据。 通过 Azure Monitor 工作簿，可以可视化此数据，以更快、更轻松地识别问题。

工作簿模板是为多个用户和团队灵活重用而设计的特选报表。 打开工作簿模板时，将加载 Azure Monitor 环境中的数据。 您可以使用模板而不会影响组织中的其他用户，并且可以根据模板保存您自己的工作簿。

Azure AD DS 包含以下两个工作簿模板：

* 安全概述报表
* 帐户活动报表

有关如何编辑和管理工作簿的详细信息，请参阅[Azure Monitor 工作簿概述](../azure-monitor/platform/workbooks-overview.md)。

## <a name="use-the-security-overview-report-workbook"></a>使用安全概述报表工作簿

为了帮助你更好地了解使用情况和确定潜在的安全威胁，安全概述报表汇总了登录数据，并标识了你可能想要检查的帐户。 您可以查看特定日期范围内的事件，并向下钻取到特定的登录事件，例如密码尝试错误或帐户被禁用的位置。

若要访问 "安全概述" 报表的工作簿模板，请完成以下步骤：

1. 搜索并选择 Azure 门户中**Azure Active Directory 域服务**。
1. 选择托管域，如*aaddscontoso.com*
1. 从左侧菜单中选择 "**监视" > 工作簿**

    ![在 Azure 门户中选择 "工作簿" 菜单选项](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. 选择 "**安全概述" 报表**。
1. 从工作簿顶部的下拉菜单中，选择 Azure 订阅，然后选择 "Azure Monitor" 工作区。

    选择**时间范围**（例如*过去7天*），如以下示例屏幕截图所示：

    ![在 Azure 门户中选择 "工作簿" 菜单选项](./media/use-azure-monitor-workbooks/select-query-filters.png)

    还可以更改**磁贴视图**和**图表视图**选项，以根据需要分析和可视化数据。

1. 若要向下钻取到特定事件类型，请选择某个**登录结果**卡，如 "*帐户已锁定*"，如以下示例中所示：

    ![Azure Monitor 工作簿中可视化的安全概述报表示例](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. 图表下面的安全性概述报表下半部分将细分所选的活动类型。 可以按右侧所涉及的用户名进行筛选，如以下示例报表所示：

    [![](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png "Details of account lockouts in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>使用 "帐户活动报表" 工作簿

为帮助您解决特定用户帐户的问题，帐户活动报告会分解详细的审核事件日志信息。 你可以查看在登录时提供了错误的用户名或密码，以及登录尝试的源。

若要访问 "帐户活动" 报表的工作簿模板，请完成以下步骤：

1. 搜索并选择 Azure 门户中**Azure Active Directory 域服务**。
1. 选择托管域，如*aaddscontoso.com*
1. 从左侧菜单中选择 "**监视" > 工作簿**
1. 选择 "**帐户活动" 报表**。
1. 从工作簿顶部的下拉菜单中，选择 Azure 订阅，然后选择 "Azure Monitor" 工作区。

    选择**时间范围**（如 "*过去30天*"），然后选择 "**磁贴" 视图**表示数据的方式。

    可以按**帐户用户名**（如*felix*）进行筛选，如以下示例报表所示：

    [![](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png "Account activity report in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    图表下面的区域显示各个登录事件以及活动结果和源工作站等信息。 此信息可帮助确定可能会导致帐户锁定或指示潜在攻击的登录事件的重复源。

与安全概述报表一样，您可以向下钻取到报表顶部的不同磁贴，以可视化和分析数据。

## <a name="save-and-edit-workbooks"></a>保存和编辑工作簿

Azure AD DS 提供的两个模板工作簿是开始使用自己的数据分析的好地方。 如果需要在数据查询和调查中获得更精细的信息，可以保存自己的工作簿并编辑查询。

1. 若要保存某个工作簿模板的副本，请选择 "编辑" " **> 另存为 > 共享报表**"，然后提供一个名称并保存。
1. 从你自己的模板副本中，选择 "**编辑**" 以进入编辑模式。 您可以选择报表的任何部分旁边的蓝色 "**编辑**" 按钮，并对其进行更改。

使用 Kusto 查询生成 Azure Monitor 工作簿中的所有图表和表。 有关创建自己的查询的详细信息，请参阅[Azure Monitor 日志查询][azure-monitor-queries]和[Kusto 查询教程][kusto-queries]。

## <a name="next-steps"></a>后续步骤

如果需要调整密码和锁定策略，请参阅[托管域上的密码和帐户锁定策略][password-policy]。

有关用户的问题，请了解如何排查[帐户登录问题][troubleshoot-sign-in]或[帐户锁定问题][troubleshoot-account-lockout]。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-security-audits]: security-audit-events.md
[password-policy]: password-policy.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-monitor-queries]: ../azure-monitor/log-query/query-language.md
[kusto-queries]: https://docs.microsoft.com/azure/kusto/query/tutorial?pivots=azuredataexplorer
