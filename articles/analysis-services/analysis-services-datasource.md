---
title: Azure Analysis Services 中支持的数据源 | Microsoft Docs
description: 介绍 Azure Analysis Services 中表格1200和更高版本数据模型支持的数据源和连接器。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7616bcdaf2a2ae6f80b0c1e964f311ef5409a64f
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707136"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Azure Analysis Services 中支持的数据源

在 Visual Studio 中，在 "获取数据" 或 "表导入向导" 中显示的数据源和连接器，同时为 Azure Analysis Services 和 SQL Server Analysis Services 显示 Analysis Services 项目。 但是，并不是所有显示的数据源和连接器在 Azure Analysis Services 中都受支持。 您可以连接到的数据源的类型取决于多种因素，例如模型兼容级别、可用的数据连接器、身份验证类型和本地数据网关支持。 下表描述了 Azure Analysis Services 支持的数据源。

## <a name="azure-data-sources"></a>Azure 数据源

|数据源  |内存中  |DirectQuery  |说明 |
|---------|---------|---------|---------|
|Azure SQL Database      |   是      |    是      |<sup>[2](#azprovider)</sup>， <sup> [3](#azsqlmanaged)</sup>|
|Azure SQL 数据仓库      |   是      |   是       |<sup>[2](#azprovider)</sup>|
|Azure Blob 存储      |   是       |    No      | <sup>[1](#tab1400a)</sup> |
|Azure 表存储     |   是       |    No      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  是        |  No        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   是       |    No      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   是       |    No      |<sup>[1](#tab1400a)</sup>、 <sup> [5](#gen2)</sup>|
|Azure HDInsight HDFS    |     是     |   No       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   是       |   No       |<sup>[1](#tab1400a)</sup>， <sup> [4](#databricks)</sup>|
||||

**说明：**    
<a name="tab1400a">1</a> - 仅限表格 1400 和更高模型。  
<a name="azprovider">2</a> -在表格1200和更高版本模型中指定为*提供程序*数据源时，内存中和 DirectQuery 模型都需要 Microsoft OLE DB 驱动程序用于 SQL Server MSOLEDBSQL （推荐）、SQL Server Native Client 11.0 或 .NET Framework 数据提供程序以进行 SQL Server。    
<a name="azsqlmanaged"></a>支持 Azure SQL 数据库托管实例。 由于托管实例在使用专用 IP 地址的 Azure VNet 中运行，因此必须在实例上启用公共终结点。 如果未启用，则需要[本地数据网关](analysis-services-gateway.md)。    
<a name="databricks">4</a> -当前不支持使用 Spark 连接器 Azure Databricks。   
<a name="gen2">5</a> ADLS Gen2 连接器目前不受支持，但是，可以将 Azure Blob 存储连接器与 ADLS Gen2 数据源一起使用。   

## <a name="other-data-sources"></a>其他数据源

|数据源 | 内存中 | DirectQuery |说明   |
|  --- | --- | --- | --- |
|Access 数据库     |  是 | No |  |
|Active Directory     |  是 | No | <sup>[共](#tab1400b)</sup>  |
|Analysis Services     |  是 | No |  |
|分析平台系统     |  是 | No |  |
|CSV 文件  |是 | No |  |
|PowerApps     |  是 | No | <sup>[共](#tab1400b)</sup> |
|Excel 工作簿     |  是 | No |  |
|交换      |  是 | No | <sup>[共](#tab1400b)</sup> |
|Folder      |是 | No | <sup>[共](#tab1400b)</sup> |
|IBM Informix  |是 | No |  |
|JSON 文档      |  是 | No | <sup>[共](#tab1400b)</sup> |
|二进制文件中的行      | 是 | No | <sup>[共](#tab1400b)</sup> |
|MySQL 数据库     | 是 | No |  |
|OData 数据源      |  是 | No | <sup>[共](#tab1400b)</sup> |
|ODBC 查询     | 是 | No |  |
|OLE DB     |   是 | No |  |
|Oracle  | 是  |是  | <sup>[900](#oracle)</sup> |
|PostgreSQL 数据库   | 是 | No | <sup>[共](#tab1400b)</sup> |
|Salesforce 对象|  是 | No | <sup>[共](#tab1400b)</sup> |
|Salesforce 报表 |是 | No | <sup>[共](#tab1400b)</sup> |
|SAP HANA     |  是 | No |  |
|SAP Business Warehouse    |  是 | No | <sup>[共](#tab1400b)</sup> |
|SharePoint 列表      |   是 | No | <sup>[6](#tab1400b)</sup>， <sup> [11](#filesSP)</sup> |
|SQL Server |是   | 是  | <sup>[7](#sqlim)</sup>、 <sup> [8](#instgw)</sup> | 
|SQL Server 数据仓库 |是   | 是  | <sup>[7](#sqlim)</sup>、 <sup> [8](#instgw)</sup> |
|Sybase 数据库     |  是 | No |  |
|Teradata | 是  | 是  | <sup>[万](#teradata)</sup> |
|TXT 文件  |是 | No |  |
|XML 表    |  是 | No | <sup>[共](#tab1400b)</sup> |
| | | |

**说明：**    
<a name="tab1400b">6</a> -仅表格式1400和更高型号的模型。  
<a name="sqlim">7</a> -在表格1200和更高型号中指定为*提供程序*数据源时，为用于 SQL Server 的 SQL Server MSOLEDBSQL （推荐）、SQL Server Native Client 11.0 或 .NET Framework 数据提供程序指定 Microsoft OLE DB 驱动程序。  
<a name="instgw">8</a> -如果指定 MSOLEDBSQL 作为数据访问接口，则可能需要下载并安装与本地数据网关所在计算机上的[SQL Server 的 Microsoft OLE DB 驱动程序](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server)。  
<a name="oracle">9</a> -对于表格1200模型，或在表格 1400 + 模型中作为*提供程序*数据源，请指定用于 .Net 的 Oracle 数据提供程序。  
<a name="teradata">10</a> -对于表格1200模型，或在表格 1400 + 模型中作为*提供程序*数据源，请指定用于 .Net 的 Teradata 数据提供程序。   
<a name="filesSP">11</a> -不支持本地 SharePoint 中的文件。

从 Azure Analysis Services 服务器连接到本地数据源需要使用[本地网关](analysis-services-gateway.md)。 使用网关时，需要 64 位提供程序。 

## <a name="understanding-providers"></a>了解提供程序

在 Visual Studio 中创建表格1400和更高版本的模型项目时，默认情况下，在使用 "**获取数据**" 连接到数据源时不指定数据访问接口。 表格1400和更高型号使用[Power Query](/power-query/power-query-what-is-power-query.md)连接器来管理数据源和 Analysis Services 之间的连接、数据查询和混合。 这有时称为*结构化*数据源连接，这些连接属性设置已设置。 不过，您可以启用旧数据源。 启用后，可以使用 "**表导入向导**" 连接到某些数据源，这些数据源通常在表格1200中受支持，并可用作*旧*模型或*提供程序*数据源。 指定为提供程序数据源时，可以指定特定的数据提供程序和其他高级连接属性。 例如，可以连接到本地 SQL Server 数据仓库，甚至作为旧数据源连接到 Azure SQL 数据库。 然后，你可以为 SQL Server MSOLEDBSQL 数据提供程序选择 OLE DB 驱动程序。 在这种情况下，选择 OLE DB 的数据访问接口可能会提高 Power Query 连接器的性能。 

在 Visual Studio 中使用 "表导入向导" 时，与任何数据源的连接都需要一个数据访问接口。 将为您选择一个默认的数据提供程序。 如果需要，可以更改数据访问接口。 选择的提供程序类型可以依赖于性能，无论该模型使用内存中存储还是 DirectQuery，以及将模型部署到哪个 Analysis Services 平台。

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>在表格1400和更高版本的模型项目中指定提供程序数据源

若要启用提供程序数据源，请在 Visual Studio 中，单击 "**工具**" > **选项**" > **Analysis Services 表格** > **数据导入**"，选择 "**启用旧数据源**"。

![启用旧数据源](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

启用旧数据源后，在 "**表格模型资源管理器**" 中，右键单击 "**数据**源" > **从数据源导入（旧版）** 。

![表格模型资源管理器中的旧数据源](media/analysis-services-datasource/aas-import-legacy-datasources.png)

与使用表格1200模型项目一样，使用 "**表导入向导**" 连接到数据源。 在 "连接" 页上，单击 "**高级**"。 在 "**设置高级属性**" 中指定数据提供程序和其他连接设置。

![旧数据源高级属性](media/analysis-services-datasource/aas-import-legacy-advanced.png)


## <a name="impersonation"></a>模拟
某些情况下可能需要指定其他模拟帐户。 可在 Visual Studio 或 SSMS 中指定模拟帐户。

对于本地数据源：

* 如果使用 SQL 身份验证，则模拟应为服务帐户。
* 如果使用 Windows 身份验证，请设置 Windows 用户/密码。 对于 SQL Server，只有内存中数据模型才支持带有特定模拟帐户的 Windows 身份验证。

对于云数据源：

* 如果使用 SQL 身份验证，则模拟应为服务帐户。

## <a name="oauth-credentials"></a>OAuth 凭据

对于1400和更高兼容级别的表格模型，Azure SQL 数据库、Azure SQL 数据仓库、Dynamics 365 和 SharePoint 列表支持 OAuth 凭据。 Azure Analysis Services 管理 OAuth 数据源的令牌刷新，以避免长时间运行的刷新操作超时。 若要生成有效的令牌，请使用 SSMS 设置凭据。

## <a name="next-steps"></a>后续步骤
[本地网关](analysis-services-gateway.md)   
[管理服务器](analysis-services-manage.md)   

