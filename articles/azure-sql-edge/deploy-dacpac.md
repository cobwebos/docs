---
title: '使用 SQL 数据库 DACPAC 和 BACPAC 包-Azure SQL Edge (预览版) '
description: '了解如何在 Azure SQL Edge 中使用 dacpac 和 bacpac (预览) '
keywords: SQL Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 52c8e9586d8ee53cdaac28cb1c48d2927d82c2ed
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462752"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>Sql Edge 中的 SQL 数据库 DACPAC 和 BACPAC 包

Azure SQL Edge（预览）是已优化的关系数据库引擎，更适合 IoT 和边缘部署。 它是在最新版 Microsoft SQL Server 数据库引擎的基础之上构建而成，此引擎提供了业界领先的性能、安全性和查询处理功能。 除了具有 SQL Server 的业界领先的关系数据库管理功能外，Azure SQL Edge 还提供了内置的流式处理功能，可用于实时分析和复杂事件处理。

Azure SQL Edge 还提供 SqlPackage.exe 的本机实现，使你能够在 SQL Edge 部署过程中部署 [Sql 数据库 DACPAC 和 BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) 包。 可以使用通过 SQL Edge 模块的 `module twin's desired properties` 选项公开的 SqlPackage 参数，将 SQL 数据库 dacpacs 部署到 SQL Edge：

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
| SqlPackage | 包含 SQL 数据库 DAC 或 BACPAC 包的 *.zip* 文件的 Azure BLOB 存储 URI。 Zip 文件可以同时包含多个 dac 包或 bacpac 文件。
| ASAJobInfo | ASA Edge 作业的 Azure Blob 存储 URI。

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>结合使用 SQL 数据库 DAC 包与 SQL Edge

若要将 SQL 数据库 DAC 包 `(*.dacpac)` 或 BACPAC 文件 `(*.bacpac)` 与 sql Edge 一起使用，请执行以下步骤：

1. 使用下面所述的机制创建/提取 DAC 包或导出 Bacpac 文件。 
    - 创建或提取 SQL 数据库 DAC 包。 若要了解如何为现有 SQL Server 数据库生成 DAC 包，请参阅[从数据库中提取 DAC](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/)。
    - 导出已部署的 DAC 包或数据库。 有关如何为现有 SQL Server 数据库生成 bacpac 文件的信息，请参阅 [导出数据层应用程序](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application/) 。

2. 压缩 `*.dacpac` 或 `*.bacpac` 文件，并将其上传到 Azure Blob 存储帐户。 若要详细了解如何将文件上传到 Azure Blob 存储，请参阅[使用 Azure 门户上传、下载和列出 Blob](../storage/blobs/storage-quickstart-blobs-portal.md)。

3. 使用 Azure 门户为 zip 文件生成共享访问签名。 有关详细信息，请参阅[使用共享访问签名 (SAS) 委托访问权限](../storage/common/storage-sas-overview.md)。

4. 更新 SQL Edge 模块配置，以包含 DAC 包的共享访问 URI。 若要更新 SQL Edge 模块，请按照以下步骤操作：

    1. 在 Azure 门户中，转到 IoT 中心部署。

    2. 在左窗格中，选择“IoT Edge”。

    3. 在“IoT Edge”页上，找到并选择其中部署了 SQL Edge 模块的 IoT Edge。

    4. 在“IoT Edge 设备”设备页上，选择“设置模块”。

    5. 在“设置模块”页上，选择针对 SQL Edge 模块的“配置”。

    6. 在“IoT Edge 自定义模块”窗格中，选择“设置模块孪生所需的属性”。 更新所需属性，以包含 `SQLPackage` 选项的 URI，如以下示例所示。

        > [!NOTE]
        > 下面 JSON 中的 SAS URI 只是其中一例。 将 URI 替换为部署中的实际 URI。

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "<<<SAS URL for the *.zip file containing the dacpac and/or the bacpac files",
                }
            }
        ```

    7. 选择“保存”。

    8. 在“设置模块”页上，选择“下一步”。

    9. 在“设置模块”页上，依次选择“下一步”和“提交”。

5. 模块更新后，会下载、解压缩包文件并将其部署到 SQL Edge 实例。

每次重新启动 Azure SQL Edge 容器 `*.dacpac` 时，都会下载文件包并评估其是否有更改。 如果遇到了新版 dacpac 文件，这些更改就会部署到 SQL Edge 中的数据库。 Bacpac 文件 

## <a name="next-steps"></a>后续步骤

- [通过 Azure 门户部署 SQL Edge](deploy-portal.md)。
- [流式传输数据](stream-data.md)
- [在 SQL Edge（预览版）中将机器学习和 AI 与 ONNX 结合使用](onnx-overview.md)
