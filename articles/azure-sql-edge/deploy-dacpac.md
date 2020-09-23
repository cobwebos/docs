---
title: 使用 SQL 数据库 DACPAC 和 BACPAC 包-Azure SQL Edge
description: 了解如何在 Azure SQL Edge 中使用 dacpac 和 bacpac
keywords: SQL Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 6c8be6e67b1d7b919d6ea221c473c8975e559658
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90887490"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>Sql Edge 中的 SQL 数据库 DACPAC 和 BACPAC 包

Azure SQL Edge 是已针对 IoT 和边缘部署进行了优化的关系数据库引擎。 它基于 Microsoft SQL 数据库引擎的最新版本，提供业界领先的性能、安全性和查询处理功能。 除了具有 SQL Server 的业界领先的关系数据库管理功能外，Azure SQL Edge 还提供了内置的流式处理功能，可用于实时分析和复杂事件处理。

Azure SQL Edge 还提供 SqlPackage.exe 的本机实现，使你能够在 SQL Edge 部署过程中部署 [Sql 数据库 DACPAC 和 BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) 包。 

可以使用环境变量将 SQL 数据库 dacpac 和 bacpac 包部署到 SQL Edge `MSSQL_PACKAGE` 。 可以通过以下任一方式配置环境变量。  
- SQL 容器内包含 dacpac 和 bacpac 文件的本地文件夹位置。 可以使用装入点或数据卷容器将此文件夹映射到主机卷。 
- SQL 容器中到 dacpac 或 bacpac 文件的本地文件路径。 可以使用装入点或数据卷容器将此文件路径映射到主机卷。 
- SQL 容器中的本地文件路径映射到包含 dacpac 或 bacpac 文件的 zip 文件。 可以使用装入点或数据卷容器将此文件路径映射到主机卷。 
- 包含 dacpac 和 bacpac 文件的 zip 文件的 Azure Blob SAS URL。
- Dacpac 或 bacpac 文件的 Azure Blob SAS URL。 

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>结合使用 SQL 数据库 DAC 包与 SQL Edge

若要 `(*.dacpac)` 使用 Azure Blob 存储和 zip 文件部署 (或导入 SQL 数据库 DAC 包或 BACPAC 文件) `(*.bacpac)` ，请执行以下步骤。 

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

    5. 在 " **设置模块** " 页上，单击 "Azure SQL Edge" 模块。

    6. 在 " **更新 IoT Edge 模块** " 窗格上，选择 " **环境变量**"。 添加 `MSSQL_PACKAGE` 环境变量，并将上面步骤3中生成的 SAS URL 指定为环境变量的值。 

    7. 选择“更新”  。

    8. 在 " **设置模块** " 页上，选择 " **查看 + 创建**"。

    9. 在 " **设置模块** " 页上，选择 " **创建**"。

5. 模块更新后，会下载、解压缩包文件，然后针对 SQL Edge 实例部署包文件。

在每次重新启动 Azure SQL Edge 容器时，SQL Edge 都会尝试下载压缩的文件包并评估更改。 如果遇到了新版 dacpac 文件，这些更改就会部署到 SQL Edge 中的数据库。

## <a name="next-steps"></a>后续步骤

- [通过 Azure 门户部署 SQL Edge](deploy-portal.md)。
- [流式传输数据](stream-data.md)
- [在 SQL Edge 中将机器学习和 AI 与 ONNX 结合使用](onnx-overview.md)
