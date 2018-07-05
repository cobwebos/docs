---
title: Azure 数据目录中的新增功能 | Microsoft Docs
description: 本文概述了 Azure 数据目录中的新增功能。
services: data-catalog
documentationcenter: ''
author: steelanddata
manager: NA
editor: ''
tags: ''
ms.assetid: 1201f8d4-6f26-4182-af3f-91e758a12303
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: 780ab56df19e8d6761fcf88fa7fbed3e8002fbe8
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025115"
---
# <a name="whats-new-in-azure-data-catalog"></a>Azure 数据目录中的新增功能
我们会定期发布“Azure 数据目录”的更新。 但是，并非每个版本都包括面向用户的新功能，因为某些版本聚焦于后端的服务功能。 本页重点介绍 Azure 数据目录服务新增的面向用户的功能。

## <a name="whats-new-for-november-2017"></a>2017 年 11 月新增功能 
截至 2017 年 11 月，已添加到 Azure 数据目录的功能如下：

* 支持直接链接到数据目录门户中的特定业务术语表术语。 用户可以从业务术语表复制链接，然后将其嵌入到文档、电子邮件、报表或其他位置，以便直接链接到术语表术语定义。
* 支持 Azure Active Directory 服务主体。 数据目录管理员可以使用服务主体授权客户端应用程序访问目录，并可以授予这些应用程序特定权限就像他们可以向用户和安全组授予权限一样。 有关其他信息，请参阅 [Azure Active Directory 中的应用程序对象和服务主体对象](../active-directory/develop/active-directory-application-objects.md)。
* 支持在使用数据目录数据源注册工具连接到 Azure SQL 数据库和 Azure SQL 数据仓库数据源时进行 Azure Active Directory 身份验证。 有关其他信息，请参阅[将 Azure Active Directory 身份验证用于 SQL 数据库或 SQL 数据仓库身份验证](../sql-database/sql-database-aad-authentication.md)。


## <a name="whats-new-for-september-2017"></a>2017 年 9 月新增功能 
截至 2017 年 9 月，已添加到 Azure 数据目录的功能如下：

* 使用数据源注册工具注册相关表时，支持从 DB2 数据源提取联接关系元数据。
* 支持使用数据源注册工具注册 MongoDB 3.4 版数据源。
* 从数据目录中删除数据库或其他容器时，支持在单个操作中删除包含对象的所有元数据。
* 在数据目录门户中优化搜索时，支持查看最多 1,000 个标记、业务术语表术语或其他搜索 Facet。


## <a name="whats-new-for-august-2017"></a>2017 年 8 月新增功能 
截至 2017 年 8 月，已添加到 Azure 数据目录的功能如下：

