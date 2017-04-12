数据工厂中的复制活动可以将数据从源数据存储复制到接收器数据存储。 数据工厂支持以下数据存储。 来自任何源的数据都可以写入到任何接收器。 单击某个数据存储即可了解如何将数据复制到该存储，以及如何从该存储复制数据。

> [!NOTE] 
> 如需将数据移入/移出复制活动不支持的数据存储，可通过自己的逻辑使用数据工厂内的**自定义活动**来复制/移动数据。 有关创建和使用自定义活动的详细信息，请参阅[在 Azure数据工厂管道中使用自定义活动](../articles/data-factory/data-factory-use-custom-activities.md)。

| 类别 | 数据存储 | 支持用作源 | 支持用作接收器 |
|:--- |:--- |:--- |:--- |
| **Azure** |[Azure Blob 存储](../articles/data-factory/data-factory-azure-blob-connector.md) |✓ |✓ |
| &nbsp; |[Azure Data Lake Store](../articles/data-factory/data-factory-azure-datalake-connector.md) |✓ |✓ |
| &nbsp; |[Azure DocumentDB](../articles/data-factory/data-factory-azure-documentdb-connector.md) |✓ |✓ |
| &nbsp; |[Azure SQL 数据库](../articles/data-factory/data-factory-azure-sql-connector.md) |✓ |✓ |
| &nbsp; |[Azure SQL 数据仓库](../articles/data-factory/data-factory-azure-sql-data-warehouse-connector.md) |✓ |✓ |
| &nbsp; |[Azure 搜索索引](../articles/data-factory/data-factory-azure-search-connector.md) | |✓ |
| &nbsp; |[Azure 表存储](../articles/data-factory/data-factory-azure-table-connector.md) |✓ |✓ |
| **数据库** |[Amazon Redshift](../articles/data-factory/data-factory-amazon-redshift-connector.md) |✓ | |
| &nbsp; |[DB2](../articles/data-factory/data-factory-onprem-db2-connector.md)* |✓ | |
| &nbsp; |[MySQL](../articles/data-factory/data-factory-onprem-mysql-connector.md)* |✓ | |
| &nbsp; |[Oracle](../articles/data-factory/data-factory-onprem-oracle-connector.md)* |✓ |✓ |
| &nbsp; |[PostgreSQL](../articles/data-factory/data-factory-onprem-postgresql-connector.md)* |✓ | |
| &nbsp; |[SAP Business Warehouse](../articles/data-factory/data-factory-sap-business-warehouse-connector.md)* |✓ | |
| &nbsp; |[SAP HANA](../articles/data-factory/data-factory-sap-hana-connector.md)* |✓ | |
| &nbsp; |[SQL Server](../articles/data-factory/data-factory-sqlserver-connector.md)* |✓ |✓ |
| &nbsp; |[Sybase](../articles/data-factory/data-factory-onprem-sybase-connector.md)* |✓ | |
| &nbsp; |[Teradata](../articles/data-factory/data-factory-onprem-teradata-connector.md)* |✓ | |
| **NoSQL** |[Cassandra](../articles/data-factory/data-factory-onprem-cassandra-connector.md)* |✓ | |
| &nbsp; |[MongoDB](../articles/data-factory/data-factory-on-premises-mongodb-connector.md)* |✓ | |
| **文件** |[Amazon S3](../articles/data-factory/data-factory-amazon-simple-storage-service-connector.md) |✓ | |
| &nbsp; |[文件系统](../articles/data-factory/data-factory-onprem-file-system-connector.md)* |✓ |✓ |
| &nbsp; |[FTP](../articles/data-factory/data-factory-ftp-connector.md) |✓ | |
| &nbsp; |[HDFS](../articles/data-factory/data-factory-hdfs-connector.md)* |✓ | |
| &nbsp; |[SFTP](../articles/data-factory/data-factory-sftp-connector.md) |✓ | |
| **其他** |[泛型 HTTP](../articles/data-factory/data-factory-http-connector.md) |✓ | |
| &nbsp; |[泛型 OData](../articles/data-factory/data-factory-odata-connector.md) |✓ | |
| &nbsp; |[泛型 ODBC](../articles/data-factory/data-factory-odbc-connector.md)* |✓ | |
| &nbsp; |[Salesforce](../articles/data-factory/data-factory-salesforce-connector.md) |✓ | |
| &nbsp; |[Web 表（HTML 中的表）](../articles/data-factory/data-factory-web-table-connector.md) |✓ | |
| &nbsp; |[GE Historian](../articles/data-factory/data-factory-odbc-connector.md#ge-historian-store)* |✓ | | |

> [!NOTE]
> 带 * 的数据存储既可位于本地，也可位于 Azure IaaS 上，需要用户在本地/Azure IaaS 计算机上安装[数据管理网关](../articles/data-factory/data-factory-data-management-gateway.md)。
>
>
