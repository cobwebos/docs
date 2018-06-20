---
title: 使用由 Azure 堆栈上的 SQL 适配器资源提供程序提供的数据库 |Microsoft 文档
description: 如何创建和管理 SQL 数据库使用 SQL 适配器资源提供程序的设置
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
ms.openlocfilehash: a82db16f2012672f6e2669f2fd8198b177f501f3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264176"
---
# <a name="create-sql-databases"></a>创建 SQL 数据库

你可以创建和管理自助服务用户门户中的数据库。 Azure 堆栈用户需要通过提供，它包含 SQL 数据库服务的订阅。

1. 登录到[Azure 堆栈](azure-stack-poc.md)用户门户。

2. 选择 **+ 新** &gt;**数据 + 存储"** &gt; **SQL Server 数据库** &gt; **添加**。

3. 下**Create Database**，输入所需的信息，如**数据库名称**和**以 mb 为单位的最大大小**。

   >[!NOTE]
   >数据库大小必须至少为 64 MB，这可以提高部署数据库后。

   根据需要为你的环境中配置其他设置。

4. 下**Create Database**，选择**SKU**。 下**选择一种 SKU**，选择你的数据库的 SKU。

   ![创建数据库](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >当宿主服务器添加到 Azure 堆栈时，按指定 SKU。 数据库创建的宿主 SKU 中的服务器池中。

5. 选择**登录**。
6. 下**选择一个登录名**，选择现有登录名，或选择 **+ 创建一个新的登录名**。
7. 下**新登录名**，输入的名称**数据库登录名**和**密码**。

   >[!NOTE]
   >这些设置是为你的访问的只在此数据库中创建的 SQL 身份验证凭据。 登录用户名必须全局唯一。 你可以重用使用相同 SKU 的其他数据库的登录设置。

   ![新建数据库用户名](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. 选择**确定**来完成部署数据库。

下**Essentials**，其显示在部署数据库之后，请记下的**连接字符串**。 在所有应用程序需要访问 SQL Server 数据库，可以使用此字符串。

![检索连接字符串](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>SQL Always On 数据库

按照设计，Always On 数据库处理以不同的方式比在独立服务器环境中。 有关详细信息，请参阅[简介 SQL Server Always On 可用性组在 Azure 虚拟机上的](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)。

### <a name="verify-sql-always-on-databases"></a>验证 SQL Always On 数据库

以下屏幕截图显示如何使用 SQL Server Management Studio 来查看数据库的状态在 SQL Always On。

![AlwaysOn 数据库状态](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

Always On 数据库应显示为同步并在所有 SQL 实例上可用，并且出现在可用性组。 在以前的屏幕截图中，数据库示例是 newdb1 并且其状态为**newdb1 （同步）**。

### <a name="delete-an-alwayson-database"></a>删除 AlwaysOn 数据库

当从资源提供程序删除 SQL AlwaysOn 数据库时，SQL 中从主副本和可用性组删除数据库。

然后，SQL 将数据库放入 Restoring 状态，与其他副本上，并不删除数据库，除非触发。 如果不删除数据库，辅助副本进入 Not Synchronizing 状态。

## <a name="next-steps"></a>后续步骤

[维护 SQL Server 资源提供程序](azure-stack-sql-resource-provider-maintain.md)