*   新开发者示例可用于使用数据目录 REST API 创建和管理关系元数据。 “导入关系信息到数据目录”示例位于[数据目录代码示例页面](https://azure.microsoft.com/resources/samples/?service=data-catalog&sort=0)。 
* 在使用数据源注册工具注册相关表时，支持从 Teradata 数据源提取联接关系元数据。
* 在使用数据源注册工具注册 SQL Server 数据源时，支持 SQL Server 表值函数 (TVF) 对象。
* 多项更新和优化，可改善数据目录门户的性能和可用性。

## <a name="whats-new-for-july-2017"></a>2017 年 7 月新增功能 
截至 2017 年 7 月，已添加到 Azure 数据目录的功能如下：
*   支持更精细地控制允许元数据操作，包括：
    - 目录管理员可以限制用户向目录提供标签和相关元数据的能力，从而实现对目录的只读访问。
    - 目录管理员可以限制用户在目录中注册新数据源的能力。
    - 目录管理员可以限制用户在目录中获取数据资产元数据的所有权。
    - 可以向 Azure Active Directory 安全组和用户授予权限，以方便管理权限。
* 支持注册数据资产之间的关系，并在数据目录门户中发现相关数据资产，包括：
    - 在使用数据目录数据源注册工具时，从 SQL Server（包括 Azure SQL 数据库）、Oracle 和 MySQL 数据源中提取关系元数据。
    - 在数据目录门户中查看资产元数据时发现相关数据资产。
    - 通过使用数据目录 REST API 来定义、发现和管理数据资产之间关系的操作。

有关管理数据目录中权限的更多详细信息，请参阅[如何保护对数据目录和数据资产的访问](data-catalog-how-to-secure-catalog.md)。
有关数据目录中关系的更多详细信息，请参阅[如何在 Azure 数据目录中查看相关数据资产](data-catalog-how-to-view-related-data-assets.md)。

## <a name="whats-new-for-june-2017"></a>2017 年 6 月新增功能 
截至 2017 年 6 月，已添加到 Azure 数据目录的功能如下：
*   支持 Sybase、Apache Cassandra 和 MongoDB 数据源。 用户现在可注册和发现 Cassandra 及 MongoDB 数据库和表，以及 Sybase 数据库、表和视图。

> [!NOTE]
> 注册包含具有复杂数据类型（例如记录和数组）的列的 MongoDB 和 Cassandra 表时，不会在添加到数据目录的元数据中包含这些列。

## <a name="whats-new-for-may-2017"></a>2017 年 5 月新增功能 
截至 2017 年 5 月，已添加到 Azure 数据目录的功能如下：
*   用于批量导入业务术语表术语的新开发者示例可用。 术语表批量导入示例可在[数据目录开发者示例页](https://docs.microsoft.com/azure/data-catalog/data-catalog-samples)获取。 
*   支持在 Azure 数据目录门户中编辑 ODBC 连接信息。 数据资产所有者和数据目录管理员现在可编辑已注册的 ODBC 数据源的连接信息，而无需重新注册数据源。
*   在业务术语表定义和说明中支持可单击的 URL。 业务术语表术语的说明和定义属性中包含 URL 时，URL 会在数据目录门户中显示为超链接。
*   除专家电子邮件地址外，还支持显示专家姓名。 在数据目录门户中查看数据资产属性中的专家时，Azure Active Directory 中专家的全名会在工具提示中显示。

## <a name="whats-new-for-april-2017"></a>2017 年 4 月新增功能 
截至 2017 年 4 月，已添加到 Azure 数据目录的功能如下：
*   支持 ODBC 数据源。 用户现在可使用数据目录数据源注册工具注册和发现 ODBC 数据库、表和视图。

## <a name="whats-new-for-march-2017"></a>2017 年 3 月新增功能 
截至 2017 年 3 月，已添加到 Azure 数据目录的功能如下：
*   支持为数据目录管理员使用 AAD 安全组。
*   Azure 数据目录现在新 Azure 区域中可用。 除美国东部、美国西部、西欧、澳大利亚东部、北欧和东南亚外，客户还可在美国中西部区域预配 Azure 数据目录。 有关详细信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。

## <a name="whats-new-for-february-2017"></a>2017 年 2 月新增功能 
截至 2017 年 2 月，已添加到 Azure 数据目录的功能如下：
*   支持数据目录数据源注册工具中的高级设置。 注册大型数据源时，用户可指定命令超时值。
*   支持 FTP 和 PostgreSQL 数据源。 用户现在可注册和发现 FTP 文件及目录，以及 PostgreSQL 数据库、表和视图。

## <a name="whats-new-for-january-2017"></a>2017 年 1 月新增功能 
截至 2017 年 1 月，已添加到 Azure 数据目录的功能如下：
*   Azure 数据目录现在符合 [CSA STAR](https://www.microsoft.com/trustcenter/compliance/csa-star-certification) 要求。
*   与[在 Excel 2016 和 Power Query for Excel 中获取和转换功能](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605)集成。 Excel 用户可在 Excel 内使用 Azure 数据目录共享查询和发现查询。 此功能面向具有 Power BI Pro 许可证的用户提供。

## <a name="whats-new-for-december-2016"></a>2016 年 12 月新增功能
截至 2016 年 12 月，已添加到 Azure 数据目录的功能如下：
*   Azure 数据目录现在符合 [HIPAA](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa)和[欧盟标准条款](https://www.microsoft.com/en-us/TrustCenter/Compliance/EU-Model-Clauses)要求。
*   支持编辑数据源连接信息。 数据资产所有者和数据目录管理员现在可编辑已注册的数据源的连接信息，而无需重新注册数据源。
*   支持 Salesforce.com 数据源。 用户现在可注册和发现 Salesforce 对象。


## <a name="whats-new-for-november-2016"></a>2016 年 11 月新增功能
截至 2016 年 11 月，已添加到 Azure 数据目录的功能如下：
*   Azure 数据目录现在符合 [ISO/IEC 27001](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001) 和 [ISO/IEC 27018](https://www.microsoft.com/TrustCenter/Compliance/iso-iec-27018) 要求。
*   支持使用数据目录门户和 REST API 手动注册 ODBC 数据源。

## <a name="whats-new-for-september-2016"></a>2016 年 9 月新增功能
截至 2016 年 9 月，已添加到 Azure 数据目录的功能如下：

* 支持 IBM DB2 数据源。 现在，用户可以注册和发现 DB2 数据库、表和视图。
* 支持 Azure Cosmos DB 数据源。 现在，用户可以注册和发现 Cosmos DB 数据库和集合。
* 支持数据目录门户中的自定义目录名称。 目录管理员现在可以提供显示在门户标题中的文本，如组织名称。

## <a name="whats-new-for-august-2016"></a>2016 年 8 月新增功能
截至 2016 年 8 月，已添加到 Azure 数据目录的功能如下：

* SQL Server Master Data Services (MDS) 数据源注册的增强功能。 在使用数据目录数据源注册工具注册 MDS 实体时，用户现在可以包括预览和数据配置文件。
* 支持管理员定义的组织中保存的搜索。 在数据目录门户中保存搜索时，数据目录管理员现在可以选择保存搜索以供个人还是供所有目录用户使用。 组织中保存的搜索共享给所有目录用户，并且可以为数据源发现提供标准化切入点。
* 数据目录门户中的属性视图更新。 现在可在单个、可调整大小的窗格中显示和管理所有数据资产属性，以提供更加一致、更易于发现的体验。

## <a name="whats-new-for-july-2016"></a>2016 年 7 月新增功能
截至 2016 年 7 月，已添加到 Azure 数据目录的功能如下：

* 支持 SQL Server Master Data Services (MDS) 数据源。 现在，用户可以注册和发现 MDS 模型和实体。
* 支持 SQL Server 存储过程。 现在，用户可以在 SQL Server 数据源中注册和发现存储的过程对象。
* 在 Azure 数据目录门户和数据源注册工具中支持其他语言，共支持 18 种语言。 Azure 数据目录的用户体验根据在 Windows 中或在 Web 浏览器中设置的语言首选项进行本地化。
* 更新并简化数据目录门户主页，包括性能改进和用户体验简化。

## <a name="whats-new-for-june-2016"></a>2016 年 6 月新增功能
截至 2016 年 6 月，已添加到 Azure 数据目录的功能如下：

* 在目录门户中发现数据资产时，支持在列表视图中调整列的大小。 用户现在可以重新调整单个列大小，更轻松地读取较长资产的元数据，如标记和说明。
* Power Query for Excel 已添加到数据目录门户中的“打开方式”菜单中。 通过安装的 [Power Query for Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605) 外接程序，用户现在可以在 Excel 2016 或 Excel 2010 和 Excel 2013 中打开受支持的数据源。
* 支持 Azure 表存储数据源。 现在，用户可以在 Azure 存储数据源中注册和发现表对象。

## <a name="whats-new-for-may-2016"></a>2016 年 5 月新增功能
截至 2016 年 5 月，已添加到 Azure 数据目录的功能如下：

* 业务术语表允许目录管理员定义业务术语和层次结构，以创建常见的业务词汇。 用户可以使用术语表术语标记已注册的数据资产，从而更容易发现和了解目录内容。 有关详细信息，请参阅 [How to set up the Business Glossary for Governed Tagging](data-catalog-how-to-business-glossary.md)  
* 数据目录业务术语表的增强功能，让用户可以通过单个操作更新多个术语表。 用户可以选择多个术语来编辑以下字段：
  * 父术语：用户可以选择新的父术语，所有选定的术语将更新为所选父术语的子级。 如果所有选定术语具有相同的父术语，则该父术语会显示在文本框中，否则父术语字段设置为空。   
  * 标记和利益干系人：用户可以添加和删除多个术语表术语的标记和利益干系人，这与标记多个数据资产一样。

> [!NOTE]
> 业务术语表仅在 Azure 数据目录标准版中可用。 免费版不提供受管标记或业务术语表功能。

## <a name="whats-new-for-march-2016"></a>2016 年 3 月新增功能
截至 2016 年 3 月，已添加到 Azure 数据目录的功能如下：

* 统一的 REST API 终结点，用于以编程方式访问 Azure 数据目录服务搜索功能和目录资产管理功能。 已弃用此搜索 API 终结点和目录 API 终结点，并已在 2016 年 3 月 21 日停用。 API 的语义没有更改。 仅更改了终结点 URI。 有关详细信息，请参阅 [Azure Data Catalog REST API Reference](https://msdn.microsoft.com/library/azure/mt267595.aspx)（Azure 数据目录 REST API 参考）。 对于 API 示例，请参阅 [Azure Data Catalog developer samples](data-catalog-samples.md)（Azure 数据目录开发者示例）。

## <a name="whats-new-for-february-2016"></a>2016 年 2 月新增功能
截至 2016 年 2 月，已添加到 Azure 数据目录的功能如下：

* 在 Azure 数据目录数据源注册工具中新增重新设计的数据源选择体验。 数据源注册工具已更新，方便找到 Azure 数据目录所支持的数据源，并从中进行选择。
* 在 Azure 数据目录门户和数据源注册工具中支持其他 10 种语言。 除英语外，Azure 数据目录体验现已支持德语、西班牙语、法语、意大利语、日语、韩语、巴西葡萄牙语、俄语、简体中文和繁体中文。 Azure 数据目录的用户体验根据在 Windows 中或在用户的 Web 浏览器中设置的语言首选项进行本地化。
* 支持 Azure 数据目录数据的异地复制，带来业务连续性，提供灾难恢复。 所有 Azure 数据目录内容，包括数据源元数据和众包批注，现在在两个 Azure 区域之间已复制，客户无需额外付费。 预先对至少相隔 500 英里的 Azure 区域进行配对，并遵循 [业务连续性和灾难恢复 (BCDR)：Azure 配对区域](../best-practices-availability-paired-regions.md)中所述的映射。
* 支持更改 Azure 数据目录使用的 Azure 订阅。 出于计费目的，Azure 数据目录管理员可以使用 Azure 数据目录门户中的设置页来选择不同的 Azure 订阅。

## <a name="whats-new-for-january-2016"></a>2016 年 1 月新增功能
截至 2016 年 1 月，已添加到 Azure 数据目录的功能如下：

* 支持手动注册更多数据源。 现在，可以在 Azure 数据目录门户中使用“手动创建条目”，或使用 Azure 数据目录 REST API 来注册以下数据源：
  * OData - 函数、实体集和实体容器
  * HTTP - 文件、终结点、报告和站点
  * 文件系统 - 文件
  * SharePoint - 列表
  * FTP - 文件和目录
  * Salesforce.com - 对象
  * DB2 - 表、视图和数据库
  * PostgreSQL - 表、视图和数据库
* 支持 SQL Server（包括 Azure SQL DB 和 Azure SQL 数据仓库）数据源的“在 SQL Server Data Tools 中打开”。  
* 支持注册和发现 SAP HANA 视图和包。 可以使用 Azure 数据目录数据源注册工具注册 SAP HANA 数据源，并可以使用 Azure 数据目录门户批注和发现已注册的 SAP HANA 数据源。
* 能够在 Azure 数据目录门户中固定和取消固定数据资产。 可以选择固定数据资产，以便更轻松地重新发现和重复使用这些数据资产。
* 重新设计了 Azure 数据目录门户中的主页。 新主页包括当前用户活动（包括最近发布的资产、固定的资产和保存的搜索）见解，以及整体目录中活动的见解。
* 支持 Azure 数据目录门户中永久性用户设置。 在用户会话之间保留用户体验设置，包括网格或磁贴视图、每页显示的结果数以及打开或关闭突出显示。
* Azure 数据目录现已在两个新的 Azure 区域中可用。 除美国东部、美国西部、西欧和澳大利亚东部外，客户还可以在北欧和东南亚区域中设置 Azure 数据目录。 有关详细信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。

> [!NOTE]
> “在 SQL Server Data Tools 中打开”要求安装了 Update 4 和 SQL Server Tooling 的 Visual Studio 2013。 若要安装最新版的 SQL Server Data Tools，请访问[下载 SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)。


## <a name="whats-new-for-december-2015"></a>2015 年 12 月新增功能
截至 2015 年 12 月，已添加到 Azure 数据目录的功能如下：

* 支持 Azure SQL 数据仓库数据源的数据配置文件。 注册 Azure SQL 数据仓库表和视图时，用户可以选择包括数据配置文件指标以及从数据源中提取的元数据。
* 支持注册和发现 MySQL 对象和数据库。 用户可以使用 Azure 数据目录数据源注册工具注册 MySQL 数据源，并可以使用 Azure 数据目录门户批注和发现已注册的 MySQL 数据源。
* 支持 Teradata 数据源的 SPNEGO 和 Windows 身份验证。 注册 Teradata 表和视图时，用户可以选择使用 SPNEGO 和 Windows 以及 LDAP 和 TD2 身份验证连接到 Teradata。
* 支持 Azure Data Lake Store 数据源。 现在，用户可以使用 Azure 数据目录注册和发现 Azure Data Lake Store 数据源。
* 支持手动在 Azure 数据目录数据源注册工具中指定网络代理设置。 用户可以从工具的欢迎页上选择“修改代理设置”，并可以指定工具使用的代理地址和端口。

## <a name="whats-new-for-november-2015"></a>2015 年 11 月新增功能
截至 2015 年 11 月，已添加到 Azure 数据目录的功能如下：

* 能够从 Azure 数据目录门户查看和复制 SQL Server（包括 Azure SQL 数据库）和 Oracle 数据源的连接字符串。 用户可以单击 SQL Server 或 Oracle 表、视图或数据库的连接信息中的“查看连接字符串”链接，查看用于连接数据源的连接字符串。 提供 SQL Server 数据源的 ADO.NET、 ODBC、 OLEDB 和 JDBC 连接字符串。 提供 Oracle 数据源的 ODBC 和 OLEDB 连接字符串。
* 支持在注册 Teradata 表和视图时包括数据配置文件。
* 对于 SQL Server（包括 Azure SQL DB 和 Azure SQL 数据仓库）、SQL Server Analysis Services、 Azure 存储和 HDFS 源，支持“在 Power BI Desktop 中打开”。  
* 支持 Teradata 数据源的 LDAP 身份验证。 注册 Teradata 表和视图时，用户可以选择使用 LDAP 和 TD2 身份验证连接到 Teradata。
* 支持 Teradata 数据源“在 Excel 中打开”。
* 在 Azure 数据目录门户中支持最近搜索词。 在在门户中搜索时，用户可以选择最近使用过的搜索词以加快发现体验。
* 支持 Teradata 数据源预览。 注册 Teradata 表和视图时，用户可以选择包括快照记录以及从数据源中提取的元数据。
* 对 Azure SQL 数据仓库数据源支持“在 Excel 中打开”。
* 对于手动注册的数据资产，支持定义和编辑列级架构。 使用 Azure 数据目录门户手动创建数据资产后，用户可以在数据资产属性中添加列定义。
* 搜索 Azure 数据目录时支持“有”查询，以发现已注册的具有特定元数据的数据资产。 Azure 数据目录查询语法现在包括：

| 查询语法 | 目的 |
| --- | --- |
| `has:previews` |查找包括预览的数据资产 |
| `has:documentation` |查找提供了文档的数据资产 |
| `has:tableDataProfiles` |查找具有表级数据配置文件信息的数据资产 |
| `has:columnsDataProfiles` |查找具有列级数据配置文件信息的数据资产 |


> [!NOTE]
> “在 Power BI Desktop 中打开”要求安装当前版本的 Power BI Desktop 应用程序。 如果使用此功能时遇到问题或错误，请确保安装了 [PowerBI.com](https://powerbi.com) 中的 Power BI Desktop 最新版本。


## <a name="whats-new-for-october-2015"></a>2015 年 10 月新增功能
截至 2015 年 10 月，已添加到 Azure 数据目录的功能如下：

* 支持静态数据预览加密以及已注册的数据源的数据配置文件。 Azure 数据目录以透明方式加密数据源注册到该服务的所有预览记录和数据配置文件，无需目录管理员进行密钥管理。
* 支持 Teradata 数据源。 现在，用户可以注册和发现 Teradata 表和视图。
* 支持本地 Hive 数据源。 现在，用户可以在 Hadoop 本地数据源中注册和发现 Apache Hive 的 Hive 表。
* 在 Azure 数据目录门户中支持最近保存的搜索。 用户可以保存搜索词并筛选选择，以便更轻松地重复之前的搜索和定义目录内容的有用视图。 用户还可以将保存的搜索标记为默认搜索。 当用户从 Azure 数据目录门户主页上或从“入门”页上单击“放大镜”搜索图标时，会直接将用户带到标记为默认的保存的搜索中。
* 在 Azure 数据目录门户中已注册的数据资产和容器，支持富文本文档。 用户现在可以提供数据资产的文档，如表、视图和报表，以及容器的文档，如数据库和模型，这适用于没有足够标记和描述的情况。
* 支持手动注册已知的数据源类型。 对于 Azure 数据目录支持的所有数据源类型，用户可以使用 Azure 数据目录门手动输入数据源信息。
* 支持授权 Azure Active Directory 安全组。 目录管理员可以启用对安全组和用户帐户的目录访问，便于管理对 Azure 数据目录的访问权限。
* 支持从 Azure 数据目录门户在 Excel 中打开 Hive 数据源。

> [!NOTE]
> 对于当前版本中，仅支持 Teradata TD2 身份验证。 以后的版本将支持其他身份验证机制。

> [!NOTE]
> 若要使用 Hive 数据源的“在 Excel 中打开”功能，用户必须已安装 Hive 的 ODBC 驱动程序。

## <a name="whats-new-for-september-2015"></a>2015 年 9 月新增功能
截至 2015 年 9 月，已添加到 Azure 数据目录的功能如下：

* 支持在注册 Hive 数据源时包括数据配置文件。
* 支持以编程方式发现目录 API，便于应用程序与 Azure 数据目录集成。
* 在 Azure 数据目录门户中增加了新的“入门”数据源发现体验。 当用户进入 Azure 数据目录门户的“发现”页却没有输入搜索词时，将为用户展现目录内容概述，包括最常用的标记、专家、数据源类型和对象类型。
* 支持注册和发现 Azure SQL 数据仓库对象和数据库。 有关 Azure SQL 数据仓库的详细信息，请参阅 [SQL 数据仓库](https://azure.microsoft.com/services/sql-data-warehouse/)。
* 支持注册和发现作为容器的 SQL Server Analysis Services 模型和 SQL Server Reporting Services 服务器。 注册 SSAS 和 SSRS 对象时，Azure 数据目录会为 SSAS 模型和 SSRS 服务器以及报表和其他对象创建条目。 可以使用 Azure 数据目录门户发现并且批注容器。 除搜索和筛选目录内容外，用户还可以搜索和筛选模型或服务器的内容。
* 支持通过 HTTP/HTTPS 注册和发现 SQL Server Analysis Services 对象。 用户现在可以替代服务器名称使用 URL（如 https://servername/olap/msmdpump.dll)）连接到 SSAS 服务器，并且除 Windows 身份验证外，还能使用基本身份验证和匿名连接。 有关通过 HTTP/HTTPS 连接到 SSAS 的详细信息，请参阅 [配置对 Analysis Services 的 HTTP 访问](https://msdn.microsoft.com/library/gg492140.aspx)。
* 支持 HDInsight 上的 Hive 数据源。 现在，用户可以在 HDInsight 本地数据源中注册和发现 Apache Hive 的 Hive 表。 有关 HDInsight 上的 Hive 的详细信息，请参阅 [HDInsight documentation center](../hdinsight/hadoop/hdinsight-use-hive.md)（HDInsight 文档中心）。
* 支持注册和发现作为容器的 Oracle 数据库和 HDFS 群集。 注册 Oracle 表和视图或 HDFS 时，Azure 数据目录会为数据库、表和视图创建条目。 可以使用 Azure 数据目录门户发现并且批注数据库。 除搜索和筛选目录内容外，用户还可以搜索和筛选数据库或群集的内容。
* 支持手动注册未知的数据源类型。 用户可以使用 Azure 数据目录门户手动输入数据源信息，以便批注和发现数据源注册工具未显式支持的数据源。
* 支持注册和发现作为容器的 SQL Server 数据库。 注册 SQL Server 表和视图时，Azure 数据目录会为数据库、表和视图创建条目。 可以使用 Azure 数据目录门户发现并且批注数据库。 除搜索和筛选目录内容外，用户还可以搜索和筛选数据库的内容。

> [!NOTE]
> 将模型或服务器条目添加到目录之前，必须使用数据源注册工具对 9 月 18 日版本之前注册的 SSAS 和 SSRS 对象重新进行注册。 重新注册的数据源不会对用户在 Azure 数据目录门户中添加的任何批注产生影响。

> [!NOTE]
> 将数据库或群集条目添加到目录之前，必须使用数据源注册工具对 9 月 11 日版本之前注册的 Oracle 表、视图、HDFS 文件和目录重新进行注册。 重新注册的数据源不会对用户在 Azure 数据目录门户中添加的任何批注产生影响。

> [!NOTE]
> 将数据库条目添加到目录之前，必须使用数据源注册工具对 9 月 4 日版本之前注册的 SQL Server 表和视图重新进行注册。 重新注册的数据源不会对用户在 Azure 数据目录门户中添加的任何批注产生影响。

## <a name="whats-new-for-august-2015"></a>2015 年 8 月新增功能
截至 2015 年 8 月，已添加到 Azure 数据目录的功能如下：

* 支持 SQL Server 和 Oracle 数据源的数据事件探查。 注册 SQL Server 以及 Oracle 表和视图时，用户可以选择包含要注册的对象的数据配置文件信息。 数据配置文件包含对象级和列级统计信息。
* 支持 Hadoop HDFS 数据源。 现在，用户可以注册和发现 HDFS 文件和目录。
* 对已注册的数据源提供访问请求信息。 对于任何已注册的数据资产，用户现在可以提供用于发出访问请求（包括电子邮件链接或 URL）的说明，以轻松与现有工具和过程集成。
* 支持标记和专家工具提示，便于更容易发现哪些用户为已注册的数据资产提供了何种元数据。
* 我们已在顶部导航栏中添加了新的“用户”按钮和菜单。 通过此菜单，用户可以查看用于登录 Azure 数据目录的帐户，并在需要时对其进行注销。 此菜单还显示目录名称，这对使用 Azure 数据目录 REST API 的开发人员很有用。
* 仅适用于标准版：将所有者添加到数据资产时，Azure 数据目录现在支持用户帐户和安全组作为所有者。 要将安全组添加为所选数据资产的所有者，可以输入组的显示名称或组的 UPN 电子邮件地址（如果有的话）。
* 支持 Azure Blob 存储数据源。 现在，用户可以注册和发现 Azure 存储 Blob 和目录。

