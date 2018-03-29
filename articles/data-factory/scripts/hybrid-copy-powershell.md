---
title: PowerShell 脚本：使用数据工厂将数据从本地复制到 Azure | Microsoft Docs
description: 此 PowerShell 脚本将数据从本地 SQL Server 数据库复制到其他 Azure Blob 存储。
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: 5c0252e42dd3dd53a0a95684553b193b983e95ee
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>使用 PowerShell 创建用于将数据从本地复制到 Azure 的数据工厂管道

本 PowerShell 脚本示例在 Azure 数据工厂中创建管道，该管道将数据从本地 SQL Server 数据库复制到 Azure Blob 存储。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>先决条件

- **SQL Server**。 在本示例中，将本地 SQL Server 数据库用作源数据存储。
- **Azure 存储帐户**。 本示例使用 Azure Blob 存储作为“目标/接收器”数据存储。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../../storage/common/storage-create-storage-account.md#create-a-storage-account)一文获取创建步骤。
- 自承载集成运行时。 从[下载中心](https://www.microsoft.com/download/details.aspx?id=39717)下载并运行 MSI 文件，在计算机上安装自承载集成运行时。  

### <a name="create-sample-database-in-sql-server"></a>在 SQL Server 中创建示例数据库
1. 在本地 SQL Server 数据库中，使用以下 SQL 脚本创建名为“emp”的表： 

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. 在该表中插入一些示例数据：

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>示例脚本

> [!IMPORTANT]
> 此脚本在硬盘驱动器上的 c:\ 文件夹中创建 JSON 文件，用于定义数据工厂实体（链接服务、数据集和管道）。

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from on-premises SQL Server -> Azure Blob Storage")]


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
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | 创建数据工厂。 |
| [New-AzureRmDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2linkedserviceencryptedcredential) | 在链接的服务中对凭据进行加密，并使用加密凭据生成新的链接服务定义。 
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2linkedservice) | 在数据工厂中创建链接服务。 链接服务可将数据存储或计算链接到数据工厂。 |
| [Set-AzureRmDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2dataset) | 在数据工厂中创建数据集。 数据集表示管道中活动的输入/输出。 | 
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactorv2ypipeline) | 在数据工厂中创建管道。 一个管道包含一个或多个执行某项操作的活动。 在此管道中，复制活动在 Azure Blob 存储中将数据从一个位置复制到另一个位置。 |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipelinerun) | 为管道创建运行。 换而言之，就是运行管道。 |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | 获取管道中活动的运行（活动运行）的相关详细信息。 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在 [Azure 数据工厂 PowerShell 示例](../samples-powershell.md)中找到其他 Azure 数据工厂 PowerShell 脚本示例。