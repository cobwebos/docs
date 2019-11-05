---
title: 通过 Azure SQL 数据库边缘使用 SQL 数据库 DAC 包和流分析作业 |Microsoft Docs
description: 了解如何在 SQL 数据库边缘中使用流分析作业
keywords: sql 数据库边缘，流分析，sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 1b6b3f4e4be9d056bc53fac2eb2f1f3fcd768085
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514987"
---
# <a name="using-sql-database-dac-package-and-stream-analytics-job-with-sql-database-edge"></a>在 SQL 数据库边缘中使用 SQL 数据库 DAC 包和流分析作业

Azure SQL 数据库边缘预览版是一种优化的关系数据库引擎，适用于 IoT 和边缘部署。 它基于最新版本的 Microsoft SQL Server 数据库引擎提供，提供业界领先的性能、安全性和查询处理功能。 除了 SQL Server 的业界领先的关系数据库管理功能之外，Azure SQL 数据库边缘还提供内置的流式处理功能，可用于实时分析和复杂的事件处理。

Azure SQL 数据库边缘还提供了 SQLPackage 的本机实现，使用户能够在 SQL 数据库边缘部署过程中部署[Sql 数据库 DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications)包。

Azure SQL 数据库边缘通过 IoT Edge 模块的模块克隆*的所需属性*选项来公开两个可选参数。

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|字段 | 说明 |
|------|-------------|
| SQLPackage | 包含 SQL 数据库 DAC 包的 * .zip 文件的 Azure Blob 存储 URI。
| ASAJobInfo | ASA Edge 作业的 Azure Blob 存储 URI。 有关发布 ASA Edge 作业的详细信息，请参阅[为 SQL 数据库边缘发布 Asa edge 作业]()。

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>结合使用 SQL 数据库 DAC 包和 SQL 数据库边缘

若要将 SQL 数据库 DAC 包（* .dacpac）用于 SQL 数据库边缘，请按照下面所述的步骤进行操作。

1. 创建或提取 SQL 数据库 DAC 包。 您可以使用[从现有数据库提取 DAC](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/)中所述的概念为现有的 SQL 数据库生成 DacPac。

2. 压缩 * *.dacpac*并上传到 Azure Blob 存储帐户。 有关将文件上传到 Azure Blob 存储的详细信息，请参阅[上传、下载和列出 Azure 门户的 blob](../storage/blobs/storage-quickstart-blobs-portal.md)。

3. 使用 Azure 门户为 zip 文件生成 SAS 签名。 有关详细信息，请参阅[使用共享访问签名（SAS）委派访问权限](../storage/common/storage-sas-overview.md)。

4. 更新 SQL 数据库边缘模块配置，使其包含 DAC 包的 SAS URI。 更新 SQL 数据库边缘模块

    1. 在 Azure 门户上，浏览到 IoT 中心部署。

    2. 在左侧窗格中，单击 " **IoT Edge**"。

    3. 在 " **IoT Edge** " 页上，找到并单击部署 SQL 数据库边缘模块的 IoT Edge。

    4. 在 " *IoT Edge 设备*设备" 页上，单击 "**设置模块**"。 

    5. 在 "**设置模块**" 页上，单击 "*配置*" 以根据 SQL 数据库边缘模块。 

    6. 在 " **IoT Edge 自定义模块**" 窗格上，选择 "*设置模块克隆的所需属性*"，然后更新所需的属性以包含 SQLPackage 选项的 URI，如以下示例中所示。 

        > [!NOTE]
        > 下面的 SAS URI 仅用于说明。 请将 URI 替换为部署中的实际 URI。

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. 单击“保存”。

    8. 在 "**设置模块**" 页上，单击 "*下一步*"。

    9. 在 "**设置模块**" 页上单击 "*下一步*"，然后单击 "**提交**"。

5. 发布模块更新后，将下载、解压缩 dacpac 文件，并将其部署在 SQL 数据库边缘实例上。

## <a name="using-streaming-jobs-with-sql-database-edge"></a>在 SQL 数据库边缘中使用流式处理作业

Azure SQL 数据库边缘具有流分析运行时的本机实现。 这使用户可以创建 Azure 流分析 Edge 作业，并将该作业部署为 SQL 数据库边缘流式处理作业。 若要创建流分析边缘作业，请遵循以下步骤。

