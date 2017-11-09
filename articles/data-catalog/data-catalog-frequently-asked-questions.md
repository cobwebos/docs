---
title: "Azure 数据目录常见问题解答 | Microsoft Docs"
description: "有关 Azure 数据目录的常见问题，包括为数据源发现功能、批注功能和管理功能。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 5c7e209a-458c-4bb4-96bb-7ed178f9528a
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 10/15/2017
ms.author: maroche
ms.openlocfilehash: 7aa0f9b292a98c6137ecf4ae957b3cb86062f250
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2017
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Azure 数据目录常见问题
本文将解答有关 Azure 数据目录服务的常见问题。

## <a name="what-is-azure-data-catalog"></a>什么是 Azure 数据目录？
托管在 Microsoft Azure 中的数据目录是一个完全托管的服务，充当企业数据源的注册和发现系统。 任何用户（从分析师到数据专家，再到开发人员）都可以使用数据目录来注册、发现、理解和使用数据源。

## <a name="what-customer-challenges-does-it-solve"></a>它解决了客户面临的哪些难题？
数据目录解决数据源发现和“暗数据”难题，使用户能够发现和理解企业数据源。

## <a name="what-are-its-target-audiences"></a>其目标受众是什么？
数据目录专为技术用户和非技术用户而设计，包括：

* 数据开发人员、BI 和专业分析师（负责生成数据和分析内容，供他人使用）。
* 数据专员（了解数据的含义、用法和用途）。
* 数据使用者（需要能够使用所需工具轻松发现、理解和连接数据来完成相应的作业）。
* 中央 IT（需要让数以百计的数据源可被业务用户发现，需监督数据的使用方式和使用人员）。

## <a name="what-is-its-availability-by-region"></a>各个区域的可用性如何？
数据目录服务当前在以下数据中心可用：

* 美国西部
* 美国东部
* 欧洲西部
* 欧洲北部
* 澳大利亚东部
* 东南亚

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>数据资产的数量限制是多少？
数据目录的免费版仅限 5,000 个已注册数据资产。

数据目录的标准版最多支持 100,000 个已注册数据资产。

## <a name="what-are-its-supported-data-source-and-asset-types"></a>哪些是受其支持的数据源和资产类型？
有关当前支持的数据源列表，请参阅[数据目录 DSR](data-catalog-dsr.md)。

## <a name="how-do-i-request-support-for-another-data-source"></a>如何对另一数据源请求支持？
若要提交功能请求和其他反馈，转到 [Azure 数据目录论坛](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)。

## <a name="how-do-i-get-started-with-data-catalog"></a>如何开始使用数据目录？
访问[数据目录入门](data-catalog-get-started.md)是入门的最佳方式。 本文针对服务中的功能进行端到端概述。

## <a name="how-do-i-register-my-data"></a>如何注册我的数据？
在数据目录中注册数据：
1. 在 Azure 数据目录门户的“发布”区域中，启动 Azure 数据目录注册工具。 
2. 在数据目录发布应用程序中，使用用于访问数据目录门户的同一凭据登录。
3. 选择要注册的数据源和特定资产。

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>为已注册数据资产提取哪些属性？
具体属性因数据源而异，但一般而言，数据目录发布服务会提取以下信息：

* 资产名称
* 资产类型
* 资产说明
* 属性/列名称
* 属性/列数据类型
* 属性/列说明

> [!IMPORTANT]
> 使用数据目录注册数据资产不会将数据移动或复制到云。 从数据源注册资产会将资产的元数据复制到 Azure，但数据仍保留在现有的数据源位置。 如果选择在注册资产时上传预览记录或数据配置文件，则此规则不适用，这是此规则的例外情况。 上传预览时，将从每个资产复制多达 20 个记录并将其作为快照存储在数据目录中。 上传数据配置文件时，会计算聚合信息并将其包含到存储在目录中的元数据里。 聚合信息可能包括表的大小、每列空值的百分比或者列的最小值、最大列和平均值。 
>
>

