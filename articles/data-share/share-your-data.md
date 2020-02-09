---
title: 教程：在组织外共享 - Azure Data Share
description: 教程 - 使用 Azure Data Share 与客户和合作伙伴共享数据
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 64c5d80b5a2660164b21e71f06e847d5b11e40da
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964409"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>教程：使用 Azure Data Share 共享数据  

本教程介绍如何设置新的 Azure Data Share，然后开始与 Azure 组织外部的客户和合作伙伴共享数据。 

本教程介绍以下操作：

> [!div class="checklist"]
> * 创建 Data Share。
> * 向 Data Share 添加数据集。
> * 为 Data Share 启用快照计划。 
> * 将接收人添加到 Data Share。 

## <a name="prerequisites"></a>必备条件

* Azure 订阅：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 收件人的 Azure 登录电子邮件地址（使用其电子邮件别名将无效）。

### <a name="share-from-a-storage-account"></a>从存储帐户共享：

* 一个 Azure 存储帐户：如果没有，可以创建一个 [Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* 向存储帐户进行写入的权限，此权限位于 *Microsoft.Storage/storageAccounts/write* 中。 “参与者”角色有此权限。
* 向存储帐户添加角色分配的权限，此权限存在于 *Microsoft.Authorization/role assignments/write* 中。 “所有者”角色有此权限。 


### <a name="share-from-a-sql-based-source"></a>从基于 SQL 的源共享：

* 包含要共享的表和视图的 Azure SQL 数据库或 Azure Synapse Analytics（以前的 Azure SQL 数据仓库）。
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
    1. 单击“+ 添加客户端 IP”  ，然后单击“保存”  。 客户端 IP 地址可能会更改。 你还可以添加 IP 范围。 

### <a name="share-from-azure-data-explorer"></a>从 Azure 数据资源管理器进行共享
* Azure 数据资源管理器群集，其中包含要共享的数据库。
* 向 Azure 数据资源管理器群集进行写入的权限，此权限存在于 *Microsoft.Kusto/clusters/write* 中。 “参与者”角色有此权限。
* 向 Azure 数据资源管理器群集添加角色分配的权限，此权限存在于 *Microsoft.Authorization/role assignments/write* 中。 “所有者”角色有此权限。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-data-share-account"></a>创建 Data Share 帐户

在 Azure 资源组中创建 Azure Data Share 资源。

1. 选择门户左上角的“创建资源”按钮 (+)  。

1. 搜索“Data Share”。 

1. 选择“Data Share”，然后选择“创建”  。

1. 根据以下信息填写 Azure Data Share 资源的基本详细信息。 

     **设置** | **建议的值** | **字段说明**
    |---|---|---|
    | 名称 | *datashareacount* | 指定 Data Share 帐户的名称。 |
    | 订阅 | 订阅 | 选择要用于 Data Share 帐户的 Azure 订阅。|
    | 资源组 | *test-resource-group* | 使用现有资源组或创建新资源组。 |
    | 位置 | 美国东部 2  | 选择 Data Share 帐户的区域。
    | | |

1. 选择“创建”，预配 Data Share 帐户。  预配新的 Data Share 帐户通常需要大约 2 分钟或更少的时间。 

1. 部署完成后，选择“转到资源”。 

## <a name="create-a-data-share"></a>创建 Data Share

1. 导航到 Data Share 概览页。

    ![共享数据](./media/share-receive-data.png "共享数据") 

1. 选择“开始共享数据”  。

1. 选择“创建”  。   

1. 填充 Data Share 的详细信息。 指定名称、共享类型、共享内容说明以及使用条款（可选）。 

    ![EnterShareDetails](./media/enter-share-details.png "输入共享详细信息") 

1. 选择“继续” 

1. 若要向 Data Share 添加数据集，请选择“添加数据集”。  

    ![数据集](./media/datasets.png "数据集")

1. 选择要添加的数据集类型。 你将会看到一个不同的数据集类型列表，具体取决于你在上一步中选择的共享类型（快照或就地）。 如果从 Azure SQL 数据库或 Azure SQL 数据仓库进行共享，系统将提示你输入一些 SQL 凭据。 使用你在先决条件部分中创建的用户进行身份验证。

    ![AddDatasets](./media/add-datasets.png "添加数据集")    

1. 导航到要共享的对象，选择“添加数据集”。 

    ![SelectDatasets](./media/select-datasets.png "选择数据集")    

1. 在“接收者”选项卡中，选择“+ 添加接收者”，输入数据使用者的电子邮件地址。 

    ![AddRecipients](./media/add-recipient.png "添加收件人") 

1. 选择“继续” 

1. 如果已选择了快照共享类型，则可以配置快照计划来向数据使用者提供数据更新。 

    ![EnableSnapshots](./media/enable-snapshots.png "启用快照") 

1. 选择开始时间和重复周期间隔。 

1. 选择“继续” 

1. 在“查看 + 创建”选项卡中查看包内容、设置、接收者和同步设置。 选择“创建” 

你的 Azure Data Share 现已创建，Data Share 的接收者现已准备好接受你的邀请。 

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建 Azure Data Share 并邀请接收者。 若要了解数据使用者如何接受和接收数据共享，请继续学习[接受和接收数据](subscribe-to-data-share.md)教程。 
