---
title: Azure Analysis Services 中支持的数据源 | Microsoft Docs
description: 介绍 Azure Analysis Services 中的表格 1200 和更高版本数据模型所支持的数据源和连接器。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b99ac957c9c5030b484b244223847be4aa53a39d
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2020
ms.locfileid: "88749088"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Azure Analysis Services 中支持的数据源

对于 Azure Analysis Services 和 SQL Server Analysis Services，包含 Analysis Services 项目的 Visual Studio 中的“获取数据”或“表导入向导”中显示的数据源和连接器都会显示。 但是，并非所显示的所有数据源和连接器在 Azure Analysis Services 中都受支持。 你可以连接到的数据源的类型取决于许多因素，例如模型兼容性级别、可用的数据连接器、身份验证类型和本地数据网关支持。 下表介绍了 Azure Analysis Services 支持的数据源。

## <a name="azure-data-sources"></a>Azure 数据源

|数据源  |内存中  |直接连接  |注释 |
|---------|---------|---------|---------|
|Azure SQL 数据库      |   是      |    是      |<sup>[2](#azprovider)</sup>、<sup>[3](#azsqlmanaged)</sup>|
|Azure Synapse Analytics (SQL DW)      |   是      |   是       |<sup>[2](#azprovider)</sup>|
|Azure Blob 存储      |   是       |    否      | <sup>[1](#tab1400a)</sup> |
|Azure 表存储     |   是       |    否      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  是        |  否        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   是       |    否      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   是       |    否      |<sup>[1](#tab1400a)</sup>，<sup>[5](#gen2)</sup>|
|Azure HDInsight HDFS    |     是     |   否       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   是       |   否       |<sup>[1](#tab1400a)</sup><sup>[4](#databricks)</sup>|
||||

注意：

<a name="tab1400a">1</a> - 仅限表格 1400 和更高模型。  
<a name="azprovider">2</a> - 在表格 1200 和更高版本的模型中指定为“提供程序”数据源时，内存中模型和 DirectQuery 模型都需要 Microsoft OLE DB Driver for SQL Server MSOLEDBSQL（推荐）、SQL Server Native Client 11.0 或 .NET Framework Data Provider for SQL Server。  
<a name="azsqlmanaged">3</a> - 支持 Azure SQL 托管实例。 由于 SQL 托管实例在具有专用 IP 地址的 Azure VNet 中运行，因此必须在实例上启用公共终结点。 如果未启用，则[本地数据网关](analysis-services-gateway.md)是必需的。  
<a name="databricks">4</a> - 目前不支持使用 Spark 连接器的 Azure Databricks。  
<a name="gen2">5</a> - 目前不支持 ADLS Gen2 连接器，但是，Azure Blob 存储连接器可以与 ADLS Gen2 数据源一起使用。

## <a name="other-data-sources"></a>其他数据源

|数据源 | 内存中 | DirectQuery |注释   |
|  --- | --- | --- | --- |
|Access 数据库     |  是 | 否 |  |
|Active Directory     |  是 | 否 | <sup>[6](#tab1400b)</sup>  |
|Analysis Services     |  是 | 否 |  |
|分析平台系统     |  是 | 否 |  |
|CSV 文件  |是 | 否 |  |
|Dynamics 365     |  是 | 否 | <sup>[6](#tab1400b)</sup> |
|Excel 工作簿     |  是 | 否 |  |
|Exchange      |  是 | 否 | <sup>[6](#tab1400b)</sup> |
|文件夹      |是 | 否 | <sup>[6](#tab1400b)</sup> |
|IBM Informix  |是 | 否 |  |
|JSON 文档      |  是 | 否 | <sup>[6](#tab1400b)</sup> |
|二进制文件中的行      | 是 | 否 | <sup>[6](#tab1400b)</sup> |
|MySQL 数据库     | 是 | 否 |  |
|OData 源      |  是 | 否 | <sup>[6](#tab1400b)</sup> |
|ODBC 查询     | 是 | 否 |  |
|OLE DB     |   是 | 否 |  |
|Oracle  | 是  |是  | <sup>[9](#oracle)</sup> |
|PostgreSQL 数据库   | 是 | 否 | <sup>[6](#tab1400b)</sup> |
|Salesforce 对象|  是 | 否 | <sup>[6](#tab1400b)</sup> |
|Salesforce 报表 |是 | 否 | <sup>[6](#tab1400b)</sup> |
|SAP HANA     |  是 | 否 |  |
|SAP Business Warehouse    |  是 | 否 | <sup>[6](#tab1400b)</sup> |
|SharePoint 列表      |   是 | 否 | <sup>[6](#tab1400b)</sup>、<sup>[11](#filesSP)</sup> |
|SQL Server |是   | 是  | <sup>[7](#sqlim)</sup>、<sup>[8](#instgw)</sup> |
|SQL Server 数据仓库 |是   | 是  | <sup>[7](#sqlim)</sup>、<sup>[8](#instgw)</sup> |
|Sybase 数据库     |  是 | 否 |  |
|Teradata | 是  | 是  | <sup>[10](#teradata)</sup> |
|TXT 文件  |是 | 否 |  |
|XML 表    |  是 | 否 | <sup>[6](#tab1400b)</sup> |
| | | |

注意：  
<a name="tab1400b">6</a> - 仅限表格 1400 和更高版本的模型。  
<a name="sqlim">7</a> - 在表格 1200 和更高版本的模型中指定为“提供程序”数据源时，请指定 Microsoft OLE DB Driver for SQL Server MSOLEDBSQL（推荐）、SQL Server Native Client 11.0 或 .NET Framework Data Provider for SQL Server。  
<a name="instgw">8</a> - 如果指定 MSOLEDBSQL 作为数据提供程序，则可能需要在本地数据网关所在的同一计算机上下载并安装 [Microsoft OLE DB Driver for SQL Server](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server)。  
<a name="oracle">9</a> - 对于表格 1200 模型，或者作为表格 1400+ 模型中的“提供程序”数据源时，请指定用于 .NET 的 Oracle 数据提供程序。 如果指定为结构化数据源，请务必 [启用 Oracle 托管提供程序](#enable-oracle-managed-provider)。   
<a name="teradata">10</a> - 对于表格 1200 模型，或者作为表格 1400+ 模型中的“提供程序”数据源时，请指定用于 .NET 的 Teradata 数据提供程序。  
<a name="filesSP">11</a> - 不支持本地 SharePoint 中的文件。

从 Azure Analysis Services 服务器连接到本地数据源需要使用[本地网关](analysis-services-gateway.md)。 使用网关时，需要 64 位提供程序。

## <a name="understanding-providers"></a>了解提供程序

在 Visual Studio 中创建表格 1400 和更高版本的模型项目时，默认情况下，在使用“获取数据”连接到数据源时，你不指定数据提供程序。 表格 1400 和更高版本的模型使用 [Power Query](/power-query/power-query-what-is-power-query) 连接器来管理数据源与 Analysis Services 之间的连接、数据查询和混搭。 这些连接有时称为结构化数据源连接，因为连接属性设置是为你设置的。 但是，你可以在 Visual Studio 中为模型项目启用旧数据源。 启用后，可以使用表导入向导连接到表格 1200 和更低版本模型中传统上受支持的某些数据源，如旧版数据源或提供程序数据源 。 当指定为提供程序数据源时，可以指定特定的数据提供程序和其他高级连接属性。 例如，可以连接到 SQL Server 数据仓库实例，甚至是连接到作为旧数据源的 Azure SQL 数据库。 然后，你可以选择 OLE DB Driver for SQL Server MSOLEDBSQL 数据提供程序。 在这种情况下，选择 OLE DB 数据提供程序可以通过 Power Query 连接器提供更好的性能。 

当使用 Visual Studio 中的“表导入向导”时，连接到任何数据源都需要数据提供程序。 会为你选择一个默认的数据提供程序。 如果需要，你可以更改数据提供程序。 你选择的提供程序的类型可能取决于性能，无论该模型使用内存中存储还是 DirectQuery，也不管你将模型部署到哪个 Analysis Services 平台。

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>在表格 1400 和更高版本的模型项目中指定提供程序数据源

若要启用提供程序数据源，请在 Visual Studio 中单击“工具” > “选项” > “Analysis Services 表格” > “数据导入”，然后选择“启用旧数据源”。    

![启用旧数据源](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

启用旧数据源后，在表格模型资源管理器中，右键单击“数据源”，并单击“从数据源(旧的)导入”。  > 

![“表格模型资源管理器”中的旧数据源](media/analysis-services-datasource/aas-import-legacy-datasources.png)

与使用表格 1200 模型项目一样，请使用**表导入向导**连接到数据源。 在连接页上，单击“高级”。 在“设置高级属性”中，指定数据提供程序和其他连接设置。

![旧数据源高级属性](media/analysis-services-datasource/aas-import-legacy-advanced.png)

## <a name="impersonation"></a>模拟
某些情况下可能需要指定其他模拟帐户。 可以在 Visual Studio 或 SQL Server Management Studio (SSMS) 中指定模拟帐户。

对于本地数据源：

* 如果使用 SQL 身份验证，则模拟应为服务帐户。
* 如果使用 Windows 身份验证，请设置 Windows 用户/密码。 对于 SQL Server，只有内存中数据模型才支持带有特定模拟帐户的 Windows 身份验证。

对于云数据源：

* 如果使用 SQL 身份验证，则模拟应为服务帐户。

## <a name="oauth-credentials"></a>OAuth 凭据

对于在 1400 和更高兼容性级别下使用内存模式的表格模型，Azure SQL 数据库、Azure Synapse（以前称为 SQL 数据仓库）、Dynamics 365 和 SharePoint 列表支持 OAuth 凭据。 Azure Analysis Services 管理 OAuth 数据源的令牌刷新，以避免长时间运行的刷新操作超时。 若要生成有效的令牌，请使用 Power Query 设置凭据。

OAuth 凭据不支持直接查询模式。

## <a name="enable-oracle-managed-provider"></a>启用 Oracle 托管提供程序

在某些情况下，对 Oracle 数据源的 DAX 查询可能会返回意外的结果。 这可能是因为用于数据源连接的访问接口。

如 " [了解提供程序](#understanding-providers) " 部分中所述，表格模型作为 *结构化* 数据源或 *提供程序* 数据源连接到数据源。 对于指定为提供程序数据源的 Oracle 数据源的模型，请确保指定的提供程序是 .NET 的 Oracle 数据提供程序 (DataAccess) 。 

如果将 Oracle 数据源指定为结构化数据源，请启用 " **MDataEngine\UseManagedOracleProvider** 服务器" 属性。 设置此属性可确保模型使用适用于 .NET 托管提供程序的建议 Oracle 数据提供程序连接到 Oracle 数据源。
 
若要启用 Oracle 托管提供程序：

1. 在 SQL Server Management Studio 中，连接到服务器。
2. 使用以下脚本创建 XMLA 查询。 将 **ServerName** 替换为完整的服务器名称，然后执行查询。

    ```xml
    <Alter AllowCreate="true" ObjectExpansion="ObjectProperties" xmlns="http://schemas.microsoft.com/analysisservices/2003/engine">
        <Object />
        <ObjectDefinition>
            <Server xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ddl2="http://schemas.microsoft.com/analysisservices/2003/engine/2" xmlns:ddl2_2="http://schemas.microsoft.com/analysisservices/2003/engine/2/2" 
    xmlns:ddl100_100="http://schemas.microsoft.com/analysisservices/2008/engine/100/100" xmlns:ddl200="http://schemas.microsoft.com/analysisservices/2010/engine/200" xmlns:ddl200_200="http://schemas.microsoft.com/analysisservices/2010/engine/200/200" 
    xmlns:ddl300="http://schemas.microsoft.com/analysisservices/2011/engine/300" xmlns:ddl300_300="http://schemas.microsoft.com/analysisservices/2011/engine/300/300" xmlns:ddl400="http://schemas.microsoft.com/analysisservices/2012/engine/400" 
    xmlns:ddl400_400="http://schemas.microsoft.com/analysisservices/2012/engine/400/400" xmlns:ddl500="http://schemas.microsoft.com/analysisservices/2013/engine/500" xmlns:ddl500_500="http://schemas.microsoft.com/analysisservices/2013/engine/500/500">
                <ID>ServerName</ID>
                <Name>ServerName</Name>
                <ServerProperties>
                    <ServerProperty>
                        <Name>MDataEngine\UseManagedOracleProvider</Name>
                        <Value>1</Value>
                    </ServerProperty>
                </ServerProperties>
            </Server>
        </ObjectDefinition>
    </Alter>
    ```

3. 重新启动服务器。


## <a name="next-steps"></a>后续步骤

* [本地网关](analysis-services-gateway.md)
* [管理服务器](analysis-services-manage.md)
