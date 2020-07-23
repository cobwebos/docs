---
title: 教程：接受和接收数据 - Azure Data Share
description: 教程 - 使用 Azure Data Share 接受和接收数据
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 4dff48f909cd3febbbb7e92dcf96070020b8f57c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82145137"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>教程：使用 Azure Data Share 接受和接收数据  

本教程介绍如何使用 Azure Data Share 接受数据共享邀请。 另外还介绍如何接收共享给你的数据，以及如何启用定期刷新时间间隔，确保共享给你的数据快照始终是最新的。 

> [!div class="checklist"]
> * 如何接受 Azure Data Share 邀请
> * 创建 Azure Data Share 帐户
> * 指定数据目标
> * 创建按计划刷新的数据共享的订阅

## <a name="prerequisites"></a>先决条件
在接受数据共享邀请之前，必须预配多个 Azure 资源，这些资源已在下面列出。 

确保在接受数据共享邀请之前已满足所有先决条件。 

* Azure 订阅：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* Data Share 邀请：Microsoft Azure 发出的邀请，主题名为“来自 **<yourdataprovider@domain.com>** 的 Azure Data Share 邀请”。
* 在你将在其中创建 Data Share 资源的 Azure 订阅以及目标 Azure 数据存储所在的 Azure 订阅中注册 [Microsoft.DataShare 资源提供程序](concepts-roles-permissions.md#resource-provider-registration)。

### <a name="receive-data-into-a-storage-account"></a>将数据接收到存储帐户 

* 一个 Azure 存储帐户：如果没有，可以创建一个 [Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)。 
* 向存储帐户进行写入的权限，此权限位于 *Microsoft.Storage/storageAccounts/write* 中。 “参与者”角色有此权限。 
* 向存储帐户添加角色分配的权限，此权限存在于 *Microsoft.Authorization/role assignments/write* 中。 “所有者”角色有此权限。  

### <a name="receive-data-into-a-sql-based-source"></a>将数据接收到基于 SQL 的源中：

* 向 SQL 服务器上的数据库进行写入的权限，此权限存在于 *Microsoft.Sql/servers/databases/write* 中。 “参与者”角色有此权限。 
* 数据共享资源托管标识用于访问 Azure SQL 数据库或 Azure SQL 数据仓库的权限。 可以通过以下步骤完成此操作： 
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


### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>将数据接收到 Azure 数据资源管理器群集中： 

* 数据提供程序的数据资源管理器群集所在的同一 Azure 数据中心内的 Azure 数据资源管理器群集：如果没有此群集，可以创建一个 [Azure 数据资源管理器群集](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal)。 如果你不知道数据提供程序群集的 Azure 数据中心，可以稍后在此过程中创建群集。
* 向 Azure 数据资源管理器群集进行写入的权限，此权限存在于 *Microsoft.Kusto/clusters/write* 中。 “参与者”角色有此权限。 
* 向 Azure 数据资源管理器群集添加角色分配的权限，此权限存在于 *Microsoft.Authorization/role assignments/write* 中。 “所有者”角色有此权限。 

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

## <a name="open-invitation"></a>打开邀请

1. 可以从电子邮件或直接从 Azure 门户打开邀请。 

   若要从电子邮件打开邀请，请检查收件箱中是否有来自数据提供程序的邀请。 邀请来自 Microsoft Azure，标题为“来自 <yourdataprovider@domain.com> 的 Azure Data Share 邀请”。  单击“查看邀请”，以在 Azure 中查看邀请。  

   若要直接从 Azure 门户打开邀请，请在 Azure 门户中搜索“数据共享邀请”  。 这会转到“数据共享邀请”列表。

   ![邀请](./media/invitations.png "邀请列表") 

1. 选择要查看的共享。 

## <a name="accept-invitation"></a>接受邀请
1. 确保查看所有字段，包括“使用条款”。  如果同意使用条款，则必须勾选表示同意的框。 

   ![使用条款](./media/terms-of-use.png "使用条款") 

1. 在“目标 Data Share 帐户”下选择要在其中部署 Data Share 的订阅和资源组。  

   对于“Data Share 帐户”字段，如果没有现有的 Data Share 帐户，请选择“新建”。   否则，请选择一个现有的 Data Share 帐户来接受数据共享。 

   对于“已接收共享的名称”字段，可以保留数据提供者指定的默认值，也可以为已接收共享指定新名称  。 

   ![目标数据共享帐户](./media/target-data-share.png "目标数据共享帐户") 

1. 同意使用条款并指定共享位置以后，请选择“接受并配置”  。 将创建共享订阅。

   对于基于快照的共享，下一个屏幕会要求你选择要将数据复制到的目标存储帐户。 

   ![接受选项](./media/accept-options.png "接受选项") 

   如果你希望现在就接受邀请但在以后再配置目标数据存储，请选择“接受并稍后配置”  。 若要在以后继续配置存储，请参阅[配置数据集映射](how-to-configure-mapping.md)页，其中的详细步骤说明了如何继续进行数据共享配置。 

   对于就地共享，请参阅[配置数据集映射](how-to-configure-mapping.md)页，其中的详细步骤说明了如何继续进行数据共享配置。 

   如果不想接受邀请，请选择“拒绝”。  

## <a name="configure-storage"></a>配置存储
1. 在“目标存储设置”下，  选择“订阅”、“资源组”以及用于接收数据的存储帐户。 

   ![目标存储设置](./media/target-storage-settings.png "目标存储") 

1. 若要接收定期更新的数据，请确保启用快照设置。 请注意，如果数据提供者已在数据共享中包括了快照设置计划，你只会看到一个这样的计划。 

   ![快照设置](./media/snapshot-settings.png "快照设置") 

1. 选择“保存”。  

> [!IMPORTANT]
> 如果你正在接收基于 SQL 的数据，并想要将该数据接收到基于 SQL 的源中，请访问[配置数据集映射](how-to-configure-mapping.md)操作指南，以了解如何将 SQL Server 配置为数据集的目标。 

## <a name="trigger-a-snapshot"></a>触发快照
这些步骤仅适用于基于快照的共享。

1. 可以在“已接收共享”->“详细信息”选项卡中触发快照，只需选择“触发快照”即可。  在这里，可以触发数据的完整或增量快照。 如果是首次从数据提供者处接收数据，请选择完整副本。 

   ![触发快照](./media/trigger-snapshot.png "触发快照") 

1. 如果上次运行状态为“成功”，请转到目标数据存储来查看接收的数据。  选择“数据集”，然后单击“目标路径”中的链接。  

   ![使用者数据集](./media/consumer-datasets.png "使用者数据集映射") 

## <a name="view-history"></a>查看历史记录
若要查看快照历史记录，请导航到“已接收共享”->“历史记录”。 在这里可以找到一个历史记录，其中包含过去 60 天生成的的所有快照。 

## <a name="next-steps"></a>后续步骤
本教程介绍了如何接受和接收 Azure Data Share。 若要详细了解 Azure Data Share 概念，请继续阅读[概念：Azure Data Share 术语](terminology.md)。