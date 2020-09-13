---
title: 在 Azure SQL 数据库和 Azure Synapse Analytics 中共享和接收数据
description: 了解如何在 Azure SQL 数据库和 Azure Synapse Analytics 中共享和接收数据
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/28/2020
ms.openlocfilehash: e813921727ee08bf9a76c0a2dbfe15f45fe4db79
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89490065"
---
# <a name="share-and-receive-data-from-azure-sql-database-and-azure-synapse-analytics"></a>在 Azure SQL 数据库和 Azure Synapse Analytics 中共享和接收数据

[!INCLUDE[appliesto-sql](includes/appliesto-sql.md)]

Azure 数据共享支持基于快照的共享 Azure SQL 数据库和 Azure Synapse Analytics (以前称为 Azure SQL DW) 。 本文介绍如何从这些源共享和接收数据。

Azure 数据共享支持在以前的 Azure SQL DW) 中共享 Azure SQL 数据库和 Azure Synapse (分析中的表或视图。 数据使用者可以选择接受将数据作为 csv 或 parquet 文件的 Azure Data Lake Storage Gen2 或 Azure Blob 存储，以及作为表的 Azure SQL 数据库和 Azure Synapse 分析。

在将数据接收到 Azure Data Lake Store Gen2 或 Azure Blob 存储时，如果已存在，则完全快照将覆盖目标文件的内容。
当将数据接收到表中，并且如果目标表尚不存在，Azure 数据共享将创建包含源架构的 SQL 表。 如果已存在具有相同名称的目标表，则将删除该目标表并使用最新的完整快照覆盖它。 当前不支持增量快照。

## <a name="share-data"></a>共享数据

### <a name="prerequisites-to-share-data"></a>共享数据的先决条件

* Azure 订阅：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 收件人的 Azure 登录电子邮件地址（使用其电子邮件别名将无效）。
* 如果源 Azure 数据存储位于与你将用于创建 Data Share 资源的 Azure 订阅不同的 Azure 订阅中，请在 Azure 数据存储所在的订阅中注册 [Microsoft.DataShare 资源提供程序](concepts-roles-permissions.md#resource-provider-registration)。 

### <a name="prerequisites-for-sql-source"></a>SQL 源的先决条件

* 使用 Azure SQL 数据库或 Azure Synapse Analytics (以前的 SQL 数据仓库，) 要共享的表和视图。
* 向 SQL 服务器上的数据库进行写入的权限，此权限存在于 *Microsoft.Sql/servers/databases/write* 中。 “参与者”角色有此权限。
* 用于访问数据仓库的数据共享权限。 可以通过以下步骤完成此操作： 
    1. 将你自己设置为 SQL Server 的 Azure Active Directory 管理员。
    1. 使用 Azure Active Directory 连接到 Azure SQL 数据库/数据仓库。
    1. 使用查询编辑器（预览版）执行以下脚本，以将 Data Share 资源托管标识添加为 db_datareader。 必须使用 Active Directory 而非 SQL Server 身份验证进行连接。 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       请注意， *<share_acc_name>* 是 Data Share 资源的名称。 如果尚未创建 Data Share 资源，则可以稍后返回到该先决条件。  

* 具有“db_datareader”访问权限的 Azure SQL 数据库用户，可以浏览和选择要共享的表和/或视图。 

* 客户端 IP SQL Server 防火墙访问权限。 可以通过以下步骤完成此操作： 
    1. 在 Azure 门户中的 SQL Server 中，导航到“防火墙和虚拟网络” 
    1. 单击“打开”切换按钮以允许访问 Azure 服务  。
    1. 单击“+ 添加客户端 IP”  ，然后单击“保存”  。 客户端 IP 地址可能会更改。 下次从 Azure 门户共享 SQL 数据时，可能需要重复此过程。 还可以添加 IP 范围。 

### <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

### <a name="create-a-data-share-account"></a>创建 Data Share 帐户

在 Azure 资源组中创建 Azure Data Share 资源。

1. 选择门户左上角的菜单按钮，然后选择“创建资源”(+)。

1. 搜索“Data Share”。 

1. 选择“Data Share”，然后选择“创建”  。

1. 根据以下信息填写 Azure Data Share 资源的基本详细信息。 

     **设置** | **建议的值** | **字段说明**
    |---|---|---|
    | 订阅 | 你的订阅 | 选择要用于 Data Share 帐户的 Azure 订阅。|
    | 资源组 | *test-resource-group* | 使用现有资源组或创建新资源组。 |
    | 位置 | *美国东部 2* | 选择 Data Share 帐户的区域。
    | 名称 | *datashareaccount* | 指定 Data Share 帐户的名称。 |
    | | |

1. 选择“审阅 + 创建”，然后选择“创建”来预配数据共享帐户 。 预配新的 Data Share 帐户通常需要大约 2 分钟或更少的时间。 

1. 部署完成后，选择“转到资源”。 

### <a name="create-a-share"></a>创建共享

1. 导航到 Data Share 概览页。

    ![共享数据](./media/share-receive-data.png "共享数据") 

1. 选择“开始共享数据”  。

1. 选择“创建”。   

1. 填写共享的详细信息。 指定名称、共享类型、共享内容说明以及使用条款（可选）。 

    ![EnterShareDetails](./media/enter-share-details.png "输入共享详细信息") 

1. 选择“继续”。

1. 若要向共享添加数据集，请选择“添加数据集”。 

    ![向共享添加数据集](./media/datasets.png "数据集")

1. 选择要添加的数据集类型。 你将会看到一个不同的数据集类型列表，具体取决于你在上一步中选择的共享类型（快照或就地）。 

    ![AddDatasets](./media/add-datasets.png "添加数据集")    

1. 选择您的 SQL server，提供凭据，然后选择 " **下一步** " 以导航到要共享的对象，然后选择 "添加数据集"。 

    ![SelectDatasets](./media/select-datasets-sql.png "选择数据集")    

1. 在“接收者”选项卡中，选择“+ 添加接收者”，输入数据使用者的电子邮件地址。 

    ![AddRecipients](./media/add-recipient.png "添加收件人") 

1. 选择“继续”。

1. 如果已选择了快照共享类型，则可以配置快照计划来向数据使用者提供数据更新。 

    ![EnableSnapshots](./media/enable-snapshots.png "启用快照") 

1. 选择开始时间和重复周期间隔。 

1. 选择“继续”。

1. 在“查看 + 创建”选项卡中查看包内容、设置、接收者和同步设置。 选择“创建”。

你的 Azure Data Share 现已创建，Data Share 的接收者现已准备好接受你的邀请。 

## <a name="receive-data"></a>接收数据

### <a name="prerequisites-to-receive-data"></a>接收数据的先决条件
在接受数据共享邀请之前，必须预配多个 Azure 资源，这些资源已在下面列出。 

确保在接受数据共享邀请之前已满足所有先决条件。 

* Azure 订阅：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* Data Share 邀请：Microsoft Azure 发出的邀请，主题名为“来自 **<yourdataprovider@domain.com>** 的 Azure Data Share 邀请”。
* 在你将在其中创建 Data Share 资源的 Azure 订阅以及目标 Azure 数据存储所在的 Azure 订阅中注册 [Microsoft.DataShare 资源提供程序](concepts-roles-permissions.md#resource-provider-registration)。

### <a name="prerequisites-for-target-storage-account"></a>目标存储帐户的先决条件
如果选择将数据接收到 Azure 存储，以下是先决条件列表。

* 一个 Azure 存储帐户：如果没有，可以创建一个 [Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)。 
* 向存储帐户进行写入的权限，此权限位于 *Microsoft.Storage/storageAccounts/write* 中。 “参与者”角色有此权限。 
* 向存储帐户添加角色分配的权限，此权限存在于 *Microsoft.Authorization/role assignments/write* 中。 “所有者”角色有此权限。  

### <a name="prerequisites-for-sql-target"></a>SQL 目标的先决条件
如果选择将数据接收到 Azure SQL Database，Azure Synapse Analytics 会列出必备组件。

* 向 SQL 服务器上的数据库进行写入的权限，此权限存在于 *Microsoft.Sql/servers/databases/write* 中。 “参与者”角色有此权限。 
* 用于访问 Azure SQL 数据库或 Azure Synapse Analytics 的数据共享资源托管标识的权限。 可以通过以下步骤完成此操作： 
    1. 将你自己设置为 SQL Server 的 Azure Active Directory 管理员。
    1. 使用 Azure Active Directory 连接到 Azure SQL 数据库/数据仓库。
    1. 使用查询编辑器（预览版）执行以下脚本，以将 Data Share 托管标识添加为“db_datareader, db_datawriter, db_ddladmin”。 必须使用 Active Directory 而非 SQL Server 身份验证进行连接。 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        请注意， *<share_acc_name>* 是 Data Share 资源的名称。 如果尚未创建 Data Share 资源，则可以稍后返回到该先决条件。         

* 客户端 IP SQL Server 防火墙访问权限。 可以通过以下步骤完成此操作： 
    1. 在 Azure 门户中的 SQL Server 中，导航到“防火墙和虚拟网络” 
    1. 单击“打开”切换按钮以允许访问 Azure 服务  。
    1. 单击“+ 添加客户端 IP”  ，然后单击“保存”  。 客户端 IP 地址可能会更改。 下次从 Azure 门户将数据接收到 SQL 目标中时，可能需要重复此过程。 还可以添加 IP 范围。 

### <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

### <a name="open-invitation"></a>打开邀请

1. 可以从电子邮件或直接从 Azure 门户打开邀请。 

   若要从电子邮件打开邀请，请检查收件箱中是否有来自数据提供程序的邀请。 邀请来自 Microsoft Azure，标题为“来自 <yourdataprovider@domain.com> 的 Azure Data Share 邀请”。  单击“查看邀请”，以在 Azure 中查看邀请。  

   若要直接从 Azure 门户打开邀请，请在 Azure 门户中搜索“数据共享邀请”  。 这会转到“数据共享邀请”列表。

   ![邀请列表](./media/invitations.png "邀请列表") 

1. 选择要查看的共享。 

### <a name="accept-invitation"></a>接受邀请
1. 确保查看所有字段，包括“使用条款”。  如果同意使用条款，则必须勾选表示同意的框。 

   ![使用条款](./media/terms-of-use.png "使用条款") 

1. 在“目标 Data Share 帐户”下选择要在其中部署 Data Share 的订阅和资源组。  

   对于“Data Share 帐户”字段，如果没有现有的 Data Share 帐户，请选择“新建”。   否则，请选择一个现有的 Data Share 帐户来接受数据共享。 

   对于“已接收共享的名称”字段，可以保留数据提供者指定的默认值，也可以为已接收共享指定新名称  。 

   同意使用条款并指定用于管理已接收共享的数据共享帐户以后，请选择“接受并配置”。 将创建共享订阅。 

   ![接受选项](./media/accept-options.png "接受选项") 

   这会转到数据共享帐户中的已接收共享。 

   如果不想接受邀请，请选择“拒绝”。** 

### <a name="configure-received-share"></a>配置已接收共享
按照以下步骤配置要接收数据的位置。

1. 选择“数据集”选项卡。选中要为其分配目标位置的数据集旁边的复选框。 选择“+ 映射到目标”以选择目标数据存储。 

   ![映射到目标](./media/dataset-map-target.png "映射到目标") 

1. 选择要将数据置于其中的目标数据存储。 目标数据存储中具有相同路径和名称的任何数据文件或表将被覆盖。 

   ![目标存储帐户](./media/dataset-map-target-sql.png "目标数据存储") 

1. 对于基于快照的共享，如果数据提供程序已创建定期更新数据的快照计划，你还可以通过选择“快照计划”选项卡来启用快照计划。选中快照计划旁边的框，然后选择“+ 启用”。

   ![启用快照计划](./media/enable-snapshot-schedule.png "启用快照计划")

### <a name="trigger-a-snapshot"></a>触发快照
这些步骤仅适用于基于快照的共享。

1. 可以通过依次选择“详细信息”选项卡、“触发快照”来触发快照 。 在这里，可以触发数据的完整或增量快照。 如果是首次从数据提供者处接收数据，请选择完整副本。 对于 SQL 源，仅支持完整快照。

   ![触发快照](./media/trigger-snapshot.png "触发快照") 

1. 如果上次运行状态为“成功”，请转到目标数据存储来查看接收的数据。** 选择“数据集”，然后单击“目标路径”中的链接。**** 

   ![使用者数据集](./media/consumer-datasets.png "使用者数据集映射") 

### <a name="view-history"></a>查看历史记录
此步骤仅适用于基于快照的共享。 若要查看快照历史记录，请选择“历史记录”选项卡。在这里可以找到包含过去 30 天生成的所有快照的历史记录。 

## <a name="next-steps"></a>后续步骤
已了解如何使用 Azure 数据共享服务从存储帐户共享和接收数据。 若要了解有关其他数据源的共享的详细信息，请继续阅读 [支持的数据存储](supported-data-stores.md)。

