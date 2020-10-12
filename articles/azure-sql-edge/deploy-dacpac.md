---
title: 使用 SQL 数据库 DACPAC 和 BACPAC 包 - Azure SQL Edge
description: 了解如何使用 Azure SQL Edge 中的 dacpacs 和 bacpacs
keywords: SQL Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: e9c8c58c6be8d2c2a85e56690903e6b54f0e4a0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91293894"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>SQL Edge 中的 SQL 数据库 DACPAC 和 BACPAC 包

Azure SQL Edge 是已针对 IoT 和边缘部署进行了优化的关系数据库引擎。 它是在最新版 Microsoft SQL 数据库引擎的基础之上构建而成，此引擎提供了业界领先的性能、安全性和查询处理功能。 除了具有 SQL Server 的业界领先的关系数据库管理功能外，Azure SQL Edge 还提供了内置的流式处理功能，可用于实时分析和复杂事件处理。

Azure SQL Edge 提供原生机制，支持在部署 SQL Edge 期间或之后部署 [SQL 数据库 DACPAC 和 BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) 包。

可以使用 `MSSQL_PACKAGE` 环境变量将 SQL 数据库 dacpac 和 bacpac 包部署到 SQL Edge。 可以通过以下任一方式配置环境变量。  
- SQL 容器内包含 dacpac 和 bacpac 文件的本地文件夹位置。 可以使用装入点或数据卷容器将此文件夹映射到主机卷。 
- SQL 容器中映射到 dacpac 或 bacpac 文件的本地文件路径。 可以使用装入点或数据卷容器将此文件路径映射到主机卷。 
- SQL 容器中映射到包含 dacpac 或 bacpac 文件的 zip 文件的本地文件路径。 可以使用装入点或数据卷容器将此文件路径映射到主机卷。 
- 包含 dacpac 和 bacpac 文件的 zip 文件的 Azure Blob SAS URL。
- dacpac 或 bacpac 文件的 Azure Blob SAS URL。 

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>结合使用 SQL 数据库 DAC 包与 SQL Edge

若要使用 Azure Blob 存储和 zip 文件部署（或导入）SQL 数据库 DAC 包 `(*.dacpac)` 或 BACPAC 文件 `(*.bacpac)`，请按照以下步骤进行操作。 

1. 使用下面所述的机制创建/提取 DAC 包或导出 Bacpac 文件。 
    - 创建或提取 SQL 数据库 DAC 包。 若要了解如何为现有 SQL Server 数据库生成 DAC 包，请参阅[从数据库中提取 DAC](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/)。
    - 导出已部署的 DAC 包或数据库。 若要了解如何为现有 SQL Server 数据库生成 bacpac 文件，请参阅[导出数据层应用程序](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application/)。

2. 压缩 `*.dacpac` 或 `*.bacpac` 文件并将其上传到 Azure Blob 存储帐户。 若要详细了解如何将文件上传到 Azure Blob 存储，请参阅[使用 Azure 门户上传、下载和列出 Blob](../storage/blobs/storage-quickstart-blobs-portal.md)。

3. 使用 Azure 门户为 zip 文件生成共享访问签名。 有关详细信息，请参阅[使用共享访问签名 (SAS) 委托访问权限](../storage/common/storage-sas-overview.md)。

4. 更新 SQL Edge 模块配置，以包含 DAC 包的共享访问 URI。 若要更新 SQL Edge 模块，请按照以下步骤操作：

    1. 在 Azure 门户中，转到 IoT 中心部署。

    2. 在左窗格中，选择“IoT Edge”。

    3. 在“IoT Edge”页上，找到并选择其中部署了 SQL Edge 模块的 IoT Edge。

    4. 在“IoT Edge 设备”设备页上，选择“设置模块”。

    5. 在“设置模块”页上，单击“Azure SQL Edge”模块。

    6. 在“更新 IoT Edge 模块”窗格上，选择“环境变量” 。 添加 `MSSQL_PACKAGE` 环境变量，并将上面步骤 3 中生成的 SAS URL 指定为环境变量的值。 

    7. 选择“更新”  。

    8. 在“设置模块”页上，选择“查看 + 创建” 。

    9. 在“设置模块”页上，选择“创建” 。

5. 在模块更新后，会针对 SQL Edge 实例下载、解压缩和部署包文件。

每当 Azure SQL Edge 容器重启时，SQL Edge 都会尝试下载压缩的文件包并评估是否有更改。 如果遇到了新版 dacpac 文件，这些更改就会部署到 SQL Edge 中的数据库。

## <a name="known-issue"></a>已知问题

在某些 DACPAC 或 BACPAC 部署过程中，用户可能会遇到命令超时，超时会导致 dacpac 部署操作失败。 如果遇到此问题，请使用 SQLPackage.exe（或 SQL 客户端工具）手动应用 DACPAC 或 BACPAC。 

## <a name="next-steps"></a>后续步骤

- [通过 Azure 门户部署 SQL Edge](deploy-portal.md)。
- [流式传输数据](stream-data.md)
- [在 SQL Edge 中将机器学习和 AI 与 ONNX 结合使用](onnx-overview.md)
