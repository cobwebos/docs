---
title: PowerShell 脚本 - 使用数据工厂转换云中的数据 | Microsoft Docs
description: 此 PowerShell 脚本通过在 Azure HDInsight Spark 群集上运行 Spark 程序转换云中的数据。
services: data-factory
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: shlo
ms.openlocfilehash: 27458a48c04a6d4ec612252dc298d454a48cf009
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
ms.locfileid: "30165784"
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>PowerShell 脚本 - 使用 Azure 数据工厂转换云中的数据

此示例 PowerShell 脚本通过在 Azure HDInsight Spark 群集上运行 Spark 程序，创建用于转换云中数据的管道。 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>先决条件
* **Azure 存储帐户**。 创建 Python 脚本和输入文件，并将其上传到 Azure 存储。 Spark 程序的输出存储在此存储帐户中。 按需 Spark 群集使用相同的存储帐户作为其主存储。  

### <a name="upload-python-script-to-your-blob-storage-account"></a>将 Python 脚本上传到 Blob 存储帐户
1. 创建包含以下内容的名为 **WordCount_Spark.py** 的 Python 文件： 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. 将 **&lt;storageAccountName&gt;** 替换为 Azure 存储帐户的名称。 然后保存文件。 
3. 在 Azure Blob 存储中，创建名为 **adftutorial** 的容器（如果尚不存在）。 
4. 创建名为 **spark** 的文件夹。
5. 在 **spark** 文件夹中创建名为 **script** 的子文件夹。 
6. 将 **WordCount_Spark.py** 文件上传到 **script** 子文件夹。 


### <a name="upload-the-input-file"></a>上传输入文件
1. 创建包含一些文本的名为 **minecraftstory.txt** 的文件。 Spark 程序会统计此文本中的单词数量。 
2. 在 blob 容器的 `spark` 文件夹中创建一个名为 `inputfiles` 的子文件夹。 
3. 将 `minecraftstory.txt` 上传到 `inputfiles` 子文件夹。 

## <a name="sample-script"></a>示例脚本
> [!IMPORTANT]
> 此脚本在硬盘驱动器上的 c:\ 文件夹中创建 JSON 文件，用于定义数据工厂实体（链接服务、数据集和管道）。

[!code-powershell[main](../../../powershell_scripts/data-factory/transform-data-using-spark/transform-data-using-spark.ps1 "Transform data using Spark")]

## <a name="clean-up-deployment"></a>清理部署

运行示例脚本后，可以使用以下命令删除资源组以及与其关联的所有资源：

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
若要从资源组中删除数据工厂，请运行以下命令： 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令：

| 命令 | 说明 |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 创建用于存储所有资源的资源组。 |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2) | 创建数据工厂。 |
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2linkedservice) | 在数据工厂中创建链接服务。 链接服务可将数据存储或计算链接到数据工厂。 |
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactorv2ypipeline) | 在数据工厂中创建管道。 一个管道包含一个或多个可执行特定操作的活动。 在此管道中，spark 活动通过在 Azure HDInsight Spark 群集上运行程序来转换数据。 |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2pipelinerun) | 为管道创建运行。 换而言之，就是运行管道。 |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | 获取管道中活动的运行（活动运行）的相关详细信息。 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在 [Azure 数据工厂 PowerShell 示例](../samples-powershell.md)中找到其他 Azure 数据工厂 PowerShell 脚本示例。