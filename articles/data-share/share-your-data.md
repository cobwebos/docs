---
title: 教程：在组织外共享 - Azure Data Share
description: 教程 - 使用 Azure Data Share 与客户和合作伙伴共享数据
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 4ef9256404b0d0d4d6379e4f5a76c0d41a38c7cd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499333"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>教程：使用 Azure Data Share 共享数据  

本教程介绍如何设置新的 Azure Data Share，然后开始与 Azure 组织外部的客户和合作伙伴共享数据。 

本教程介绍以下操作：

> [!div class="checklist"]
> * 创建 Data Share。
> * 向 Data Share 添加数据集。
> * 为 Data Share 启用同步计划。 
> * 将接收人添加到 Data Share。 

## <a name="prerequisites"></a>先决条件

* Azure 订阅：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 收件人的 Azure 登录电子邮件地址（使用其电子邮件别名将无效）。

### <a name="share-from-a-storage-account"></a>从存储帐户共享：

* 一个 Azure 存储帐户：如果没有，可以创建一个 [Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* 向存储帐户添加角色分配的权限，该权限存在于 *Microsoft.Authorization/role assignments/write* 权限中。 所有者角色中存在此权限。 

### <a name="share-from-a-sql-based-source"></a>从基于 SQL 的源共享：

* 包含要共享的表和视图的 Azure SQL 数据库或 Azure SQL 数据仓库。
* 用于访问数据仓库的数据共享权限。 可以通过以下步骤完成此操作： 
    1. 将自己设置为服务器的 Azure Active Directory 管理员。
    1. 使用 Azure Active Directory 连接到 Azure SQL 数据库/数据仓库。
    1. 使用查询编辑器（预览版）执行以下脚本，以将数据共享 MSI 添加为 db_owner。 必须使用 Active Directory 而非 SQL Server 身份验证进行连接。 
    
```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```                   
请注意，<share_acc_name>  是数据共享帐户的名称。 如果尚未创建数据共享帐户，则可以稍后返回到该先决条件。  

* 客户端 IP SQL Server 防火墙访问：可以通过以下步骤完成此操作：1. 导航到“防火墙和虚拟网络”  1。 单击“启动”  切换以允许访问 Azure 服务。 

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-data-share-account"></a>创建 Data Share 帐户

在 Azure 资源组中创建 Azure Data Share 资源。

1. 选择门户左上角的“创建资源”按钮 (+)  。

1. 搜索“Data Share”。 

1. 选择“Data Share”，然后选择“创建”  。

1. 根据以下信息填写 Azure Data Share 资源的基本详细信息。 

     **设置** | **建议的值** | **字段说明**
    |---|---|---|
    | Name | *datashareacount* | 指定 Data Share 帐户的名称。 |
    | Subscription | 订阅 | 选择要用于 Data Share 帐户的 Azure 订阅。|
    | Resource group | *test-resource-group* | 使用现有资源组或创建新资源组。 |
    | 位置 | 美国东部 2  | 选择 Data Share 帐户的区域。
    | | |

1. 选择“创建”，预配 Data Share 帐户。  预配新的 Data Share 帐户通常需要大约 2 分钟或更少的时间。 

1. 部署完成后，选择“转到资源”。 

## <a name="create-a-data-share"></a>创建 Data Share

1. 导航到 Data Share 概览页。

    ![共享数据](./media/share-receive-data.png "共享数据") 

1. 选择“开始共享数据”  。

1. 选择“创建”  。   

1. 填充 Data Share 的详细信息。 指定名称、共享内容说明以及使用条款（可选）。 

    ![EnterShareDetails](./media/enter-share-details.png "输入共享详细信息") 

1. 选择“继续” 

1. 若要向 Data Share 添加数据集，请选择“添加数据集”。  

    ![数据集](./media/datasets.png "数据集")

1. 选择要添加的数据集类型。 

    ![AddDatasets](./media/add-datasets.png "添加数据集")    

1. 导航到要共享的对象，选择“添加数据集”。 

    ![SelectDatasets](./media/select-datasets.png "选择数据集")    

1. 在“接收者”选项卡中，选择“+ 添加接收者”，输入数据使用者的电子邮件地址。 

    ![AddRecipients](./media/add-recipient.png "添加收件人") 

1. 选择“继续” 

1. 如果希望数据使用者能够获得数据的增量更新，请启用快照计划。 

    ![EnableSnapshots](./media/enable-snapshots.png "启用快照") 

1. 选择开始时间和重复周期间隔。 

1. 选择“继续” 

1. 在“查看 + 创建”选项卡中查看包内容、设置、接收者和同步设置。 选择“创建” 

你的 Azure Data Share 现已创建，Data Share 的接收者现已准备好接受你的邀请。 

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建 Azure Data Share 并邀请接收者。 若要了解数据使用者如何接受和接收数据共享，请继续学习[接受和接收数据](subscribe-to-data-share.md)教程。 
