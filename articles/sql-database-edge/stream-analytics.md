---
title: 使用 SQL 数据库 DAC 包和流分析作业与 Azure SQL 数据库边缘 |微软文档
description: 了解如何在 SQL 数据库边缘中使用流分析作业
keywords: sql 数据库边缘、流分析、sql 包
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74384156"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>使用 SQL 数据库 DAC 包和具有 SQL 数据库边缘的流分析作业

Azure SQL 数据库边缘预览是一个优化的关系数据库引擎，适用于 IoT 和边缘部署。 它基于最新版本的 Microsoft SQL Server 数据库引擎，它提供了业界领先的性能、安全性和查询处理功能。 除了 SQL Server 业界领先的关系数据库管理功能外，Azure SQL 数据库边缘还提供内置流式处理功能，用于实时分析和复杂的事件处理。

Azure SQL 数据库边缘还提供 SqlPackage.exe 的本机实现，使您能够在 SQL 数据库边缘部署期间部署[SQL 数据库 DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications)包。

Azure SQL 数据库边缘通过 IoT 边缘`module twin's desired properties`模块的选项公开两个可选参数：

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|字段 | 描述 |
|------|-------------|
| SqlPackage | 包含 SQL 数据库 DAC 包的 @.zip 文件的 Azure Blob 存储 URI。
| ASAJobInfo | 用于 ASA 边缘作业的 Azure Blob 存储 URI。 有关详细信息，请参阅为[SQL 数据库边缘发布 ASA 边缘作业](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge)。

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>将 SQL 数据库 DAC 包与 SQL 数据库边缘一起使用

要将 SQL 数据库 DAC 包 （*.dacpac） 与 SQL 数据库边缘一起使用，请执行以下步骤：

1. 创建或提取 SQL 数据库 DAC 包。 有关如何为现有 SQL Server 数据库生成 DAC 包的信息，请参阅[从数据库中提取 DAC。](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/)

2. 压缩 @.dacpac 并将其上载到 Azure Blob 存储帐户。 有关将文件上载到 Azure Blob 存储的详细信息，请参阅[使用 Azure 门户上载、下载和列出 Blob。](../storage/blobs/storage-quickstart-blobs-portal.md)

3. 使用 Azure 门户为 zip 文件生成共享访问签名。 有关详细信息，请参阅[使用共享访问签名 （SAS） 委派访问权限](../storage/common/storage-sas-overview.md)。

4. 更新 SQL 数据库边缘模块配置以包括 DAC 包的共享访问 URI。 要更新 SQL 数据库边缘模块，请执行以下步骤：

    1. 在 Azure 门户中，转到 IoT 中心部署。

    2. 在左窗格中，选择“IoT Edge”****。

    3. 在**IoT 边缘**页上，查找并选择部署 SQL 数据库边缘模块的 IoT 边缘。

    4. 在**IoT 边缘设备**设备页面上，选择 **"设置模块**"。

    5. 在 **"设置模块"** 页上，选择针对 SQL 数据库边缘模块**进行配置**。

    6. 在**IoT 边缘自定义模块**窗格中，选择 **"设置模块孪生"所需的属性**。 更新所需属性以包括选项的`SQLPackage`URI，如以下示例所示。

        > [!NOTE]
        > 以下 JSON 中的 SAS URI 只是一个示例。 将 URI 替换为部署中的实际 URI。

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. 选择“保存”。****

    8. 在 **"设置模块"** 页上，选择 **"下一步**"。

    9. 在 **"设置模块"** 页上，选择 **"下一步**"，然后**提交**。

5. 模块更新后，将下载、解压缩 DAC 包文件，并针对 SQL 数据库边缘实例部署该文件。

## <a name="using-streaming-jobs-with-sql-database-edge"></a>使用具有 SQL 数据库边缘的流式处理作业

Azure SQL 数据库边缘具有流分析运行时的本机实现。 此实现使您能够创建 Azure 流分析边缘作业，并将该作业部署为 SQL 数据库边缘流作业。 要创建流分析边缘作业，请完成以下步骤：

1. 使用预览[URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)转到 Azure 门户。 此预览 URL 使您能够为流分析边缘作业配置 SQL 数据库输出。

2. 在**IoT 边缘作业上创建新的 Azure 流分析**。 选择面向**边缘的**托管环境。

