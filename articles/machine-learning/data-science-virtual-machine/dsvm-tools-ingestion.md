---
title: 数据科学虚拟机数据引入工具 - Azure | Microsoft 文档
description: 数据科学虚拟机数据引入工具
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 7aeb0476fffb8c9e5cf2b0b5d89a2a387bd6364a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>数据科学虚拟机数据引入工具

数据科学或 AI 项目中的第一个技术步骤之一就是，识别要使用的数据集并将其引入到分析环境中。 数据科学虚拟机 (DSVM) 提供工具和库，可将来自不同来源的数据导入 DSVM 上的本地分析数据存储，或云、本地的数据平台中。 

以下是我们在 DSVM 上提供的一些数据移动工具。 

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| 它是什么？   | 可将数据从 Azure 存储 blob 复制到 Azure Data Lake Store 的工具 此外，也可在两个 Azure Data Lake Store 帐户之间复制数据。      |
| 支持的 DSVM 版本      | Windows      |
| 典型用途      | 将多个 blob 从 Azure 存储复制到 Azure Data Lake Store。      |
|  如何使用/运行它？    |   打开命令提示符，然后键入 `adlcopy` 获取帮助。    |
| 示例链接      | [使用 AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| DSVM 上的相关工具      | AzCopy、Azure 命令行     |

## <a name="azure-command-line"></a>Azure 命令行

|    |           |
| ------------- | ------------- |
| 它是什么？   | Azure 的管理工具。 它还包含可从 Azure 数据平台（如 Azure 存储 blob、Azure Data Lake Storage）移动数据的命令谓词     |
| 支持的 DSVM 版本      | Windows、Linux     |
| 典型用途      | 从 Azure 存储、Azure Data Lake Store 导出数据或将数据导入其中      |
|  如何使用/运行它？    |   打开命令提示符，然后键入 `az` 获取帮助。    |
| 示例链接      | [使用 Azure CLI](https://docs.microsoft.com/cli/azure)     |
| DSVM 上的相关工具      | AzCopy、AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| 它是什么？   | 用于从本地文件、Azure 存储 blob、文件和表复制数据以及将数据复制到其中的工具。      |
| 支持的 DSVM 版本      | Windows      |
| 典型用途      | 将文件复制到 blob 存储，在帐户之间复制 blob。      |
|  如何使用/运行它？    |   打开命令提示符，然后键入 `azcopy` 获取帮助。    |
| 示例链接      | [Windows 上的 AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| DSVM 上的相关工具      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB 数据迁移工具

|    |           |
| ------------- | ------------- |
| 它是什么？   | 可从各种源（包括 JSON 文件、CSV 文件、SQL、MongoDB、Azure 表存储、Amazon DynamoDB 和 Azure Cosmos DB SQL API 集合）将数据导入 Azure Cosmos DB 中的工具。      |
| 支持的 DSVM 版本      | Windows      |
| 典型用途      | 将文件从虚拟机导入 CosmosDB，将数据从 Azure 表存储导入 CosmosDB，或将数据从 SQL Server 数据库导入到 CosmosDB。     |
|  如何使用/运行它？    |   要使用命令行版本，请打开命令提示符，然后键入 `dt`。 要使用 GUI 工具，请打开命令提示符，然后键入 `dtui`。    |
| 示例链接      | [CosmosDB 导入数据](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| DSVM 上的相关工具      | AzCopy、AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| 它是什么？   | 在 SQL Server 和数据文件之间复制数据的 SQL Server 工具。      |
| 支持的 DSVM 版本      | Windows      |
| 典型用途      | 将 CSV 文件导入到 SQL Server 表中，将 SQL Server 表导出到文件。      |
|  如何使用/运行它？    |   打开命令提示符，然后键入 `bcp` 获取帮助。    |
| 到示例的链接      | [大容量复制实用工具](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| DSVM 上的相关工具      | SQL Server、sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| 它是什么？   | 用于在 Linux 文件系统中装载 Azure blob 容器的工具。      |
| 支持的 DSVM 版本      | Linux      |
| 典型用途      | 读取和写入到容器中的 blob      |
|  如何使用/运行它？    |   在终端中运行 _blobfuse_。    |
| 示例链接      | [GitHub 上的 blobfuse](https://github.com/Azure/azure-storage-fuse)      |
| DSVM 上的相关工具      | Azure 命令行      |


## <a name="microsoft-data-management-gateway"></a>Microsoft 数据管理网关

|    |           |
| ------------- | ------------- |
| 它是什么？   | 将本地数据源连接到云服务来使用的工具。      |
| 支持的 DSVM 版本      | Windows      |
| 典型用途      | 将虚拟机连接到本地数据源。      |
|  如何使用/运行它？    |   从“开始”菜单启动“Microsoft 数据管理网关”。    |
| 示例链接      | [数据管理网关](https://msdn.microsoft.com/library/dn879362.aspx)      |
| DSVM 上的相关工具      | AzCopy、AdlCopy、bcp    |
