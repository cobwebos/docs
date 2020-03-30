---
title: 请求增加配额并获得支持
description: 如何在 Azure 门户中为 Azure 同步分析创建支持请求。 请求增加配额或获得问题解决支持。
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: eaa6ceded9893b278b28d517cbddb303f8469e10
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350900"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>请求增加配额并获得对 Azure 突触分析的支持

本文介绍如何在 Azure 门户中提交 Azure 突触分析的支持票证。 此过程使您能够请求增加配额或为工程支持团队提交技术支持请求。

## <a name="create-a-support-ticket"></a>创建支持票证

使用以下步骤从 Azure 门户为 Azure 突触分析创建新的支持请求。

1. 在[Azure 门户](https://portal.azure.com)菜单上，选择 **"帮助 + 支持**"。

   !["帮助+支持"链接](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. 在 **"帮助+支持"** 中，选择 **"新建支持请求**"。

    ![创建新的支持请求](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. 查看[Azure 支持计划](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)。

   * **计费、配额和订阅管理**支持适用于所有支持级别。
   * **中断修复**支持通过[开发人员](https://azure.microsoft.com/support/plans/developer/)、[标准](https://azure.microsoft.com/support/plans/standard/)、[专业直接](https://azure.microsoft.com/support/plans/prodirect/)或[高级](https://azure.microsoft.com/support/plans/premier/)支持提供。 故障维修服务问题是客户在使用 Azure 时遇到的确定是由 Microsoft 引起的问题。
   * 我们在[专业直接支持](https://azure.microsoft.com/support/plans/prodirect/)和[顶级支持](https://azure.microsoft.com/support/plans/premier/)级别提供**开发人员辅导**与**咨询服务**。

   如果您有高级支持计划，还可以在[Microsoft Premier 在线门户](https://premier.microsoft.com/)上报告 Azure 同步分析问题。 请参阅[Azure 支持计划](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)，了解有关各种支持计划（包括范围、响应时间、定价等）详细信息。 有关 Azure 支持的常见问题，请参阅[Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。

1. 对于**问题类型**，请选择适当的问题类型。 对于中断修复问题，请选择 **"技术**"。 对于增加配额的请求，请选择 **"服务和订阅限制"（配额）。**

   ![选择问题类型](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > 本文的其余部分侧重于增加配额的请求。 但是，您也可以在此处选择 **"技术"** 以用于问题解决支持请求。 如果选择 **"技术**"，系统将要求您提供摘要，然后通过选择 **"选择问题类型"来识别问题类型**。 您可能会看到解决方案来帮助解决问题。 如果提供的解决方案无法解决您的问题，请选择 **"下一步：详细信息**"并填写表单以提交支持票证。

1. 对于增加配额的请求，选择**配额类型的** **Azure 突触分析**。 然后选择 **"下一步：解决方案 >>。 **

   ![选择配额类型](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. 在 **"详细信息"** 窗口中，选择 **"提供详细信息**"以输入其他信息。

   !["提供详细信息"链接](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>配额请求类型

单击 **"提供详细信息**"将显示允许您添加其他信息的**配额详细信息**窗口。 以下各节介绍可用于 Azure 同步分析的不同配额请求。

### <a name="data-warehouse-units-dwus-per-server"></a>每个服务器的数据仓库单位 （DWU）

使用以下步骤请求增加每个服务器的 DWU。

1. 根据服务器配额类型选择**数据仓库单位 （DTUs）。**

1. 在 **"资源"** 列表中，选择要定位的资源。

1. 在 **"请求配额"** 字段中，输入您请求的新 DWU 限制。

   ![DWU 配额详细信息](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>每个订阅的服务器

使用以下步骤请求增加每个订阅的服务器数。

1. **选择每个订阅**配额类型的服务器。

1. 在 **"位置**"列表中，选择要使用的 Azure 区域。 配额是每个区域中每个订阅的配额。

1. 在"**新建配额"** 字段中，输入对该区域中服务器最大数量的请求。

   ![服务器配额详细信息](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>启用对区域的订阅访问

某些产品/服务类型并非在每个区域都可用。 您可能会看到如下错误：

`This location is not available for subscription`

如果您的订阅需要在特定区域进行访问，请使用 **"其他配额请求**"选项请求访问。 在请求中，指定要为该区域启用的产品/服务以及 SKU 详细信息。 要浏览产品/服务列表和 SKU 选项，请参阅[Azure 突触分析定价](https://azure.microsoft.com/pricing/details/synapse-analytics/)。

![其他配额详细信息](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>提交请求

最后一步是填写 SQL 数据库支持请求的剩余详细信息。 然后选择 **"下一步：审阅 + 创建>>**"，在查看请求详细信息后，单击"**创建"** 以提交请求。

## <a name="monitor-a-support-ticket"></a>监视支持票证

提交支持请求后，Azure 支持团队将与您联系。 若要查看请求状态和详细信息，请单击仪表板上的“所有支持请求”****。

![查看状态](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>其他资源

您还可以在[堆栈溢出](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse)或通过[Azure SQL 数据仓库 MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/)与 Azure 同步分析社区连接。

