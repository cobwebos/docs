---
title: 跨多个实验室和订阅的 Azure 开发测试实验室使用情况
description: 了解如何跨多个实验室和订阅报告 Azure 开发测试实验室的使用情况。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: takamath
ms.openlocfilehash: 912f510f6380c0ba1eb92b7c485091801123558e
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169185"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>跨多个实验室和订阅报告 Azure 开发测试实验室的使用情况

大多数大型组织希望通过在使用中可视化趋势和离群值，来跟踪资源使用情况，以便更有效地处理这些资源。 根据资源使用情况，实验室所有者或经理可自定义实验室以[提高资源使用和成本](https://docs.microsoft.com/azure/billing/billing-getting-started)。 在 Azure 开发测试实验室中，可以下载每个实验室的资源使用情况，以便更深入地了解使用模式。 这些使用模式可以帮助查明更改以提高效率。 大多数企业需要单独的实验室使用情况和跨[多个实验室和订阅](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/)的总体使用情况。 

本文介绍如何跨多个实验室和订阅处理资源使用情况信息。

![报告使用情况](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>个别实验室使用情况

本部分介绍如何导出单个实验室的资源使用情况。

必须先设置 Azure 存储帐户，以允许存储包含使用情况数据的不同文件，然后才能导出开发测试实验室的资源使用情况。 可以通过两种常见方法来执行数据导出：

* [开发测试实验室 REST API](https://docs.microsoft.com/rest/api/dtl/labs/exportresourceusage) 
* PowerShell Az [AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction?view=azps-2.5.0&viewFallbackFrom=azps-2.3.2)模块的操作为 `exportResourceUsage`、实验室资源 ID 和必要的参数。 

    [导出或删除个人数据](personal-data-delete-export.md)一文包含一个示例 PowerShell 脚本，其中包含有关导出的数据的详细信息。 

    > [!NOTE]
    > Date 参数不包含时间戳，因此数据包含从午夜开始的所有内容，具体取决于实验室所在的时区。

导出完成后，blob 存储中会有多个 CSV 文件，其中包含不同的资源信息。
  
当前有两个 CSV 文件：

* *virtualmachines* -包含有关实验室中的虚拟机的信息
* *磁盘 .csv* -包含有关实验室中不同磁盘的信息 

这些文件存储在 "实验室名称"、"实验室唯一 ID"、"已执行日期" 和 "完整" 或 "基于导出请求的开始日期" 下的*labresourceusage* blob 容器中。 示例 blob 结构为：

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>为所有实验室导出使用情况

若要为多个实验室导出使用情况信息，请考虑使用 

* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/)，可通过多种语言（包括 PowerShell）或 
* [Azure 自动化 runbook](https://docs.microsoft.com/azure/automation/)，使用 PowerShell、Python 或自定义图形设计器来编写导出代码。

使用这些技术，可以在特定的日期和时间对所有实验室执行单个实验室导出。 

Azure 函数应将数据推送到长期存储。 导出多个实验室的数据时，导出可能需要一些时间。 为了帮助提高性能和降低信息重复的可能性，建议并行执行每个实验室。 若要完成并行，请异步运行 Azure Functions。 还可利用 Azure Functions 提供的计时器触发器。

## <a name="using-a-long-term-storage"></a>使用长期存储

长期存储将来自不同实验室的导出信息合并到单个数据源。 使用长期存储的另一个好处是可以从存储帐户中删除这些文件，以减少重复和成本。 

长期存储可用于执行任何文本操作，例如： 

* 添加友好名称
* 创建复杂分组
* 聚合数据。

一些常见的存储解决方案是： [SQL Server](https://azure.microsoft.com/services/sql-database/)、 [Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)和[Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)。 选择所选的长期存储解决方案取决于首选项。 你可以考虑根据可视化数据时的交互可用性来选择工具。

## <a name="visualizing-data-and-gathering-insights"></a>可视化数据和收集见解

使用所选的数据可视化工具连接到长期存储，以显示使用情况数据并收集见解以验证使用效率。 例如，可以使用[Power BI](https://docs.microsoft.com/power-bi/power-bi-overview)来组织和显示使用情况数据。 

可以使用[Azure 数据工厂](https://azure.microsoft.com/services/data-factory/)在单个位置接口中创建、链接和管理资源。 如果需要更多控制，则可以在单个资源组中创建单个资源，并独立于数据工厂服务进行管理。  

## <a name="next-steps"></a>后续步骤

设置系统并将数据转移到长期存储后，下一步就是提出数据需要回答的问题。 例如： 

-   什么是 VM 大小使用情况？

    用户是否选择高性能（更昂贵）的 VM 大小？
-   正在使用哪些 Marketplace 映像？

    为自定义映像最常见的 VM 基准，应生成类似于[共享映像库](../virtual-machines/windows/shared-image-galleries.md)或[映像工厂](image-factory-create.md)的通用映像存储。
-   正在使用或未使用哪些自定义映像？
