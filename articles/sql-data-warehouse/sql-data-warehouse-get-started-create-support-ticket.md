---
title: 请求配额增加并获得支持
description: 如何在 Azure Synapse Analytics 的 Azure 门户中创建支持请求。 请求配额增加或获取问题解决支持。
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 88cc737fddfdd896575227e036df087c13a4d0f6
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79130104"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>请求配额增加并获得 Azure Synapse Analytics 支持

本文介绍如何在 Azure Synapse Analytics 的 Azure 门户中提交支持票证。 利用此过程，你可以请求增加配额或提交技术支持团队。

## <a name="create-a-support-ticket"></a>创建支持票证

使用以下步骤从 Azure Synapse Analytics Azure 门户创建新的支持请求。

1. 在 " [Azure 门户](https://portal.azure.com)" 菜单上，选择 "**帮助 + 支持**"。

   !["帮助 + 支持" 链接](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. 在 "**帮助 + 支持**" 中，选择 "**新建支持请求**"。

    ![创建新的支持请求](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. 查看[Azure 支持计划](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)。

   * **计费、配额和订阅管理**支持适用于所有支持级别。
   * **中断-修复**支持通过[开发人员](https://azure.microsoft.com/support/plans/developer/)、[标准](https://azure.microsoft.com/support/plans/standard/)、[专业直接](https://azure.microsoft.com/support/plans/prodirect/)或[顶级](https://azure.microsoft.com/support/plans/premier/)支持提供。 故障维修服务问题是客户在使用 Azure 时遇到的确定是由 Microsoft 引起的问题。
   * **开发人员辅导**和**咨询服务**在[专业直接](https://azure.microsoft.com/support/plans/prodirect/)和[顶级](https://azure.microsoft.com/support/plans/premier/)支持级别提供。

   如果你有顶级支持计划，还可以在[Microsoft Premier online 门户](https://premier.microsoft.com/)上报告 Azure Synapse Analytics 问题。 若要深入了解各种支持计划（包括范围、响应时间、定价等），请参阅[Azure 支持计划](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)。 有关 Azure 支持的常见问题，请参阅[azure 支持常见问题](https://azure.microsoft.com/support/faq/)。

1. 对于 "**问题类型**"，请选择相应的问题类型。 有关中断修复问题，请选择 "**技术**"。 对于 "配额增加请求"，请选择 "**服务和订阅限制（配额）** "。

   ![选择问题类型](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > 本文的剩余部分重点介绍了配额增加的请求。 不过，你也可以在此处选择 "**技术**支持" 以获取问题解决支持请求。 如果选择 "**技术**"，系统会要求提供摘要，然后选择 "**选择问题类型**" 来确定问题类型。 你可能会看到用于帮助解决问题的解决方案。 如果提供的解决方案无法解决问题，请选择 "**下一步：详细信息**" 并填写表单以提交支持票证。

1. 对于 "配额增加请求"，请选择 " **Azure Synapse Analytics** " 作为 "**配额类型**"。 然后选择 "**下一步：解决方案" > >** "。

   ![选择配额类型](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. 在**详细信息**窗口中，选择 "**提供详细**信息" 以输入其他信息。

   !["提供详细信息" 链接](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>配额请求类型

单击 "**提供详细信息**" 将显示允许您添加其他信息的 "**配额详细信息**" 窗口。 以下各节介绍可用于 Azure Synapse Analytics 的不同配额请求。

### <a name="data-warehouse-units-dwus-per-server"></a>每台服务器的数据仓库单位（Dwu）

使用以下步骤来请求增加每个服务器的 Dwu。

1. 选择**每个服务器的数据仓库单位（dtu）** 配额类型。

1. 在 "**资源**" 列表中，选择要作为目标的资源。

1. 在 "**请求配额**" 字段中，输入你请求的新 DWU 限制。

   ![DWU 配额详细信息](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>每个订阅的服务器

使用以下步骤来请求增加每个订阅的服务器数量。

1. 选择 "**每个订阅的服务器**" 配额类型。

1. 在 "**位置**" 列表中，选择要使用的 Azure 区域。 配额是每个区域中的每个订阅。

1. 在 "**新配额**" 字段中，输入你对该区域中服务器的最大数量的请求。

   ![服务器配额详细信息](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>启用对区域的订阅访问

某些产品/服务类型在每个区域中都不可用。 你可能会看到如下所示的错误：

`This location is not available for subscription`

如果你的订阅需要特定区域的访问权限，请使用**其他配额请求**选项来请求访问权限。 在请求中，指定要为区域启用的产品/服务和 SKU 详细信息。 若要浏览产品/服务和 SKU 选项，请参阅[Azure Synapse Analytics 定价](https://azure.microsoft.com/pricing/details/synapse-analytics/)。

![其他配额详细信息](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>提交请求

最后一步是填写 SQL 数据库支持请求的其他详细信息。 然后选择 "**下一步"：查看和创建 > >** ，查看请求详细信息后，单击 "**创建**" 以提交请求。

## <a name="monitor-a-support-ticket"></a>监视支持票证

提交支持请求后，Azure 支持团队将与你联系。 若要查看请求状态和详细信息，请单击仪表板上的“所有支持请求”。

![查看状态](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>其他资源

你还可以在[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse)上或通过[Azure SQL 数据仓库 MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/)连接到 azure Synapse Analytics 社区。

