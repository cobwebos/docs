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
ms.date: 10/04/2016
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 76cc9c79851cc040e6a36c5499a77c0d95948154


---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Azure 数据目录常见问题
本文将解答有关 Microsoft **Azure 数据目录**服务的常见问题。

## <a name="q-what-is-azure-data-catalog"></a>问：什么是 Azure 数据目录？
答：Microsoft Azure 数据目录由 Microsoft Azure 云完全托管的云服务，具有企业数据源的注册系统和发现系统的功能。 任何用户（从分析师到数据专家再到开发人员）都可以使用 Azure 数据目录提供的功能进行注册、发现、理解和使用数据源。

## <a name="q-what-customer-challenges-does-azure-data-catalog-solve"></a>问：Azure 数据目录可解决客户的哪些难题？
Azure 数据目录通过帮助用户发现和理解企业数据源来解决数据源发现和“黑暗数据”的难题。

## <a name="q-who-are-the-target-audiences-for-azure-data-catalog"></a>问：哪些人是 Azure 数据目录的目标群体？
Azure 数据目录为技术性用户和非技术性用户提供功能，包括：

* 数据开发人员、BI 和专业分析师（负责生成数据和分析内容，供他人使用）
* 数据专员（了解数据的含义、用法和用途）
* 数据使用者（使用所需工具能够轻松发现、理解和连接数据来完成相应的作业）
* 中央 IT（需要让数以百计的数据源可被业务用户发现、监督数据的使用方式和使用人员）

## <a name="q-what-is-the-azure-data-catalog-region-availability"></a>问：什么是 Azure 数据目录区域可用性？
Azure 数据目录当前在以下数据中心可用：

* 美国西部
* 美国东部
* 欧洲西部
* 欧洲北部
* 澳大利亚东部
* 亚洲东南部

## <a name="q-what-are-the-limits-on-the-number-of-data-assets-in-azure-data-catalog"></a>问：Azure 数据目录中数据资产的数量限制是多少？
Azure 数据目录的免费版仅限 5,000 个已注册数据资产。

Azure 数据目录的标准版最多支持 100,000 个已注册数据资产。

## <a name="q-what-are-the-supported-data-source-and-asset-types"></a>问：哪些是受支持的数据源和资产类型？
请参阅[数据目录 DSR](data-catalog-dsr.md)，获取当前支持的数据源列表。

## <a name="q-how-do-i-request-support-for-another-data-source"></a>问：如何对另一数据源请求支持？
可在 [Azure 数据目录论坛](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)中提交功能请求和其他反馈。

## <a name="q-how-do-i-get-started-with-azure-data-catalog"></a>问：如何开始使用 Azure 数据目录？
最佳做法为按照 [数据目录入门](data-catalog-get-started.md) 中的说明开始操作。 本文针对服务中的功能进行端到端概述。

## <a name="q-how-do-i-register-my-data"></a>问：如何注册我的数据？
若要在 Azure 数据目录中注册数据，请从 Azure 数据目录门户的“发布”区域启动 Azure 数据目录注册工具。 在 Azure 数据目录发布应用程序中，使用用于访问 Azure 数据目录门户的同一凭据登录，然后选择要注册的数据源和特定资产。

## <a name="q-what-properties-are-extracted-for-data-assets-that-are-registered"></a>问：为已注册数据资产提取了哪些属性？
特定属性会让各个数据源之间产生差异，但一般而言，Azure 数据目录发布服务会提取以下信息：

* 资产名称
* 资产类型
* 资产说明
* 属性/列名称
* 属性/列数据类型
* 属性/列说明

> [!IMPORTANT]
> 使用 Azure 数据目录注册数据资产不会将你的数据移动或复制到云。 从数据源注册资产会将资产的元数据复制到 Azure，但数据仍会存在于现有数据源的位置。 如果用户选择在注册资产时上传预览记录或数据配置文件，此规则不适用，这是此规则的唯一例外情况。 上传预览时，将从每个资产复制多达 20 个记录并将其作为快照存储在 Azure 数据目录中。 上传数据配置文件时，将计算聚合信息（如表的大小、每个列的百分比 null 值和列的最小、最大和平均值）并将其包括在存储于目录的元数据中。
> 
> 

<br/>

> [!NOTE]
> 对于数据源（例如具有一流**说明**属性的 SQL Server Analysis Services），Azure 数据目录发布应用程序将提取该属性值。 对于 SQL Server 关系数据库（缺少一流**说明**属性），Azure 数据目录发布应用程序将从对象和列的 ms_description 扩展属性中提取值。 有关详细信息，请参阅 TechNet [对数据库对象使用扩展属性](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx)。
> 
> 

