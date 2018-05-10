---
title: 使用 Azure 命令行接口管理 Azure Data Lake Analytics | Microsoft Docs
description: 了解如何使用 Azure CLI 管理 Data Lake Analytics 帐户、数据源、作业和用户
services: data-lake-analytics
documentationcenter: ''
author: SnehaGunda
manager: Kfile
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/29/2018
ms.author: sngun
ms.openlocfilehash: 9504e1fcbb0128f538cb6e959cf7586f24827836
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="manage-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>使用 Azure 命令行接口 (CLI) 管理 Azure Data Lake Analytics

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

了解如何使用 Azure CLI 管理 Azure Data Lake Analytics 帐户、数据源、用户和作业。 若要查看使用其他工具的管理主题，请单击上述选项卡选项。


**先决条件**

开始学习本教程之前，必须有以下资源：

* Azure 订阅。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* Azure CLI。 请参阅 [安装和配置 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

   * 若要完成此演示，请下载并安装 **预发行版** [Azure CLI 工具](https://github.com/MicrosoftBigData/AzureDataLake/releases) 。

* 使用 `az login` 命令进行身份验证，并选择要使用的订阅。 有关使用公司或学校帐户进行身份验证的详细信息，请参阅 [从 Azure CLI 连接到 Azure 订阅](/cli/azure/authenticate-azure-cli)。

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   现在可以访问 Data Lake Analytics 和 Data Lake Store 命令。 运行以下命令以列出 Data Lake Store 和 Data Lake Analytics 命令：

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>管理帐户

运行任何 Data Lake Analytics 作业之前，必须具有 Data Lake Analytics 帐户。 与 Azure HDInsight 不同，Analytics 帐户未运行作业时无需付费。 只需在其运行作业时付费。  有关详细信息，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。  

### <a name="create-accounts"></a>创建帐户

运行以下命令创建 Data Lake 帐户。 

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>更新帐户

以下命令会更新现有 Data Lake Analytics 帐户的属性

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>列出帐户

列出特定资源组中的 Data Lake Analytics 帐户

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>获取帐户的详细信息

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>删除帐户

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>管理数据源

Data Lake Analytics 目前支持以下两个数据源：

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure 存储](../storage/common/storage-introduction.md)

创建 Analytics 帐户时，必须指定一个 Azure Data Lake 存储帐户作为默认存储帐户。 默认 Data Lake 存储帐户用于存储作业元数据和作业审核日志。 创建 Analytics 帐户后，可添加其他 Data Lake 存储帐户和/或 Azure 存储帐户。 

### <a name="find-the-default-data-lake-store-account"></a>查找默认 Data Lake Store 帐户

可以通过运行 `az dla account show` 命令来查看所用的默认 Data Lake Store 帐户。 默认的帐户名称将在 defaultDataLakeStoreAccount 属性下列出。

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>添加其他 Blob 存储帐户

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> 仅支持 Blob 存储短名称。 请勿使用 FQDN，例如“myblob.blob.core.windows.net”。
> 

### <a name="add-additional-data-lake-store-accounts"></a>添加其他 Data Lake Store 帐户

以下命令使用其他 Data Lake Store 帐户更新指定的 Data Lake Analytics 帐户：

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>更新现有数据源

更新现有 Blob 存储帐户密钥：

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>列出数据源：

列出 Data Lake Store 帐户：

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

列出 Blob 存储帐户：

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Data Lake Analytics 列表数据源](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>删除数据源：
删除 Data Lake Store 帐户：

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

删除 Blob 存储帐户：

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>管理作业
必须拥有 Data Lake Analytics 帐户才可创建作业。  有关详细信息，请参阅[管理 Data Lake Analytics 帐户](#manage-accounts)。

### <a name="list-jobs"></a>列出作业

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Data Lake Analytics 列表数据源](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>获取作业详细信息

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>提交作业

> [!NOTE]
> 作业的默认优先级为 1000，默认并行度为 1。
> 
   ```azurecli
   az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
   ```

### <a name="cancel-jobs"></a>取消作业
使用 list 命令查找作业 ID，并使用 cancel 来取消相应作业。

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="pipelines-and-recurrences"></a>管道和重复周期

获取关于管道和重复周期的相关信息

使用 `az dla job pipeline` 命令查看先前所提交作业的管道信息。

```
az dla job pipeline list --account "<Data Lake Analytics Account Name>"

az dla job pipeline show --account "<Data Lake Analytics Account Name>" --pipeline-identity "<Pipeline ID>"
```

使用 `az dla job recurrence` 命令查看先前所提交作业的重复周期信息。

```
az dla job recurrence list --account "<Data Lake Analytics Account Name>"

az dla job recurrence show --account "<Data Lake Analytics Account Name>" --recurrence-identity "<Recurrence ID>"
```


## <a name="use-azure-resource-manager-groups"></a>使用 Azure 资源管理器组
应用程序通常由许多组件构成，例如 Web 应用、数据库、数据库服务器、存储和第三方服务。 可使用 Azure 资源管理器以组（称为 Azure 资源组）的方式处理应用程序中的资源。 可以通过一个协调的操作为应用程序部署、更新、监视或删除所有资源。 可以使用一个模板来完成部署，该模板适用于不同的环境，例如测试、过渡和生产。 可以通过查看整个组的累积费用，明确了解组织的帐单开支。 有关详细信息，请参阅 [Azure 资源管理器概述](../azure-resource-manager/resource-group-overview.md)。 

Data Lake Analytics 服务可包括以下组件：

* Azure Data Lake Analytics 帐户
* 所需的默认 Azure Data Lake 存储帐户
* 其他 Azure Data Lake 存储帐户
* 其他 Azure 存储帐户

可在一个资源管理器组下创建所有这些组件，使其更易于管理。

![Azure Data Lake Analytics 帐户和存储](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Data Lake Analytics 帐户和从属存储帐户必须位于同一个 Azure 数据中心。
但资源管理器组可以位于不同的数据中心。  

## <a name="see-also"></a>另请参阅
* [Microsoft Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [Get started with Data Lake Analytics using Azure portal](data-lake-analytics-get-started-portal.md)
* [使用 Azure 门户管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)
* [使用 Azure 门户监视 Azure Data Lake Analytics 作业以及对其进行故障排除](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

