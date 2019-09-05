---
title: PowerShell 脚本：将 blob 数据集添加到 Azure 数据共享 |Microsoft Docs
description: 此 PowerShell 脚本将 blob 数据集添加到现有共享。
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 95a10b112c9f6448c437f20ee95f808632a31d2d
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307299"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>使用 PowerShell 在 Azure 中创建数据共享

此 PowerShell 脚本将 blob 数据集添加到现有的数据共享。

## <a name="sample-script"></a>示例脚本

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$blobDatasetName = "<Dataset name>"
$blobContainer = "<Blob container name>"
$blobFilePath = "<Blob file path>"
$storageAccountResourceId = "<Storage account resource id>"

#Add a blob dataset to the datashare
New-AzDataShareDataSet -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $blobDataSetName -StorageAccountResourceId $storageAccountResourceId -FilePath $blobFilePath

```


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令： 

| Command | 说明 |
|---|---|
| [新-AzDataShareDataSet](/powershell/module/az.datashare/new-azdatasharedataset?view=azps-2.6.0) | 将数据集添加到数据共享。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在[Azure 数据共享 powershell 示例](../../samples-powershell.md)中找到其他 Azure 数据共享 powershell 脚本示例。
