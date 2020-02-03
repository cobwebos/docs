---
title: 排查 Azure Data Share 问题
description: 了解如何在通过 Azure 数据共享创建或接收数据共享时解决与邀请和错误有关的问题。
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 901f2b56bc045dc9a9837dd18b2e6ce7169aa3b9
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964220"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>排查 Azure 数据共享中的常见问题 

本文介绍如何解决 Azure 数据共享的常见问题。 

## <a name="azure-data-share-invitations"></a>Azure Data Share 邀请 

在某些情况下，当新用户单击他人发送的电子邮件邀请中的“接受邀请”时，会出现一个空的邀请列表。 

![无邀请](media/no-invites.png)

这可能是由于以下原因造成的：

* **Azure 数据共享服务未在 Azure 租户中注册为任何 Azure 订阅的资源提供程序。** 如果 Azure 租户中没有数据共享资源，则会遇到此问题。 当你创建 Azure 数据共享资源时，它会自动在你的 Azure 订阅中注册资源提供程序。 还可以按照以下步骤手动注册数据共享服务。 需要具有 Azure 参与者角色才能完成这些步骤。

    1. 在 Azure 门户中，导航到“订阅”
    1. 选择要用于创建 Azure 数据共享资源的订阅
    1. 单击“资源提供程序”
    1. 搜索**DataShare**
    1. 单击“注册” 

    需要拥有 [Azure 参与者 RBAC 角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor)才能完成这些步骤。 

* **邀请发送到你的电子邮件别名，而不是 Azure 登录电子邮件。** 如果已注册 Azure 数据共享服务或已在 Azure 租户中创建了数据共享资源，但仍无法查看邀请，原因可能是提供商已将你的电子邮件别名输入为收件人，而不是 Azure 登录电子邮件地址。 联系你的数据提供商，确保他们已将邀请发送到你的 Azure 登录电子邮件地址，而不是电子邮件别名。

* **已接受邀请。** 电子邮件中的链接将你转到 Azure 门户中的数据共享邀请页面，此页面仅列出待定邀请。 如果已接受邀请，则它将不再显示在 "数据共享邀请" 页中。 转到你用于接受邀请的数据共享资源，以便查看收到的共享并配置目标 Azure 数据资源管理器群集设置。

## <a name="error-when-creating-or-receiving-a-new-share"></a>创建或接收新共享时出错

"未能添加数据集"

"未能映射数据集"

"无法授予数据共享资源 x 对 y 的访问权限"

"您对 x 没有适当的权限

"我们无法向一个或多个所选资源添加 Azure 数据共享帐户的写入权限"

如果在创建新的共享或映射数据集时收到上述任何错误，可能是由于 Azure 数据存储的权限不足。 请参阅所需权限的[角色和要求](concepts-roles-permissions.md)。 

需要具有写入权限，才能共享或接收 Azure 数据存储中的数据，这些数据通常位于参与者角色中。 

如果这是你第一次在 Azure 数据存储中共享或接收数据，则还需要*Microsoft。授权/角色分配/写入*权限，后者通常存在于所有者角色中。 即使您创建了 Azure 数据存储资源，也不会自动使您成为该资源的所有者。 使用适当的权限时，Azure 数据共享服务会自动向数据共享资源的托管标识授予对数据存储区的访问权限。 此过程可能需要几分钟才能生效。 如果遇到此延迟，请在几分钟后重试。

基于 SQL 的共享需要其他权限。 有关详细信息，请参阅基于 SQL 的共享疑难解答。

## <a name="troubleshooting-sql-based-sharing"></a>基于 SQL 的共享疑难解答

"用户 x 在 SQL 数据库中不存在"

如果从基于 SQL 的源添加数据集时收到此错误，原因可能是未在 SQL Server 上为 Azure 数据共享托管标识创建用户。  若要解决此问题，请运行以下脚本：

```sql
    create user "<share_acct_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acct_name>";
```      
如果在将数据集映射到基于 SQL 的目标时收到此错误，可能是因为你未在 SQL Server 上创建 Azure 数据共享托管标识的用户。  若要解决此问题，请运行以下脚本：

```sql
    create user "<share_acc_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acc_name>"; 
    exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
    exec sp_addrolemember db_ddladmin, "<share_acc_name>";
```
请注意， *< share_acc_name >* 是数据共享资源的名称。      

确保已遵循[共享数据](share-your-data.md)和[接受和接收数据](subscribe-to-data-share.md)教程中列出的所有先决条件。

## <a name="snapshot-failed"></a>快照失败
快照可能因多种原因而失败。 单击快照的开始时间，然后单击每个数据集的状态，即可找到详细的错误消息。 

如果错误消息与权限相关，请验证数据共享服务是否具有所需的权限。 有关详细信息，请参阅[角色和要求](concepts-roles-permissions.md)。 如果这是你第一次拍摄快照，可能需要几分钟时间才能向数据共享资源授予对 Azure 数据存储的访问权限。 请等待几分钟，然后重试。

## <a name="next-steps"></a>后续步骤

若要了解如何开始共享数据，请继续阅读[共享数据](share-your-data.md)教程。 

若要了解如何接收数据，请继续阅读[接受和接收数据](subscribe-to-data-share.md)教程。

