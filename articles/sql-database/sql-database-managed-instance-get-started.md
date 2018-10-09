---
title: Azure 门户：创建 SQL 托管实例 | Microsoft Docs
description: 创建用于访问的 SQL 托管实例、网络环境和客户端 VM。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: Carlrab
manager: craigg
ms.date: 09/23/2018
ms.openlocfilehash: c0c249ffe426e86049024122d9cbf786bb677220
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2018
ms.locfileid: "47160632"
---
# <a name="create-an-azure-sql-database-managed-instance"></a>创建 Azure SQL 数据库托管实例

本快速入门分步介绍了如何在 Azure 门户中创建 Azure SQL 数据库[托管实例](sql-database-managed-instance.md)。 

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-managed-instance"></a>创建托管实例

下列步骤展示了如何创建托管实例。

1. 在 Azure 门户的左上角单击“创建资源”。
2. 找到“托管实例”，然后选择“Azure SQL 托管实例”。
3. 单击“创建”。

   ![创建托管实例](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. 根据下表中的说明，在托管实例窗体中填充请求的信息：

   | 设置| 建议的值 | Description |
   | ------ | --------------- | ----------- |
   | **订阅** | 订阅 | 你有权在其中创建新资源的订阅 |
   |**托管实例名称**|任何有效的名称|请参阅[命名规则和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)，了解什么是有效的名称。|
   |**托管实例管理员登录名**|任何有效的用户名|请参阅[命名规则和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)，了解什么是有效的名称。 不要使用“serveradmin”，因为这是保留的服务器级角色。| 
   |**密码**|任何有效的密码|密码必须至少 16 个字符，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)。|
   |**资源组**|新的或现有的资源组|如需有效的资源组名称，请参阅 [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)（命名规则和限制）。|
   |**位置**|你要在其中创建托管实例的位置|有关区域的信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。|
   |**虚拟网络**|选择“创建新的虚拟网络”或选择在你之前在此窗体中提供的资源组中以前创建的一个虚拟网络。| 若要使用自定义设置为托管实例配置虚拟网络，请参阅 Github 中的[配置 SQL 托管实例虚拟网络环境模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment)。 有关为托管实例配置网络环境的要求的信息，请参阅[为 Azure SQL 数据库托管实例配置 VNet](sql-database-managed-instance-vnet-configuration.md) |

   ![托管实例名称窗体](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. 单击“定价层”，设置计算和存储资源的大小并查看定价层选项。 默认值是具有 32 GB 内存和 16 个 vCore 的“常规用途”定价层。
6. 使用滑块或文本框指定存储量和虚拟核心数。 
7. 完成后单击“应用”，保存选择的内容。  
8. 单击“创建”，部署托管实例。
9. 单击“通知”图标，查看部署的状态。

    ![托管实例部署进度](./media/sql-database-managed-instance-get-started/deployment-progress.png)

10. 单击“正在进行的部署”以打开“托管实例”窗口，进一步监视部署进度。 

> [!IMPORTANT]
> 子网中第一个实例的部署时间通常远超后续实例的部署时间。 请勿因持续时间超出预期而取消部署操作。 在子网中创建第二个托管实例仅需要花费数分钟时间。

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>查看资源并检索你的完全限定的服务器名称

在部署成功完成后，查看创建的资源，并检索完全限定的服务器名称以在后续快速入门中使用。

1. 打开你的托管实例的资源组，并查看在[创建托管实例](sql-database-managed-instance-get-started.md)快速入门中创建的其资源。

   ![托管实例资源](./media/sql-database-managed-instance-get-started/resources.png)在 Azure 门户中打开你的托管实例资源。

2. 单击你的托管实例。
3. 在“概述”选项卡上，找到“主机”属性并复制托管实例的完全限定主机地址。


   ![托管实例资源](./media/sql-database-managed-instance-get-started/host-name.png)

   名称类似于以下示例：**your_machine_name.neu15011648751ff.database.windows.net**。

## <a name="next-steps"></a>后续步骤

- 若要了解如何连接到托管实例，请参阅：
  - 如需应用程序的连接选项的概述，请参阅[将应用程序连接到托管实例](sql-database-managed-instance-connect-app.md)。
  - 有关展示了如何从 Azure 虚拟机连接到托管实例的快速入门，请参阅[配置 Azure 虚拟机连接](sql-database-managed-instance-configure-vm.md)。
  - 如需介绍如何使用点到站点连接从本地客户端计算机连接到托管实例的快速入门，请参阅[配置点到站点连接](sql-database-managed-instance-configure-p2s.md)。
- 若要将现有 SQL 服务器数据库从本地还原到托管实例，可以使用[用于迁移的 Azure 数据库迁移服务 (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) 从数据库备份文件还原，或使用 [T-SQL RESTORE 命令](sql-database-managed-instance-get-started-restore.md)从数据库备份文件还原。
