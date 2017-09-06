---
title: "将数据从联机数据源导入机器学习工作室 | Microsoft 文档"
description: "如何将训练数据从各种联机源导入 Azure 机器学习工作室。"
keywords: "导入数据, 数据格式, 数据类型, 数据源, 训练数据"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 701b93fe-765b-4d15-a1cf-9b607f17add6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev;garye
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 16d4586d82ed256a90d8eb6be4aab927aed1200a
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---
# <a name="import-data-into-azure-machine-learning-studio-from-various-online-data-sources-with-the-import-data-module"></a>使用“导入数据”模块将数据从各种联机数据源导入 Azure 机器学习工作室
本文介绍针对从各种源导入联机数据的支持，以及将数据从这些源移入 Azure 机器学习试验所需的信息。

> [!NOTE]
> 本文提供有关[导入数据][import-data]模块的一般信息。 有关可访问的数据类型、格式、参数和常见问题解答的详细信息，请参阅[导入数据][import-data]模块的模块参考主题。
> 
> 

<!-- -->

[!INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

## <a name="introduction"></a>介绍
使用[导入数据][import-data]模块运行试验，可以在 [Azure 机器学习工作室](https://studio.azureml.net/Home)中访问来自多个联机数据源之一的数据：

* 使用 HTTP 的 Web URL
* 使用 HiveQL 的 Hadoop
* Azure Blob 存储
* Azure 表
* Azure SQL 数据库，或 Azure VM 上的 SQL Server
* 本地 SQL Server 数据库
* 数据馈送提供程序（目前为 OData）
* Azure CosmosDB（以前称为 DocumentDB）

要在工作室试验中访问联机数据源，请将[导入数据][import-data]模块添加到试验，选择“数据源”，然后提供访问数据时所需的参数。 下表列举了支持的联机数据源。 此表还汇总了支持的文件格式，以及用于访问数据的参数。

请注意，此训练数据是在运行试验时访问的，因此只能在该试验中使用。 相比之下，存储在数据集模块中的数据可供工作区中的任何试验使用。

> [!IMPORTANT]
> [导入数据][import-data]和[导出数据][export-data]模块目前只能在使用经典部署模型创建的 Azure 存储中读取和写入数据。 换言之，目前尚不支持可提供热存储访问层或冷存储访问层的新式 Azure Blob 存储帐户类型。 
> 
> 一般情况下，在此服务选项推出之前创建的任何 Azure 存储帐户应该不受影响。 
> 如果需要创建新帐户，请为“部署模型”选择“经典”或使用“资源管理器”；对于“帐户类型”，请选择“常规用途”而不是“Blob 存储”。 
> 
> 有关详细信息，请参阅 [Azure Blob 存储：热存储层和冷存储层](../storage/blobs/storage-blob-storage-tiers.md)。
> 
> 

## <a name="supported-online-data-sources"></a>支持的联机数据源
Azure 机器学习**导入数据**模块支持以下数据源：

| 数据源 | 说明 | parameters |
| --- | --- | --- |
| 通过 HTTP 的 Web URL |从使用 HTTP 的任何 Web URL 中读取逗号分隔值 (CSV)、制表符分隔值 (TSV)、属性关系文件格式 (ARFF) 和支持向量机 (SVM-light) 格式的数据 |<b>URL</b>：指定文件的完整名称，包括站点 URL 和文件名与任何扩展名。 <br/><br/><b>数据格式</b>：指定支持的数据格式之一：CSV、TSV、ARFF 或 SVM-light。 如果数据包含标头行，该数据用于分配列名。 |
| Hadoop/HDFS |从 Hadoop 中的分布式存储读取数据。 可以使用 HiveQL（类似于 SQL 的查询语言）指定所需的数据。 使用 HiveQL 还可以在将数据添加到机器学习工作室之前聚合数据和执行数据筛选。 |<b>Hive 数据库查询</b>：指定用于生成数据的 Hive 查询。<br/><br/><b>HCatalog 服务器 URI</b>：使用 *&lt;群集名称&gt;.azurehdinsight.net 格式指定群集的名称。*<br/><br/><b>Hadoop 用户帐户名</b>：指定用于预配群集的 Hadoop 用户帐户名。<br/><br/><b>Hadoop 用户帐户密码</b>：指定预配群集时使用的凭据。 有关详细信息，请参阅 [Create Hadoop clusters in HDInsight](../hdinsight/hdinsight-provision-clusters.md)（在 HDInsight 中创建 Hadoop 群集）。<br/><br/><b>输出数据的位置</b>：指定数据是要存储在 Hadoop 分布式文件系统 (HDFS) 还是 Azure 中。 <br/><ul>如果将输出数据存储在 HDFS 中，请指定 HDFS 服务器的 URI。 （请务必使用不带 HTTPS:// 前缀的 HDInsight 群集名称）。 <br/><br/>如果将输出数据存储在 Azure 中，则必须指定 Azure 存储帐户名、存储访问密钥和存储容器名称。</ul> |
| SQL 数据库 |读取存储在 Azure SQL 数据库中或 Azure 虚拟机上运行的 SQL Server 数据库中的数据。 |<b>数据库服务器名称</b>：指定运行数据库的服务器的名称。<br/><ul>对于 Azure SQL 数据库，请输入生成的服务器名称。 其格式通常为 *&lt;生成的标识符&gt;.database.windows.net。* <br/><br/>对于托管在 Azure 虚拟机上的 SQL Server，请输入 *tcp:&lt;虚拟机 DNS 名称&gt;, 1433*</ul><br/><b>数据库名称</b>：指定服务器上的数据库名称。 <br/><br/><b>服务器用户帐户名</b>：指定具有数据库访问权限的帐户的用户名。 <br/><br/><b>服务器用户帐户密码</b>：指定用户帐户的密码。<br/><br/><b>接受任何服务器证书</b>：如果要在读取数据之前跳过审查站点证书，请使用此选项（较不安全）。<br/><br/><b>数据库查询</b>：输入 SQL 语句用于说明要读取的数据。 |
| 本地 SQL 数据库 |读取本地 SQL 数据库中存储的数据。 |<b>数据网关</b>：指定可访问 SQL Server 数据库的计算机上安装的数据管理网关的名称。 有关设置网关的信息，请参阅 [Perform advanced analytics with Azure Machine Learning using data from an on-premises SQL server](machine-learning-use-data-from-an-on-premises-sql-server.md)（使用本地 SQL Server 中的数据通过 Azure 机器学习执行高级分析）。<br/><br/><b>数据库服务器名称</b>：指定运行数据库的服务器的名称。<br/><br/><b>数据库名称</b>：指定服务器上的数据库名称。 <br/><br/><b>服务器用户帐户名</b>：指定具有数据库访问权限的帐户的用户名。 <br/><br/><b>用户名和密码</b>：单击“输入值”输入数据库凭据。<b></b> 可以使用 Windows 集成身份验证或 SQL Server 身份验证，具体取决于配置本地 SQL Server 的方式。<br/><br/><b>数据库查询</b>：输入 SQL 语句用于说明要读取的数据。 |
| Azure 表 |从 Azure 存储中的表服务读取数据。<br/><br/>如果不常读取大量数据，请使用 Azure 表服务。 它提供了一个灵活、非关系 (NoSQL)、可大规模缩放、成本较低且高度可用的存储解决方案。 |**导入数据**中的选项根据访问的是公共信息还是需要登录凭据的专用存储帐户而变化。 这一点可以根据“身份验证类型”来确定，其值可能是“PublicOrSAS”或“Account”，两者都有自身的参数集。<b></b> <br/><br/><b>公共或共享访问签名 (SAS) URI</b>：参数为：<br/><br/><ul><b>表 URI</b>：指定表的公共 URL 或 SAS URL。<br/><br/><b>指定要扫描属性名称的行</b>：值为 <i>TopN</i>（扫描指定的行数）或 <i>ScanAll</i>（获取表中的所有行）。 <br/><br/>如果数据是同构的且可预测，我们建议选择“TopN”并为 N 输入一个数字。对于大型表，这样可以加快读取速度。<br/><br/>如果已使用根据表的深度和位置变化的属性集将数据结构化，请选择“ScanAll”选项来扫描所有行。 这可确保生成的属性和元数据转换的完整性。<br/><br/></ul><b>专用存储帐户</b>：参数为： <br/><br/><ul><b>帐户名</b>：指定要读取的表所在的帐户的名称。<br/><br/><b>帐户密钥</b>：指定与帐户关联的存储密钥。<br/><br/><b>表名称</b>：指定要读取的数据所在的表的名称。<br/><br/><b>要扫描属性名称的行</b>：值为 <i>TopN</i>（扫描指定的行数）或 <i>ScanAll</i>（获取表中的所有行）。<br/><br/>如果数据是同构的且可预测，我们建议选择“TopN”并为 N 输入一个数字。对于大型表，这样可以加快读取速度。<br/><br/>如果已使用根据表的深度和位置变化的属性集将数据结构化，请选择“ScanAll”选项来扫描所有行。 这可确保生成的属性和元数据转换的完整性。<br/><br/> |
| Azure Blob 存储 |读取存储在 Azure 存储的 Blob 服务中的数据，包括图像、非结构化文本或二元数据。<br/><br/>可以使用 Blob 服务公开数据，或者私下存储应用程序数据。 可以使用 HTTP 或 HTTPS 连接从任意位置访问数据。 |**导入数据**模块中的选项根据访问的是公共信息还是需要登录凭据的专用存储帐户而变化。 这一点可以根据“身份验证类型”来确定，其值可能是“PublicOrSAS”或“Account”。<b></b><br/><br/><b>公共或共享访问签名 (SAS) URI</b>：参数为：<br/><br/><ul><b>URI</b>：指定存储 Blob 的公共 URL 或 SAS URL。<br/><br/><b>文件格式</b>：指定 Blob 服务中数据的格式。 支持的格式包括 CSV、TSV 和 ARFF。<br/><br/></ul><b>专用存储帐户</b>：参数为： <br/><br/><ul><b>帐户名</b>：指定要读取的 Blob 所在的帐户的名称。<br/><br/><b>帐户密钥</b>：指定与帐户关联的存储密钥。<br/><br/><b>容器、目录或 Blob 的路径</b>：指定要读取的数据所在的 Blob 的名称。<br/><br/><b>Blob 文件格式</b>：指定 Blob 服务中数据的格式。 支持的数据格式包括 CSV、TSV、ARFF、CSV（使用指定的编码）和 Excel。 <br/><br/><ul>如果格式是 CSV 或 TSV，请务必指明文件是否包含标头行。<br/><br/>可以使用“Excel”选项从 Excel 工作簿中读取数据。 在“Excel 数据格式”选项中，指明数据是在 Excel 工作表范围内还是在 Excel 表中。<i></i> 在“Excel 工作表或嵌入表”选项中，指定要从中读取数据的工作表或表的名称。<i></i></ul><br/> |
| 数据馈送提供程序 |从支持的馈送提供程序读取数据。 目前仅支持开放数据协议 (OData) 格式。 |<b>数据内容类型</b>：指定 OData 格式。<br/><br/><b>源 URL</b>：指定数据馈送的完整 URL。 <br/>例如，以下 URL 从 Northwind 示例数据库读取数据：http://services.odata.org/northwind/northwind.svc/ |

## <a name="next-steps"></a>后续步骤

[部署使用数据导入和数据导出模块的 Azure 机器学习 Web 服务](machine-learning-web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/