> [!NOTE]
> 对于具有一流“说明”属性的数据源（例如 SQL Server Analysis Services），数据目录发布应用程序提取该属性值。 对于 SQL Server 关系数据库（缺少一流“说明”属性），数据目录发布应用程序提取对象和列的“ms_description”扩展属性中的值。 有关详细信息，请参阅[对数据库对象使用扩展属性](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx)。
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>新注册的资产需要多久才能在目录中显示？
在数据目录中注册资产后，可能需要 5至10 秒钟，才会在数据目录门户中显示。

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>如何批注和丰富已注册数据资产的元数据？
为已注册资产提供元数据最简单的方法是在数据目录门户中选择资产，然后在属性窗格或架构窗格中为选定对象输入值。

此外，还可在注册过程中提供某些元数据（例如专家和标记）。 在数据目录发布服务中提供的值适用于此时注册的所有资产。 若要查看门户中最近注册对象的更多批注，请选择数据目录发布应用程序的最终屏幕上的“查看门户”按钮。

## <a name="how-do-i-delete-my-registered-data-objects"></a>如何删除已注册的数据对象？
可通过在门户中选择对象并单击“删除”按钮，删除数据目录中的对象。 删除对象会从数据目录中删除其元数据，但不会影响基础数据源。

## <a name="what-is-an-expert"></a>什么是专家？
专家是对数据对象具有明智观点的人员。 一个对象可以具有多个专家。 专家不需要是对象的“所有者”，只需知道可以怎样和应该怎样使用数据。

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>遇到问题时如何与数据目录团队共享信息？
若要报告问题、共享信息和提出问题，请转到 [Azure 数据目录论坛](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)。

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>该目录是否与我感兴趣的另一个数据源配合使用？
我们当前正努力将更多数据源添加到数据目录中。 如果想查看支持的具体数据源，请通过转到 [Azure 数据目录论坛](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)提出建议（如果已有人建议则可表达你的支持）。

## <a name="how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>Azure 数据目录与 Power BI for Office 365 中的数据目录有什么关系？
可将 Azure 数据目录看做 Power BI 中的数据目录的演进。 自 2017 年春季开始，Azure 数据目录用于在 Excel 2016 和 Power Query for Excel 中启用查询的共享和发现功能。 Excel 中的数据目录功能适用于具有 Power BI Pro 许可证的用户。

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>在数据目录中注册资产时需要什么权限？
若要运行数据目录注册工具，需要具有对数据源的权限，允许从数据源读取元数据。 若要上传预览，必须具有允许从正在注册的对象读取数据的权限。

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>数据目录将可用于本地部署吗？
数据目录是可用于云和本地数据源的云服务，提供混合数据源发现解决方案。 目前没有计划提供在本地运行的数据目录服务版本。

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>我能从注册的数据源中提取更多或更丰富的元数据吗？
我们正在努力扩展数据目录的功能。 如果希望在注册期间从数据源中提取其他元数据，请在 [Azure 数据目录论坛](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)中提出建议（若已有建议则可为其投票）。 以后我们会允许第三方通过扩展性 API 添加新的数据源类型。

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>如何限制已注册数据资产的可见性，仅允许特定人员发现它们？
在数据目录中选择数据资产，然后单击“取得所有权”按钮。 数据目录中数据资产的所有者可更改可见性设置，允许所有用户发现拥有的资产或对特定用户限制可见性。

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>如何更新数据资产的注册，将数据源中的更改都反映到目录中？
若要更新已在目录中注册的数据资产的元数据，仅需重新注册包含该资产的数据源即可。 数据源中的任何更改（例如添加列、从表或视图中删除列）都会在目录中更新，但会保留用户提供的所有批注。

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>未在此处找到相关问题。 在哪里可以找到答案？
转到 [Azure 数据目录论坛](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)。 将在此处找到提出的问题。
