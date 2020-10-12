---
title: 将 Azure Monitor 工作簿与 Azure AD 域服务结合使用 | Microsoft Docs
description: 了解如何使用 Azure Monitor 工作簿在 Azure Active Directory 域服务托管域中查看安全审核并了解问题。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: 280b7e051c4c9d87c504c1abf6aca0b04bfa1b29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91704593"
---
# <a name="review-security-audit-events-in-azure-active-directory-domain-services-using-azure-monitor-workbooks"></a>使用 Azure Monitor 工作簿查看 Azure Active Directory 域服务中的安全审核事件

为了帮助你了解 Azure Active Directory 域服务 (Azure AD DS) 托管域中的状态，你可以启用安全审核事件。 然后可以使用 Azure Monitor 工作簿查看这些安全审核事件，工作簿可将文本、分析查询和参数合并到丰富的交互式报表。 Azure AD DS 包括安全概述和帐户活动的工作簿模板，使你能够深入了解审核事件并管理你的环境。

本文介绍如何使用 Azure Monitor 工作簿来查看 Azure AD DS 中的安全审核事件。

## <a name="before-you-begin"></a>准备阶段

需有以下资源和特权才能完成本文：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请完成[创建并配置 Azure Active Directory 域服务托管域][create-azure-ad-ds-instance]的教程。
* 为托管域启用的安全审核事件，可将数据流式传输到 Log Analytics 工作区。
    * 如果需要，[为 Azure AD DS 启用安全审核][enable-security-audits]。

## <a name="azure-monitor-workbooks-overview"></a>Azure Monitor 工作簿概述

如果在 Azure AD DS 中启用安全审核事件，则很难分析和确定托管域中的问题。 通过 Azure Monitor，你可以聚合这些安全审核事件并查询数据。 通过 Azure Monitor 工作簿，你可以可视化此数据，从而更快、更轻松地确定问题。

工作簿模板是特选的报表，可供多个用户和团队灵活重复使用。 打开工作簿模板时，将加载 Azure Monitor 环境中的数据。 可以在不影响组织中其他用户的情况下使用模板，并且可以根据模板保存自己的工作簿。

Azure AD DS 包含以下两个工作簿模板：

* 安全概述报表
* 帐户活动报表

有关如何编辑和管理工作簿的详细信息，请参阅 [Azure Monitor 工作簿概述](../azure-monitor/platform/workbooks-overview.md)。

## <a name="use-the-security-overview-report-workbook"></a>使用安全概述报表工作簿

为了帮助你更好地了解使用情况和确定潜在的安全威胁，安全概述报表汇总了登录数据，并标识了你可能想要检查的帐户。 可以查看特定日期范围内的事件，并向下钻取到特定的登录事件，例如错误的密码尝试或帐户被禁用的位置。

若要访问安全概述报表的工作簿模板，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择“Azure Active Directory 域服务”。
1. 选择你的托管域，例如 aaddscontoso.com
1. 从左侧的菜单中选项“监视”>“工作簿”

    ![Hightlights 屏幕截图，可在其中选择 "安全概述" 报表和 "帐户活动" 报表。](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. 选择“安全概述报表”。
1. 从工作簿顶部的下拉菜单中，选择 Azure 订阅，然后选择一个 Azure Monitor 工作区。

    选择“时间范围”，如“过去 7 天”，如以下示例屏幕截图所示：

    ![在 Azure 门户中选择“工作簿”菜单选项](./media/use-azure-monitor-workbooks/select-query-filters.png)

    也可更改“平铺视图”和“图表视图”选项，以根据需要分析和可视化数据 。

1. 若要向下钻取到特定的事件类型，请选择“登录结果”卡片之一，例如“帐户已锁定”，如以下示例中所示：

    ![在 Azure Monitor 工作簿中可视化的安全概述报表数据示例](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. 然后图表下面的安全概述报表的下半部分将细分所选的活动类型。 可以按右侧包含的用户名进行筛选，如以下示例报表所示：

    [![Azure Monitor 工作簿中帐户锁定的详细信息。](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png)](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>使用帐户活动报表工作簿

为帮助你排查特定用户帐户的问题，帐户活动报表将细分详细的审核事件日志信息。 可以检查在登录时是否提供了错误的用户名或密码，以及登录尝试的来源。

若要访问帐户活动报表的工作簿模板，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择“Azure Active Directory 域服务”。
1. 选择你的托管域，例如 aaddscontoso.com
1. 从左侧的菜单中选项“监视”>“工作簿”
1. 选择“帐户活动报表”。
1. 从工作簿顶部的下拉菜单中，选择 Azure 订阅，然后选择一个 Azure Monitor 工作区。

    选择“时间范围”，例如“最近 30 天”，然后选择希望“平铺视图”显示数据的方式。

    可以按“帐户用户名”（如 felix）进行筛选，如以下示例报表所示：

    [![Azure Monitor 工作簿中的帐户活动报表。](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png)](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    图表下面的区域显示各个登录事件以及活动结果和源工作站等信息。 此信息可帮助确定重复的登录事件源，这些事件可能会导致帐户锁定或指示潜在攻击。

与安全概述报表一样，也可向下钻取到报表顶部的不同磁贴，进而根据需要可视化和分析数据。

## <a name="save-and-edit-workbooks"></a>保存和编辑工作簿

Azure AD DS 提供的两个模板工作簿适合于开始进行数据分析。 如果需要在数据查询和调查中获得更精确的信息，可以保存自己的工作簿并编辑查询。

1. 若要保存其中一个工作簿模板的副本，请选择“编辑”>“另存为”>“共享报表”，然后提供名称并保存。
1. 从你自己的模板副本中，选择“编辑”以进入编辑模式。 可以选择报表中任何部分旁边的蓝色“编辑”按钮，并对其进行更改。

Azure Monitor 工作簿中的所有图表和表都是使用 Kusto 查询生成的。 有关创建自己的查询的详细信息，请参阅 [Azure Monitor 日志查询][azure-monitor-queries] 和 [Kusto 查询教程][kusto-queries]。

## <a name="next-steps"></a>后续步骤

如果需要调整密码和锁定策略，请参阅[托管域中的密码和帐户锁定策略][password-policy]。

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
[kusto-queries]: /azure/kusto/query/tutorial?pivots=azuredataexplorer