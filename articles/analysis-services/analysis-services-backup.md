---
title: "Azure Analysis Services 数据库备份和还原 | Microsoft Docs"
description: "介绍如何备份和还原 Azure Analysis Services 数据库。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: fb660384f2f9f569bcfbe7fa7d5c1f7ce772cacd
ms.lasthandoff: 04/20/2017


---

# <a name="backup-and-restore"></a>备份和还原

在 Azure Analysis Services 中备份表格模型数据库与在本地 Analysis Services 中备份大致相同。 主要区别在于存储备份文件的位置。 必须将备份文件保存到 [Azure 存储帐户](../storage/storage-create-storage-account.md)的容器中。 可以使用已有存储帐户和容器，也可以在为服务器配置存储设置时创建。

> [!NOTE]
> 创建存储帐户可能会导致新的计费服务。 若要了解详细信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。
> 
> 

使用 .abf 扩展名保存备份。 对于内存中表格模型，将存储模型数据和元数据。 对于直接查询表格模型，将仅存储模型元数据。 备份可以进行压缩和加密，具体取决于选择的选项。 



## <a name="configure-storage-settings"></a>配置存储设置
备份前，需要为服务器配置存储设置。


### <a name="to-configure-storage-settings"></a>配置存储设置
1.  在 Azure 门户中，转到“设置”，单击“备份”。

    ![设置中的备份](./media/analysis-services-backup/aas-backup-backups.png)

2.  单击“已启用”，然后单击“存储设置”。

    ![启用](./media/analysis-services-backup/aas-backup-enable.png)

3. 选择存储帐户，或新建一个。

4. 选择容器，或新建一个。

    ![选择容器](./media/analysis-services-backup/aas-backup-container.png)

5. 保存备份设置。 每当更改存储设置或启用/禁用备份后，都必须保存更改。

    ![保存备份设置](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>备份

### <a name="to-backup-by-using-ssms"></a>使用 SSMS 备份

1. 在 SSMS 中，右键单击某个数据库，转到“备份”。

2. 在“备份数据库” > “备份文件”中，单击“浏览”。

3. 在“文件另存为”对话框中，验证文件夹路径，然后键入备份文件的名称。 默认情况下，文件名称具有 .abf 扩展名。 

4. 在“备份数据库”对话框中，选择选项。

    “允许覆盖文件”- 选择此选项可覆盖具有相同名称的备份文件。 如果未选择此选项，则要保存的文件不能与同一位置中已存在的文件具有相同的名称。

    “应用压缩”- 选择此选项可压缩备份文件。 压缩的备份文件可节省磁盘空间，但需要稍高的 CPU 使用率。 

    “加密备份文件”- 选择此选项可加密备份文件。 此选项要求用户提供密码来保护备份文件。 密码可防止采用还原操作外的任何其他方式读取备份数据。 如果选择对备份加密，请将密码存储在安全位置。

5. 单击“确定”创建并保存备份文件。


### <a name="powershell"></a>PowerShell
使用 [Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet) cmdlet。

## <a name="restore"></a>还原
还原时，备份文件必须在已为服务器配置的存储帐户中。 如果需要将备份文件从本地位置移到存储帐户，请使用 [Microsoft Azure 存储资源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)或 [AzCopy](../storage/storage-use-azcopy.md) 命令行实用工具。 

如果要从本地 SQL Server Analysis Services 服务器还原表格 1200 模型数据库，必须先从该模型的角色中删除所有域用户，然后再将这些用户作为 Azure Active Directory 用户重新添加到这些角色。 角色将是相同的。

### <a name="to-restore-by-using-ssms"></a>使用 SSMS 还原

1. 在 SSMS 中，右键单击某个数据库，转到“还原”。

2. 在“备份文件”的“备份数据库”对话框中，单击“浏览”。

3. 在“定位数据库文件”对话框中，选择要还原的文件。

4. 在“还原数据库”，选择数据库。

5. 指定选项。 安全选项必须与备份时使用的备份选项相匹配。


### <a name="powershell"></a>PowerShell

使用 [Restore-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet) cmdlet.


## <a name="related-information"></a>相关信息

[Azure 存储帐户](../storage/storage-create-storage-account.md)  
[高可用性](analysis-services-bcdr.md)     
[管理 Azure Analysis Services](analysis-services-manage.md)