## <a name="q-how-long-should-it-take-for-newly-registered-assets-to-appear-in-azure-data-catalog"></a>问：新注册的资产需要多久才能在 Azure 数据目录中显示？
在 Azure 数据目录中注册资产后，可能需要 5-10 秒钟，才会在 Azure 数据目录门户中显示。

## <a name="q-how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>问：如何对已注册数据资产的元数据进行批注和丰富？
为已注册资产提供元数据最简单的方法是在 Azure 数据目录门户中选择资产，然后在属性窗格或架构窗格中为所选对象输入元数据值。

此外，还可在注册过程中提供某些元数据（例如专家和标记）。 Azure 数据目录发布服务中提供的值将应用到所有在此时注册的资产中。 若要查看门户中最近注册对象的更多批注，请选择 Azure 数据目录发布应用程序的最终屏幕上的“查看门户”按钮。

## <a name="q-how-do-i-delete-my-registered-data-objects"></a>问：如何删除已注册的数据对象？
可通过在门户中选择对象并单击“删除”按钮，删除 Azure 数据目录中的对象。 这将从 Azure 数据目录删除对象的元数据，但不会影响实际的基础数据源。

## <a name="q-what-is-an-expert"></a>问：什么是专家？
专家是对数据对象具有明智观点的人员。 一个对象可以具有多个专家。 专家不需要是对象的“所有者”；专家仅需知道可以怎样和应该怎样使用数据。

## <a name="q-how-do-i-share-information-with-the-azure-data-catalog-team-if-i-encounter-problems"></a>问：遇到问题时如何与 Azure 数据目录团队共享信息？
请使用 Azure 数据目录论坛报告问题、分享信息和提出问题。 可在此处找到论坛：http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409

## <a name="q-does-azure-data-catalog-work-with-this-other-data-source-im-interested-in"></a>问：Azure 数据目录支持我感兴趣的其他数据源吗？
我们当前正努力将更多数据源添加到 Azure 数据目录中。 如果你希望某个数据源收到支持，请在[Azure 数据目录论坛](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)中提出建议（如果已有人建议则可表达你的支持）。

## <a name="q-how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>问：Azure 数据目录与 Office 365 的 Power BI 中的数据目录有什么关系？
你可将 Azure 数据目录看做数据目录的演进。 Azure 数据目录提供数据源发布和发现的类似功能，但其注重于更广泛的方案且不依赖于 Office 365。 Azure 数据目录发布正式版后，很快就会将这两个目录合并为单个服务。

## <a name="q-what-permissions-does-a-user-need-to-register-assets-with-azure-data-catalog"></a>问：用户在 Azure 数据目录中注册资产时需要什么权限？
运行 Azure 数据目录注册工具的用户需要数据源上的权限，允许其从数据源读取元数据。 如果用户还选择包括预览，则用户还必须具有允许其从正在注册的对象读取数据的权限。

## <a name="q-will-azure-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>问：Azure 数据目录将可用于本地部署吗？
Azure 数据目录是可用于云和本地数据源的云服务，提供混合数据源发现解决方案。 当前没有计划本地运行的 Azure 数据目录服务版本。

## <a name="q-can-we-extract-more-richer-metadata-from-the-data-sources-we-register"></a>问：我们能从注册的数据源中提取更多/更丰富的元数据吗？
我们正在努力扩展 Azure 数据目录的功能。 如果你希望在注册期间从数据源中提取其他元数据，请在 [Azure 数据目录论坛](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)中提出建议（若已建议则可为其投票）。 以后我们会允许第三方通过可扩展性 API 添加新的数据源类型。

## <a name="q-how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>问：如何限制已注册数据资产的可见性，仅允许特定人员发现它们？
答：在 Azure 数据目录中选择数据资产，然后单击“取得所有权”按钮。 Azure 数据目录中数据资产的所有者可更改可见性设置，允许所有目录用户发现其拥有的资产或对特定用户限制可见性。

## <a name="q-how-do-i-update-the-registration-for-a-data-asset-to-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>问：如何更新数据资产的注册，将数据源中的更改都反映到目录中？
答：若要更新已在目录中注册的数据资产的元数据，仅需重新注册包含该资产的数据源即可。 数据源中的任何更改（添加列或从表或视图中删除列）将在目录中更新，但用户提供的所有批注都会保留。

## <a name="q-my-question-isnt-answered-here-what-should-i-do"></a>问：如果未在此处找到相关问题怎么办？
转到 [Azure 数据目录论坛](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)。 将在此处找到提出的问题。




<!--HONumber=Nov16_HO3-->