1. 使用预览[URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)浏览到 Azure 门户。 此预览 URL 允许用户为流分析边缘作业配置 SQL 数据库输出。

2. 创建新的**Azure IoT Edge 流分析**作业，并选择宿主环境目标**边缘**。

3. 定义 Azure 流分析作业的*输入*和*输出*。 每个 SQL 输出（如下所示）都绑定到数据库中的一个表。 如果需要将数据流式传输到多个表，则需要创建多个 SQL 数据库输出。 可以将 SQL 输出配置为指向不同的数据库。

    *输入-选择 EdgeHub 作为边缘作业的输入，并填写资源信息。*

    *输出-选择 SQL 数据库作为输出，"手动提供 SQL 数据库设置"，并提供数据库和表的配置详细信息。*

    |字段      | 说明 |
    |---------------|-------------|
    |输出别名 | 输出别名的名称。|
    |数据库 | SQL 数据库的名称。 这必须是 SQL 数据库边缘实例上存在的有效数据库名称。|
    |服务器名称 | SQL 实例的名称（或 IP 地址）和端口号详细信息。 对于 SQL 数据库边缘部署，可以使用**tcp：.、1433**作为服务器名称。|
    |用户名 | SQL 登录帐户，其中包含对上述数据库的数据读取器和数据写入器的访问权限。|
    |密码 | 上面提到的 SQL 登录帐户的密码。|
    |表 | 将为流式处理作业输出的表的名称。|
    |继承分区| 此 SQL 输出配置选项可继承上一个查询步骤或输入的分区方案。 启用此功能后，写入到基于磁盘的表，并为作业提供完全并行拓扑，就会看到更好的吞吐量。|
    |批大小| 批大小是每个大容量插入事务发送的最大记录数。|

    示例输入/输出配置如下：

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output :
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
    > 有关 Azure 流分析的 SQL 输出适配器的详细信息，请参阅 azure[流分析输出到 AZURE SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md)。

4. 定义 edge 作业的 ASA 作业查询。 此查询应使用定义的输入/输出别名作为查询中的输入和输出名称。 有关详细信息，请参阅[流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)。

5. 设置边缘作业的存储帐户设置。 存储帐户用作边缘作业的发布目标。

6. 在 "配置" 下，选择 "发布"，然后单击 "发布" 按钮。 保存 SAS URL 以用于 SQL 数据库边缘模块。

### <a name="deploy-the-stream-analytics-edge-job-to-the-sql-database-edge"></a>将流分析边缘作业部署到 SQL 数据库边缘

若要将流式处理作业部署到 SQL 数据库边缘模块，请更新 SQL 数据库边缘模块配置，使其包含上述步骤中的流式处理作业的 SAS URI。 更新 SQL 数据库边缘模块

1. 在 Azure 门户上，浏览到 IoT 中心部署。

2. 在左侧窗格中，单击 " **IoT Edge**"。

3. 在 " **IoT Edge** " 页上，找到并单击部署 SQL 数据库边缘模块的 IoT Edge。

4. 在 " *IoT Edge 设备*设备" 页上，单击 "**设置模块**"。 

5. 在 "**设置模块**" 页上，单击 "*配置*" 以根据 SQL 数据库边缘模块。 

6. 在 " **IoT Edge 自定义模块**" 窗格上，选择 "*设置模块克隆的所需属性*"，然后更新所需的属性以包含 ASAJobInfo 选项的 URI，如以下示例中所示。 

    > [!NOTE]
    > 下面的 SAS URI 仅用于说明。 请将 URI 替换为部署中的实际 URI。

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. 单击“保存”。

8. 在 "**设置模块**" 页上，单击 "*下一步*"。

9. 在 "**设置模块**" 页上单击 "*下一步*"，然后单击 "**提交**"。

10. 升级模块更新后，将下载、解压缩流分析作业文件，并将其部署在 SQL 数据库边缘实例上。

## <a name="next-steps"></a>后续步骤

- 有关定价和可用性相关的详细信息，请参阅[AZURE SQL 数据库边缘](https://azure.microsoft.com/services/sql-database-edge/)。
- 为订阅启用 Azure SQL 数据库边缘的请求。
- 若要开始操作，请参阅以下内容：
  - [通过 Azure 门户部署 SQL 数据库边缘](deploy-portal.md)
