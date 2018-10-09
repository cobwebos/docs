---
title: 升级到常规用途 v2 存储帐户 - Azure 存储 | Microsoft Docs
description: 升级到常规用途 v2 存储帐户。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/11/2018
ms.author: tamram
ms.openlocfilehash: 6e77c4836531a7efd0b52b9a411ac40ff6a613fa
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224488"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>升级到常规用途 v2 存储帐户

常规用途 v2 存储帐户支持最新的 Azure 存储功能，并整合了常规用途 v1 和 Blob 存储帐户的所有功能。 建议将常规用途 v2 帐户用于大多数存储方案。 常规用途 v2 帐户为 Azure 存储提供最低的每 GB 容量价格，以及具有行业竞争力的事务处理价格。

从常规用途 v1 存储帐户或 Blob 存储帐户升级到常规用途 v2 存储帐户很简单。 可以使用 Azure 门户、PowerShell 或 Azure CLI 进行升级。 升级帐户的操作不能撤消，并且可能会收费。

## <a name="upgrade-using-the-azure-portal"></a>使用 Azure 门户进行升级

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到存储帐户。
3. 在“设置”部分单击“配置”。
4. 在“帐户类型”下单击“升级”。
5. 在“确认升级”下键入帐户名称。 
6. 单击边栏选项卡底部的“升级”。

## <a name="upgrade-with-powershell"></a>使用 PowerShell 进行升级

若要使用 PowerShell 将常规用途 v1 帐户升级为常规用途 v2 帐户，请先更新 PowerShell，以便使用最新版本的 **AzureRm.Storage** 模块。 请参阅[安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)，了解如何安装 PowerShell。 

接下来，调用以下命令来升级帐户，使用自己的资源组和存储帐户的名称来代替相应项：

```powershell
Set-AzureRmStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

## <a name="upgrade-with-azure-cli"></a>使用 Azure CLI 进行升级

若要使用 Azure CLI 将常规用途 v1 帐户升级为常规用途 v2 帐户，请先安装最新版的 Azure CLI。 请参阅 [Install the Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)（安装 Azure CLI 2.0），了解如何安装 CLI。 

接下来，调用以下命令来升级帐户，使用自己的资源组和存储帐户的名称来代替相应项：

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
``` 

## <a name="specify-an-access-tier-for-blob-data"></a>指定 Blob 数据的访问层

常规用途 v2 帐户支持所有 Azure 存储服务和数据对象，但访问层仅适用于 Blob 存储中的块 Blob。 升级到常规用途 v2 存储帐户时，可以为 Blob 数据指定访问层。 

有了访问层，就可以根据预期的使用模式选择最经济有效的存储。 块 Blob 可以存储在热、冷或存档层中。 有关访问层的详细信息，请参阅 [Azure Blob 存储：热、冷和存档存储层](../blobs/storage-blob-storage-tiers.md)。

默认情况下，新的存储帐户在热访问层中创建，常规用途 v1 存储帐户升级到热访问层。 若要探索将哪个访问层用于升级后数据，请考虑一下你的方案。 可以通过两个典型的用户方案迁移到常规用途 v2 帐户：

* 已经有了一个常规用途 v1 存储帐户，想要使用适合 Blob 数据的存储层来评估对常规用途 v2 存储帐户所做的更改。
* 已经决定使用常规用途 v2 存储帐户，或者已经有了一个这种帐户，想要评估一下是应使用热存储层还是冷存储层来存储 Blob 数据。

在这两种情况下，首要任务都是评估对存储在常规用途 v2 存储帐户中的数据进行存储、访问和操作所需的成本，并将该成本与当前成本进行比较。

### <a name="estimate-costs-for-your-current-usage-patterns"></a>估计当前使用模式的成本

若要估算在特定层的常规用途 v2 存储帐户中存储和访问 Blob 数据的成本，请评估现有的使用模式，或对预期的使用模式进行一个大致的估计。 一般情况下，需了解：

* Blob 存储使用量（以 GB 为单位），包括：
    - 有多少数据存储在存储帐户中？
    - 数据量每月如何变化；新数据是否不断替换旧数据？
* Blob 存储数据的主要访问模式，包括：
    - 从存储帐户读取了多少数据，向其写入了多少数据？ 
    - 对存储帐户中的数据执行了多少读取操作和写入操作？

若要确定最适合需求的访问层，可以先确定 Blob 数据目前使用的容量，以及该数据的使用方式。 

若要在迁移之前收集存储帐户的使用数据，可以使用 [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) 监视存储帐户。 Azure Monitor 会进行日志记录并提供 Azure 服务（包括 Azure 存储）的指标数据。 

若要监视存储帐户中 Blob 的使用数据，请在 Azure Monitor 中启用容量指标。 容量指标记录的数据涉及帐户中的 Blob 每天使用多少存储。 容量指标可以用来估算在存储帐户中存储数据的费用。 若要了解每类帐户的 Blob 存储容量的定价，请参阅[块 Blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。

若要监视 Blob 存储的数据访问模式，请在 Azure Monitor 中启用事务指标。 可以对不同的 Azure 存储操作进行筛选，以便估算每种操作的调用频率。 对于每种类型的帐户，若要了解块 Blob 和追加 Blob 的不同事务类型的定价，请参阅[块 Blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。  

若要详细了解如何从 Azure Monitor 收集指标，请参阅 [Azure Monitor 中的 Azure 存储指标](storage-metrics-in-azure-monitor.md)。

## <a name="next-steps"></a>后续步骤

- [创建存储帐户](storage-quickstart-create-account.md)
- [管理 Azure 存储帐户](storage-account-manage.md)