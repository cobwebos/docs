---
title: Azure 开发人员测试实验室跨多个实验室和订阅的使用
description: 了解如何报告跨多个实验室和订阅的 Azure 开发人员测试实验室使用情况。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169185"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>报告跨多个实验室和订阅的 Azure 开发人员测试实验室使用情况

大多数大型组织希望通过可视化使用中的趋势和异常值来跟踪资源使用情况，从而更有效地利用这些资源。 根据资源使用情况，实验室所有者或经理可以自定义实验室以提高[资源使用和成本](https://docs.microsoft.com/azure/billing/billing-getting-started)。 在 Azure 开发人员测试实验室中，您可以按每个实验室下载资源使用情况，从而更深入地了解使用模式。 这些使用模式可以帮助确定更改，以提高效率。 大多数企业都希望跨[多个实验室和订阅](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/)使用单个实验室和总体使用情况。 

本文讨论了如何处理跨多个实验室和订阅的资源使用信息。

![报告使用情况](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>单个实验室使用情况

本节讨论如何导出单个实验室的资源使用情况。

在导出 DevTest Labs 的资源使用情况之前，必须设置 Azure 存储帐户，以允许存储包含使用情况数据的不同文件。 执行数据导出有两种常见方法：

* [开发测试实验室 REST API](https://docs.microsoft.com/rest/api/dtl/labs/exportresourceusage) 
* PowerShell Az.Resource 模块[Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction?view=azps-2.5.0&viewFallbackFrom=azps-2.3.2)具有`exportResourceUsage`的 操作，实验室资源 ID 和必要的参数。 

    [导出或删除个人数据](personal-data-delete-export.md)文章包含一个示例 PowerShell 脚本，其中包含有关导出数据的详细信息。 

    > [!NOTE]
    > 日期参数不包含时间戳，因此数据包含基于实验室所在的时区的午夜的所有内容。

导出完成后，Blob 存储中将有多个带有不同资源信息的 CSV 文件。
  
目前有两个 CSV 文件：

* *虚拟机.csv* - 包含有关实验室中虚拟机的信息
* *磁盘.csv* - 包含有关实验室中不同磁盘的信息 

这些文件存储在*实验室资源使用*blob 容器中，其名称为实验室名称、实验室唯一 ID、执行日期以及导出请求中的完整日期或开始日期。 一个示例 blob 结构是：

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>导出所有实验室的使用情况

要导出多个实验室的使用情况信息，请考虑使用 

* [Azure 函数](https://docs.microsoft.com/azure/azure-functions/)，提供多种语言版本，包括 PowerShell，或 
* [Azure 自动化运行簿](https://docs.microsoft.com/azure/automation/)，使用 PowerShell、Python 或自定义图形设计器编写导出代码。

使用这些技术，您可以在特定的日期和时间在所有实验室上执行单个实验室导出。 

Azure 函数应将数据推送到长期存储。 为多个实验室导出数据时，导出可能需要一些时间。 为了帮助提高性能并减少信息重复的可能性，我们建议并行执行每个实验室。 要完成并行性，可以异步运行 Azure 函数。 还利用 Azure 函数提供的计时器触发器。

## <a name="using-a-long-term-storage"></a>使用长期存储

长期存储将来自不同实验室的导出信息合并到单个数据源中。 使用长期存储的另一个好处是能够从存储帐户中删除文件，以减少重复和成本。 

长期存储可用于执行任何文本操作，例如： 

* 添加友好名称
* 创建复杂的分组
* 聚合数据。

一些常见的存储解决方案是[：SQL 服务器](https://azure.microsoft.com/services/sql-database/)[、Azure 数据湖](https://azure.microsoft.com/services/storage/data-lake-storage/)和[Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)。 选择您选择的长期存储解决方案取决于偏好。 在可视化数据时，您可以考虑根据工具在交互可用性方面提供的内容来选择该工具。

## <a name="visualizing-data-and-gathering-insights"></a>可视化数据和收集见解

使用您选择的数据可视化工具连接到长期存储以显示使用情况数据并收集见解以验证使用效率。 例如[，Power BI](https://docs.microsoft.com/power-bi/power-bi-overview)可用于组织和显示使用情况数据。 

可以使用[Azure 数据工厂](https://azure.microsoft.com/services/data-factory/)在单个位置界面中创建、链接和管理资源。 如果需要更大的控制，可以在单个资源组中创建单个资源，并且独立于数据工厂服务进行管理。  

## <a name="next-steps"></a>后续步骤

一旦系统建立，数据移动到长期存储，下一步是提出数据需要回答的问题。 例如： 

-   VM 大小使用情况是什么？

    用户选择高性能（更昂贵的）VM 大小吗？
-   正在使用哪些应用商店映像？

    自定义映像是最常见的 VM 基础，应像[共享映像库](../virtual-machines/windows/shared-image-galleries.md)或[映像工厂](image-factory-create.md)一样构建公共映像存储。
-   正在使用或不使用哪些自定义映像？
