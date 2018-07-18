---
title: 使用提供的 Azure Stack 上的 SQL 适配器资源提供程序数据库 |Microsoft Docs
description: 如何创建和管理 SQL 数据库使用 SQL 适配器资源提供程序设置
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 2f286c48822956c82f99808092c26f6637be5cb1
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968820"
---
# <a name="create-sql-databases"></a>创建 SQL 数据库

您可以创建和管理自助服务用户门户中的数据库。 Azure Stack 用户需要具有包含 SQL 数据库服务的产品/服务的订阅。

1. 登录到[Azure Stack](azure-stack-poc.md)用户门户。

2. 选择 **+ 新建** &gt;**数据 + 存储** &gt; **SQL Server 数据库** &gt; **添加**。

3. 下**Create Database**，输入所需的信息，如**数据库名称**并**最大大小 （mb）**。

   >[!NOTE]
   >数据库大小必须至少 64 MB，这可以提高部署数据库后。

   根据需要为您的环境中配置其他设置。

4. 下**Create Database**，选择**SKU**。 下**选择 SKU**，选择你的数据库的 SKU。

   ![创建数据库](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >宿主服务器添加到 Azure Stack，它们分配 SKU。 托管 SKU 中的服务器的池中创建数据库。

5. 选择**登录名**。
6. 下**选择一个登录名**中，选择现有登录名，或选择 **+ 创建新的登录名**。
7. 下**新的登录名**，输入的名称**数据库登录名**和一个**密码**。

   >[!NOTE]
   >这些设置是为您对此数据库只能访问权限创建的 SQL 身份验证凭据。 登录用户名必须全局唯一。 可以重复使用其他使用相同的 SKU 的数据库的登录名的设置。

   ![新建数据库用户名](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. 选择**确定**完成将数据库部署。

下**Essentials**，所显示部署数据库后，记下**连接字符串**。 任何应用程序需要访问 SQL Server 数据库中，可以使用此字符串。

![检索连接字符串](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>SQL Always On 数据库

根据设计，Always On 数据库被处理方式不同于独立服务器环境。 有关详细信息，请参阅[引入了 SQL Server Always On 可用性组在 Azure 虚拟机上的](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)。

### <a name="verify-sql-always-on-databases"></a>验证 SQL Always On 数据库

以下屏幕截图显示了如何使用 SQL Server Management Studio 查看在 SQL Always On 的数据库状态。

![AlwaysOn 数据库状态](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

Always On 数据库应显示为 Synchronized 且适用于所有 SQL 实例，并出现在可用性组。 在上一屏幕截图中，数据库示例是 newdb1 且其状态为**newdb1 （同步）**。

### <a name="delete-an-alwayson-database"></a>删除了 AlwaysOn 的数据库

当从资源提供程序中删除 SQL AlwaysOn 数据库时，SQL 从主副本和可用性组删除数据库。

然后，SQL 将数据库置于 Restoring 状态放在其他副本上，并不会删除该数据库，除非触发。 如果不删除数据库，辅助副本进入未同步状态。

## <a name="next-steps"></a>后续步骤

[维护 SQL Server 资源提供程序](azure-stack-sql-resource-provider-maintain.md)
