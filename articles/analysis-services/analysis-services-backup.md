---
title: Azure Analysis Services 数据库备份和还原 | Microsoft Docs
description: 本文介绍如何从 Azure Analysis Services 数据库备份和还原模型元数据和数据。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: af1850f77c1d13c761bfc2a143074b5067b349b4
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014046"
---
# <a name="analysis-services-database-backup-and-restore"></a>Analysis Services 数据库备份和还原

在 Azure Analysis Services 中备份表格模型数据库与在本地 Analysis Services 中备份大致相同。 主要区别在于存储备份文件的位置。 必须将备份文件保存到 [Azure 存储帐户](../storage/common/storage-account-create.md)的容器中。 可以使用已有存储帐户和容器，也可以在为服务器配置存储设置时创建。

> [!NOTE]
> 创建存储帐户可能会导致新的计费服务。 若要了解详细信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。
> 
> 

> [!NOTE]
> 如果存储帐户位于另一区域中，请将存储帐户防火墙设置配置为允许从“所选网络”**** 进行访问。 在防火墙的“地址范围”**** 中，指定 Analysis Services 服务器所在区域的 IP 地址范围。 支持将存储帐户防火墙设置配置为允许从“所有网络”进行访问，但是最好选择“所选网络”并指定 IP 地址范围。 若要了解详细信息，请参阅[网络连接常见问题解答](analysis-services-network-faq.md#backup-and-restore)。

备份以 .abf 扩展名保存。 对于内存中表格模型，将存储模型数据和元数据。 对于 DirectQuery 表格模型，将仅存储模型元数据。 备份可以进行压缩和加密，具体取决于选择的选项。


## <a name="configure-storage-settings"></a>配置存储设置
备份前，需要为服务器配置存储设置。


### <a name="to-configure-storage-settings"></a>配置存储设置
1.  在 Azure 门户中，转到“设置”****，单击“备份”****。

    ![设置中的备份](./media/analysis-services-backup/aas-backup-backups.png)

2.  单击“已启用”****，并单击“存储设置”****。

    ![启用](./media/analysis-services-backup/aas-backup-enable.png)

3. 选择存储帐户，或新建一个。

4. 选择容器，或新建一个。

    ![选择容器](./media/analysis-services-backup/aas-backup-container.png)

5. 保存备份设置。

    ![保存备份设置](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>使用 SSMS 备份

1. 在 SSMS 中，右键单击某个数据库，转到“备份”****。

2. 在“备份数据库”**** > “备份文件”**** 中，单击“浏览”****。

3. 在“文件另存为”**** 对话框中，验证文件夹路径，并键入备份文件的名称。 

4. 在“备份数据库”**** 对话框中，选择选项。

    “允许覆盖文件”****- 选择此选项可覆盖具有相同名称的备份文件。 如果未选择此选项，则要保存的文件不能与同一位置中已存在的文件具有相同的名称。

    “应用压缩”****- 选择此选项可压缩备份文件。 压缩的备份文件可节省磁盘空间，但需要稍高的 CPU 使用率。 

    “加密备份文件”****- 选择此选项可加密备份文件。 此选项要求用户提供密码来保护备份文件。 密码可防止采用还原操作外的任何其他方式读取备份数据。 如果选择对备份加密，请将密码存储在安全位置。

5. 单击“确定”**** 创建并保存备份文件。


### <a name="powershell"></a>PowerShell
使用 [Backup-ASDatabase](/powershell/module/sqlserver/backup-asdatabase) cmdlet。

## <a name="restore"></a>还原
还原时，备份文件必须在已为服务器配置的存储帐户中。 如果需要将备份文件从本地位置移到存储帐户，请使用 [Microsoft Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)或 [AzCopy](../storage/common/storage-use-azcopy-v10.md) 命令行实用工具。 



> [!NOTE]
> 如果要从本地服务器还原，必须先从该模型的角色中删除所有域用户，并再将这些用户作为 Azure Active Directory 用户重新添加到这些角色。
> 
> 

### <a name="to-restore-by-using-ssms"></a>使用 SSMS 还原

1. 在 SSMS 中，右键单击某个数据库，转到“还原”****。

2. 在“备份文件”**** 的“备份数据库”**** 对话框中，单击“浏览”****。

3. 在“定位数据库文件”**** 对话框中，选择要还原的文件。

4. 在“还原数据库”****，选择数据库。

5. 指定选项。 安全选项必须与备份时使用的备份选项相匹配。


### <a name="powershell"></a>PowerShell

使用 [Restore-ASDatabase](/powershell/module/sqlserver/restore-asdatabase) cmdlet.


## <a name="related-information"></a>相关信息

[Azure 存储帐户](../storage/common/storage-account-create.md)  
[高可用性](analysis-services-bcdr.md)      
[Analysis Services 网络连接常见问题解答](analysis-services-network-faq.md)