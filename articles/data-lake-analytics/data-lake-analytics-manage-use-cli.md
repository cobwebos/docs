---
title: "使用 Azure 命令行接口管理 Azure Data Lake Analytics | Microsoft Docs"
description: "了解如何使用 Azure CLI 管理 Data Lake Analytics 帐户、数据源、作业和用户"
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: d04d33fede00fbc083d71bc486b726bd7f86c6d0
ms.contentlocale: zh-cn
ms.lasthandoff: 06/10/2017


---
# <a name="manage-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>使用 Azure 命令行接口 (CLI) 管理 Azure Data Lake Analytics
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

了解如何使用 Azure CLI 管理 Azure Data Lake Analytics 帐户、数据源、用户和作业。 若要查看使用其他工具的管理主题，请单击上述选项卡选项。


**先决条件**

在开始阅读本教程前，你必须具有：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure CLI**。 请参阅 [安装和配置 Azure CLI](../cli-install-nodejs.md)。
  * 若要完成此演示，请下载并安装 **预发行版** [Azure CLI 工具](https://github.com/MicrosoftBigData/AzureDataLake/releases) 。
* **身份验证**，请使用以下命令：
  
        azure login
    有关使用公司或学校帐户进行身份验证的详细信息，请参阅 [从 Azure CLI 连接到 Azure 订阅](../xplat-cli-connect.md)。
* 使用以下命令**切换到 Azure Resource Manager 模式**：
  
        azure config mode arm

**列出 Data Lake Store 和 Data Lake Analytics 命令：**

    azure datalake store
    azure datalake analytics

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-accounts"></a>管理帐户
运行任何 Data Lake Analytics 作业之前，必须具有 Data Lake Analytics 帐户。 与 Azure HDInsight 不同，Analytics 帐户未运行作业时无需付费。  只需在其运行作业时付费。  有关详细信息，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。  

### <a name="create-accounts"></a>创建帐户
      azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"


### <a name="update-accounts"></a>更新帐户
以下命令会更新现有 Data Lake Analytics 帐户的属性

    azure datalake analytics account set "<Data Lake Analytics Account Name>"


### <a name="list-accounts"></a>列出帐户
列出 Data Lake Analytics 帐户 

    azure datalake analytics account list

列出特定资源组中的 Data Lake Analytics 帐户

    azure datalake analytics account list -g "<Azure Resource Group Name>"

获取特定 Data Lake Analytics 帐户的详细信息

    azure datalake analytics account show -g "<Azure Resource Group Name>" -n "<Data Lake Analytics Account Name>"

### <a name="delete-data-lake-analytics-accounts"></a>删除 Data Lake Analytics 帐户
      azure datalake analytics account delete "<Data Lake Analytics Account Name>"


<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>管理帐户数据源
Data Lake Analytics 当前支持以下数据源：

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure 存储](../storage/storage-introduction.md)

创建 Analytics 帐户时，必须指定一个 Azure Data Lake 存储帐户作为默认存储帐户。 默认 ADL 帐户用于存储作业元数据和作业审核日志。 创建 Analytics 帐户后，可添加其他 Data Lake 存储帐户和/或 Azure 存储帐户。 

### <a name="find-the-default-adl-storage-account"></a>查找默认 ADL 存储帐户
    azure datalake analytics account show "<Data Lake Analytics Account Name>"

值列于 properties:datalakeStoreAccount:name 下。

### <a name="add-additional-azure-blob-storage-accounts"></a>添加其他 Azure Blob 存储帐户
      azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -b "<Azure Blob Storage Account Short Name>" -k "<Azure Storage Account Key>"

> [!NOTE]
> 仅支持 Blob 存储短名称。  请勿使用 FQDN，例如“myblob.blob.core.windows.net”。
> 
> 

### <a name="add-additional-data-lake-store-accounts"></a>添加其他 Data Lake Store 帐户
      azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -l "<Data Lake Store Account Name>" [-d]

[-d] 为可选开关，用于指示正在添加的 Data Lake 是否为默认 Data Lake 帐户。 

### <a name="update-existing-data-source"></a>更新现有数据源
将现有 Data Lake Store 帐户设置为默认帐户：

      azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -l "<Azure Data Lake Store Account Name>" -d

更新现有 Blob 存储帐户密钥：

      azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -b "<Blob Storage Account Name>" -k "<New Blob Storage Account Key>"

### <a name="list-data-sources"></a>列出数据源：
    azure datalake analytics account show "<Data Lake Analytics Account Name>"

![Data Lake Analytics 列表数据源](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>删除数据源：
删除 Data Lake Store 帐户：

      azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Azure Data Lake Store Account Name>"

删除 Blob 存储帐户：

      azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Blob Storage Account Name>"

## <a name="manage-jobs"></a>管理作业
必须拥有 Data Lake Analytics 帐户才可创建作业。  有关详细信息，请参阅[管理 Data Lake Analytics 帐户](#manage-accounts)。

### <a name="list-jobs"></a>列出作业
      azure datalake analytics job list -n "<Data Lake Analytics Account Name>"

![Data Lake Analytics 列表数据源](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>获取作业详细信息
      azure datalake analytics job show -n "<Data Lake Analytics Account Name>" -j "<Job ID>"

### <a name="submit-jobs"></a>提交作业
> [!NOTE]
> 作业的默认优先级为 1000，默认并行度为 1。
> 
> 

    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"

### <a name="cancel-jobs"></a>取消作业
使用 list 命令查找作业 ID，然后使用 cancel 来取消相应作业。

      azure datalake analytics job list -n "<Data Lake Analytics Account Name>"
      azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job ID>"

## <a name="manage-catalog"></a>管理目录
可以使用 -SQL 目录组织数据和代码，以便通过 U-SQL 脚本共享数据和代码。 目录启用 Azure Data Lake 中数据的最高可能性能。 有关详细信息，请参阅 [使用 U-SQL 目录](data-lake-analytics-use-u-sql-catalog.md)。

### <a name="list-catalog-items"></a>列出目录项
    #List databases
    azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t database

    #List tables
    azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t table

类型包括数据库、架构、程序集、外部数据源、表、表值函数或表统计信息。

<!-- ################################ -->
<!-- ################################ -->
## <a name="use-arm-groups"></a>使用 ARM 组
应用程序通常由许多组件构成，例如 Web 应用、数据库、数据库服务器、存储和第三方服务。 你可以使用 Azure 资源管理器 (ARM) 以组（称为 Azure 资源组）的方式处理应用程序中的资源。 你可以通过一个协调的操作为应用程序部署、更新、监视或删除所有资源。 你可以使用一个模板来完成部署，该模板适用于不同的环境，例如测试、过渡和生产。 你可以通过查看整个组的累积费用，明确了解组织的帐单开支。 有关详细信息，请参阅 [Azure Resource Manager 概述](../azure-resource-manager/resource-group-overview.md)。 

Data Lake Analytics 服务可包括以下组件：

* Azure Data Lake Analytics 帐户
* 所需的默认 Azure Data Lake 存储帐户
* 其他 Azure Data Lake 存储帐户
* 其他 Azure 存储帐户

可在一个 ARM 组下创建所有这些组件，使其更易于管理。

![Azure Data Lake Analytics 帐户和存储](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Data Lake Analytics 帐户和从属存储帐户必须位于同一个 Azure 数据中心。
但 ARM 组可以位于不同的数据中心。  

## <a name="see-also"></a>另请参阅
* [Microsoft Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [通过 Azure 门户实现 Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)
* [使用 Azure 门户管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)
* [使用 Azure 门户监视 Azure Data Lake Analytics 作业以及对其进行故障排除](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)