3. 为 Azure 流分析作业定义输入和输出。 您将在此处设置的每个 SQL 输出都绑定到数据库中的单个表。 如果需要将数据流式传输到多个表，则需要创建多个 SQL 数据库输出。 您可以将 SQL 输出配置为指向不同的数据库。

    **输入**。 选择 EdgeHub 作为边缘作业的输入，并提供资源信息。

    **输出**。 选择 SQL 数据库作为输出。 选择**手动提供 SQL 数据库设置**。 提供数据库和表的配置详细信息。

    |字段      | 描述 |
    |---------------|-------------|
    |输出别名 | 输出别名的名称。|
    |数据库 | SQL 数据库的名称。 它需要是 SQL 数据库边缘实例上存在的数据库的有效名称。|
    |服务器名称 | SQL 实例的名称（或 IP 地址）和端口号详细信息。 对于 SQL 数据库边缘部署，可以使用**tcp：.，1433**作为服务器名称。|
    |用户名 | SQL 登录帐户，该帐户具有数据读取器和数据编写器对前面指定的数据库的访问权限。|
    |密码 | 您之前指定的 SQL 登录帐户的密码。|
    |表 | 将输出的流作业的表的名称。|
    |继承分区| 启用继承上一个查询步骤或输入的分区方案。 启用此选项后，在写入基于磁盘的表并为作业提供完全并行拓扑时，可以预期会看到更好的吞吐量。|
    |批大小| 随每个批量插入事务一起发送的最大记录数。|

    下面是一个示例输入/输出配置：

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output:
            Output alias: output
            Database:  MeasurementsDB
            Server name: tcp:.,1433
            Username: sa
            Password: <Password>
            Table: TemperatureMeasurements
            Inherit Partitioning: Merge all input partitions into a single writer
            Max batch count: 10000
    ```

    > [!NOTE]
    > 有关 Azure 流分析的 SQL 输出适配器的详细信息，请参阅[Azure 流分析输出到 Azure SQL 数据库](../stream-analytics/stream-analytics-sql-output-perf.md)。

4. 为边缘作业定义 ASA 作业查询。 此查询应使用定义的输入/输出别名作为查询中的输入和输出名称。 有关详细信息，请参阅[流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)。

5. 设置边缘作业的存储帐户设置。 存储帐户用作边缘作业的发布目标。

6. 在 **"配置**"**下，选择"发布**"，然后选择 **"发布**"按钮。 保存 SAS URI 以与 SQL 数据库边缘模块一起使用。

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>将流分析边缘作业部署到 SQL 数据库边缘

要将流式处理作业部署到 SQL 数据库边缘模块，请更新 SQL 数据库边缘模块配置，以包括前面步骤中流作业的 SAS URI。 要更新 SQL 数据库边缘模块：

1. 在 Azure 门户中，转到 IoT 中心部署。

2. 在左窗格中，选择“IoT Edge”****。

3. 在**IoT 边缘**页上，查找并选择部署 SQL 数据库边缘模块的 IoT 边缘。

4. 在**IoT 边缘设备**设备页面上，选择 **"设置模块**"。

5. 在 **"设置模块"** 页上，选择针对 SQL 数据库边缘模块**进行配置**。

6. 在**IoT 边缘自定义模块**窗格中，选择 **"设置模块孪生"所需的属性**。 更新所需属性以包括选项的`ASAJobInfo`URI，如以下示例所示。

    > [!NOTE]
    > 以下 JSON 中的 SAS URI 只是一个示例。 将 URI 替换为部署中的实际 URI。

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. 选择“保存”。****

8. 在 **"设置模块"** 页上，选择 **"下一步**"。

9. 在 **"设置模块"** 页上，选择 **"下一步**"，然后**提交**。

10. 模块更新后，将下载、解压缩和部署针对 SQL 数据库边缘实例的流分析作业文件。

## <a name="next-steps"></a>后续步骤

- 有关定价和可用性详细信息，请参阅[Azure SQL 数据库边缘](https://azure.microsoft.com/services/sql-database-edge/)。
- 请求为订阅启用 Azure SQL 数据库边缘。
- 要开始，请参阅[通过 Azure 门户部署 SQL 数据库边缘](deploy-portal.md)。
