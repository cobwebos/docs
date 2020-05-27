---
title: 在 Azure SQL Edge（预览版）中使用 Azure 流分析 Edge 作业
description: 了解如何在 Azure SQL Edge（预览版）中使用流分析作业
keywords: SQL Edge, 流分析,
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 3b57cb8cae80381a6c2cd88358dd9284ba56c919
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594516"
---
# <a name="using-azure-stream-analytics-jobs-with-sql-edge"></a>在 SQL Edge 中使用 Azure 流分析作业

Azure SQL Edge（预览版）是已优化的关系数据库引擎，适用于 IoT 和边缘部署。 它是在最新版 Microsoft SQL Server 数据库引擎的基础之上构建而成，此引擎提供了业界领先的性能、安全性和查询处理功能。 除了具有 SQL Server 的业界领先的关系数据库管理功能外，Azure SQL Edge 还提供了内置的流式处理功能，可用于实时分析和复杂事件处理。

Azure SQL Edge 具有流分析运行时的本机实现。 此实现允许你创建 Azure 流分析 Edge 作业，并将该作业部署为 SQL Edge 流式处理作业。 可以使用通过 SQL Edge 模块的 `module twin's desired properties` 选项公开的 ASAJobInfo 参数，将 Azure 流分析作业部署到 SQL Edge：

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
| SqlPackage | 包含 SQL 数据库 DAC 包的 *.zip 文件的 Azure Blob 存储 URI。
| ASAJobInfo | ASA Edge 作业的 Azure Blob 存储 URI。

## <a name="create-an-azure-stream-analytics-edge-job"></a>创建 Azure 流分析 Edge 作业

1. 转到 Azure 门户。

2. 创建新的“Azure IoT Edge 流分析”作业。 选择面向“Edge”的宿主环境。

3. 定义 Azure 流分析作业的输入和输出。 你将在此处设置的每个 SQL 输出都将绑定到数据库的单个表中。 如果需要将数据流式传输到多个表，则需要创建多个 SQL 数据库输出。 可以将 SQL 输出配置为指向不同的数据库。

    输入。 选择“EdgeHub”作为 Edge 作业的输入，并提供资源信息。

    输出。 选择“SQL 数据库”作为输出。 选择“手动提供 SQL 数据库设置”。 提供数据库和表的配置详细信息。

    |字段      | 说明 |
    |---------------|-------------|
    |输出别名 | 输出的名称。|
    |数据库 | SQL 数据库的名称。 它必须是 SQL Edge 实例上存在的数据库的有效名称。|
    |服务器名称 | SQL 实例的名称（或 IP 地址）和端口号详细信息。 对于 SQL Edge 部署，可以将“tcp:.,1433”用于服务器名称。|
    |用户名 | SQL 登录帐户，它对之前指定的数据库的数据具有读取和写入访问权限。|
    |密码 | 前面指定的 SQL 登录帐户的密码。|
    |表 | 流式处理作业输出的表的名称。|
    |继承分区| 启用继承上一个查询步骤或输入的分区方案。 启用此选项后，写入到基于磁盘的表以及对作业使用完全并行拓扑时，吞吐量预期会提高。|
    |批大小| 随每个大容量插入事务一起发送的最大记录数。|

    下面是输入/输出配置示例：

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
    > 有关 Azure 流分析的 SQL 输出适配器的详细信息，请参阅[从 Azure 流分析输出到 Azure SQL 数据库](../stream-analytics/stream-analytics-sql-output-perf.md)。

4. 定义 Edge 作业的 ASA 作业查询。 此查询应使用定义的输入/输出别名作为查询中的输入和输出名称。 有关详细信息，请参阅[流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)。

5. 设置 Edge 作业的存储帐户设置。 存储帐户作为 Edge 作业的发布目标。

6. 在“配置”下，选择“发布”，然后选择“发布”按钮。 保存 SAS URI 以用于 SQL Edge 模块。

## <a name="deploy-azure-stream-analytics-edge-job-to-sql-edge"></a>向 SQL Edge 部署 Azure 流分析 Edge 作业

若要将流式处理作业部署到 SQL Edge 模块，请更新 SQL Edge 模块配置，使其包含之前步骤中流式处理作业的 SAS URI。 若要更新 SQL Edge 模块：

1. 在 Azure 门户中，转到 IoT 中心部署。

2. 在左窗格中，选择“IoT Edge”。

3. 在“IoT Edge”页上，找到并选择其中部署了 SQL Edge 模块的 IoT Edge。

4. 在“IoT Edge 设备”设备页上，选择“设置模块”。

5. 在“设置模块”页上，选择针对 SQL Edge 模块的“配置”。

6. 在“IoT Edge 自定义模块”窗格中，选择“设置模块孪生所需的属性”。 更新所需属性，以包含 `ASAJobInfo` 选项的 URI，如以下示例所示。

    > [!NOTE]
    > 下面 JSON 中的 SAS URI 只是其中一例。 将 URI 替换为部署中的实际 URI。

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "<<<SAS URL For the published ASA Edge Job>>>>"
            }
        }
    ```

7. 选择“保存”。

8. 在“设置模块”页上，选择“下一步”。

9. 在“设置模块”页上，依次选择“下一步”和“提交”。

10. 在模块更新后，流分析作业文件会针对 SQL Edge 实例进行下载、解压缩和部署。

## <a name="next-steps"></a>后续步骤

- [通过 Azure 门户部署 SQL Edge](deploy-portal.md)。

- [流式传输数据](stream-data.md)

- [在 SQL Edge（预览版）中将机器学习和 AI 与 ONNX 结合使用](onnx-overview.md)
