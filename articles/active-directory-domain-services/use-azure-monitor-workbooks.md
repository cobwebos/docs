---
title: 将 Azure 监视器工作簿与 Azure AD 域服务一起使用 |微软文档
description: 了解如何使用 Azure 监视器工作簿查看安全审核并了解 Azure 活动目录域服务托管域中的问题。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: iainfou
ms.openlocfilehash: 4bf6926651a0e2e9289b3c729abea16b1c66bce6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80108635"
---
# <a name="review-security-audit-events-in-azure-ad-domain-services-using-azure-monitor-workbooks"></a>使用 Azure 监视器工作簿查看 Azure AD 域服务中的安全审核事件

为了帮助您了解 Azure 活动目录域服务 （Azure AD DS） 托管域的状态，可以启用安全审核事件。 然后，可以使用 Azure 监视器工作簿检查这些安全审核事件，这些工作簿将文本、分析查询和参数合并为丰富的交互式报表。 Azure AD DS 包括用于安全概述和帐户活动的工作簿模板，这些模板可让您深入了解审核事件并管理环境。

本文介绍如何使用 Azure 监视器工作簿查看 Azure AD DS 中的安全审核事件。

## <a name="before-you-begin"></a>开始之前

要完成本文，您需要以下资源和特权：

* 一个有效的 Azure 订阅。
    * 如果没有 Azure 订阅，[请创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请完成创建[和配置 Azure 活动目录域服务实例][create-azure-ad-ds-instance]的教程。
* 为 Azure 活动目录域服务托管域启用了安全审核事件，这些域将数据流式传输到日志分析工作区。
    * 如果需要，[请为 Azure 活动目录域服务启用安全审核][enable-security-audits]。

## <a name="azure-monitor-workbooks-overview"></a>Azure 监视器工作簿概述

在 Azure AD DS 中打开安全审核事件时，可能很难分析和识别托管域中的问题。 Azure 监视器允许您聚合这些安全审核事件并查询数据。 使用 Azure 监视器工作簿，可以可视化此数据，以便更快、更轻松地识别问题。

工作簿模板是精心策划的报表，专为多个用户和团队灵活重用而设计。 打开工作簿模板时，将加载 Azure 监视器环境中的数据。 您可以使用模板而不影响组织中的其他用户，还可以根据模板保存您自己的工作簿。

Azure AD DS 包括以下两个工作簿模板：

* 安全概述报告
* 帐户活动报告

有关如何编辑和管理工作簿的详细信息，请参阅[Azure 监视器工作簿概述](../azure-monitor/platform/workbooks-overview.md)。

## <a name="use-the-security-overview-report-workbook"></a>使用安全概述报告工作簿

为了帮助您更好地了解使用情况和识别潜在的安全威胁，安全概述报告汇总了登录数据并标识了可能需要检查的帐户。 您可以查看特定日期范围内的事件，并深入了解特定的登录事件，例如密码尝试错误或帐户被禁用的位置。

要访问安全概述报告的工作簿模板，完成以下步骤：

1. 在 Azure 门户中搜索并选择**Azure 活动目录域服务**。
1. 选择托管域，例如*aaddscontoso.com*
1. 从左侧的菜单中选择 **"监视>工作簿**

    ![在 Azure 门户中选择"工作簿"菜单选项](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. 选择**安全概述报告**。
1. 从工作簿顶部的下拉菜单中，选择 Azure 订阅，然后选择 Azure 监视器工作区。 选择**时间范围**，如最近*7 天*。

    ![在 Azure 门户中选择"工作簿"菜单选项](./media/use-azure-monitor-workbooks/select-query-filters.png)

    也可以更改 **"磁贴视图**"和 **"图表"视图**选项，以便根据需要分析和可视化数据

1. 要向下钻取到特定事件类型，请选择**登录结果**卡片之一，如 *"帐户锁定"，* 如以下示例所示：

    ![示例安全概述 报表在 Azure 监视器工作簿中可视化的数据](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. 图表下方的安全概览报告的下半部分将细分所选活动类型。 您可以按右侧涉及的用户名进行筛选，如以下示例报告所示：

    [![](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png "Details of account lockouts in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>使用帐户活动报表工作簿

为了帮助您解决特定用户帐户的问题，帐户活动报告会分解详细的审核事件日志信息。 您可以查看登录期间何时提供了错误的用户名或密码，以及登录尝试的来源。

要访问帐户活动报表的工作簿模板，完成以下步骤：

1. 在 Azure 门户中搜索并选择**Azure 活动目录域服务**。
1. 选择托管域，例如*aaddscontoso.com*
1. 从左侧的菜单中选择 **"监视>工作簿**
1. 选择**帐户活动报告**。
1. 从工作簿顶部的下拉菜单中，选择 Azure 订阅，然后选择 Azure 监视器工作区。 选择**时间范围**，如最近*30 天*，然后选择希望**磁贴视图**如何表示数据。 您可以按**帐户用户名**（如*felix*） 进行筛选，如以下示例报告所示：

    [![](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png "Account activity report in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    图表下方的区域显示单个登录事件以及活动结果和源工作站等信息。 此信息可帮助确定可能导致帐户锁定或指示潜在攻击的登录事件的重复源。

与安全概述报表一样，您可以向下钻取报表顶部的不同磁贴，以便根据需要可视化和分析数据。

## <a name="save-and-edit-workbooks"></a>保存和编辑工作簿

Azure AD DS 提供的两个模板工作簿是一个从您自己的数据分析开始的好地方。 如果需要在数据查询和调查中更精细，可以保存自己的工作簿并编辑查询。

1. 要保存其中一个工作簿模板的副本，请选择 **"编辑>保存为>共享报表**，然后提供名称并保存它。
1. 从您自己的模板副本中，选择 **"编辑"** 以进入编辑模式。 您可以选择报表任何部分旁边的蓝色 **"编辑"** 按钮并进行更改。

Azure 监视器工作簿中的所有图表和表都是使用 Kusto 查询生成的。 有关创建自己的查询的详细信息，请参阅[Azure 监视器日志查询][azure-monitor-queries]和[Kusto 查询教程][kusto-queries]。

## <a name="next-steps"></a>后续步骤

如果需要调整密码和锁定策略，请参阅[托管域上的密码和帐户锁定策略][password-policy]。

对于用户的问题，了解如何解决[帐户登录问题][troubleshoot-sign-in]或[帐户锁定问题][troubleshoot-account-lockout]。

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
