---
title: 数据科学虚拟机数据引入工具 - Azure | Microsoft 文档
description: 了解 Data Science Virtual Machine 上预安装的数据引入工具和实用工具。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: vijetaj
ms.openlocfilehash: 58bfab43d39f050e19687c30a61e05892fffc3f2
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70060604"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>数据科学虚拟机数据引入工具

作为数据科学或 AI 项目中的第一项技术步骤, 你必须确定要使用的数据集并将其引入分析环境。 Data Science Virtual Machine (DSVM) 提供了工具和库, 可将来自不同源的数据引入到本地 DSVM 上的分析数据存储中, 或引入到云中或本地的数据平台。

下面是 DSVM 中提供的一些数据移动工具。

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| 它是什么？   | 用于将数据从 Azure Blob 存储复制到 Azure Data Lake Store 的工具。 此外，也可在两个 Azure Data Lake Store 帐户之间复制数据。      |
| 支持的 DSVM 版本      | Windows      |
| 典型用途      | 将多个 blob 从 Azure Blob 存储导入 Azure Data Lake Store。      |
|  如何使用/运行它？    |   打开命令提示符并键入`adlcopy`以获得帮助。    |
| 指向示例的链接      | [使用 AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| DSVM 上的相关工具      | AzCopy、Azure CLI     |

## <a name="azure-cli"></a>Azure CLI

|    |           |
| ------------- | ------------- |
| 它是什么？   | Azure 的管理工具。 它还包含用于从 azure 数据平台 (如 Azure Blob 存储和 Azure Data Lake Store) 移动数据的命令谓词。     |
| 支持的 DSVM 版本      | Windows、Linux     |
| 典型用途      | 在 Azure 存储和 Azure Data Lake Store 中导入和导出数据。      |
|  如何使用/运行它？    |   打开命令提示符并键入`az`以获得帮助。    |
| 指向示例的链接      | [使用 Azure CLI](https://docs.microsoft.com/cli/azure)     |
| DSVM 上的相关工具      | AzCopy、AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| 它是什么？   | 用于在本地文件、Azure Blob 存储、文件和表之间复制数据的工具。      |
| 支持的 DSVM 版本      | Windows      |
| 典型用途      | 将文件复制到 Azure Blob 存储并在帐户之间复制 blob。      |
|  如何使用/运行它？    |   打开命令提示符并键入`azcopy`以获得帮助。    |
| 指向示例的链接      | [Windows 上的 AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| DSVM 上的相关工具      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB 数据迁移工具

|    |           |
| ------------- | ------------- |
| 它是什么？   | 用于将数据从各种源导入 Azure Cosmos DB 的工具。 这些源包括 JSON 文件、CSV 文件、SQL、MongoDB、Azure 表存储、Amazon DynamoDB 和 Azure Cosmos DB SQL API 集合。      |
| 支持的 DSVM 版本      | Windows      |
| 典型用途      | 将文件从 VM 导入到 CosmosDB, 将数据从 Azure 表存储导入到 CosmosDB, 并将数据从 Microsoft SQL Server 数据库导入到 CosmosDB。     |
|  如何使用/运行它？    |   若要使用命令行版本, 请打开命令提示符并键入`dt`。 若要使用 GUI 工具, 请打开命令提示符并键入`dtui`。    |
| 指向示例的链接      | [CosmosDB 导入数据](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| DSVM 上的相关工具      | AzCopy、AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| 它是什么？   | 在 SQL Server 和数据文件之间复制数据的 SQL Server 工具。      |
| 支持的 DSVM 版本      | Windows      |
| 典型用途      | 将 CSV 文件导入到 SQL Server 表中, 并将 SQL Server 表导出到文件。      |
|  如何使用/运行它？    |   打开命令提示符并键入`bcp`以获得帮助。    |
| 指向示例的链接      | [bcp 实用工具](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| DSVM 上的相关工具      | SQL Server、sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| 它是什么？   | 用于在 Linux 文件系统中装载 Azure Blob 存储容器的工具。      |
| 支持的 DSVM 版本      | Linux      |
| 典型用途      | 读取和写入容器中的 blob。      |
|  如何使用和运行它？    |   在终端中运行 _blobfuse_。    |
| 指向示例的链接      | [GitHub 上的 blobfuse](https://github.com/Azure/azure-storage-fuse)      |
| DSVM 上的相关工具      | Azure CLI      |


## <a name="microsoft-data-management-gateway"></a>Microsoft 数据管理网关

|    |           |
| ------------- | ------------- |
| 它是什么？   | 将本地数据源连接到云服务来使用的工具。      |
| 支持的 DSVM 版本      | Windows      |
| 典型用途      | 将虚拟机连接到本地数据源。      |
|  如何使用/运行它？    |   从 "开始" 菜单打开 Microsoft 数据管理网关。    |
| 指向示例的链接      | [数据管理网关](https://msdn.microsoft.com/library/dn879362.aspx)      |
| DSVM 上的相关工具      | AzCopy、AdlCopy、bcp    |
