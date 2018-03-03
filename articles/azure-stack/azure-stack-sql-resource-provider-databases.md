---
title: "使用提供的 SQL 适配器 RP Azure 堆栈上的数据库 |Microsoft 文档"
description: "如何创建和管理 SQL 数据库使用 SQL 适配器资源提供程序设置"
services: azure-stack
documentationCenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 39f6cc30191f07a7c891446a9132222a6d264dc4
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="create-sql-databases"></a>创建 SQL 数据库

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

通过用户门户体验提供了自助服务数据库。 用户需要包含提议，其中包含的数据库服务的订阅。

1. 登录到[Azure 堆栈](azure-stack-poc.md)用户门户 （服务管理员还可以使用管理门户）。

2. 单击**+ 新建** &gt;**数据 + 存储"** &gt; **SQL Server 数据库 （预览版）** &gt; **添加**。

3. 填写表单的数据库的详细信息，包括**数据库名称**，**最大大小**，并根据需要更改其他参数。 系统会要求你选取你的数据库的 SKU。 当宿主服务器添加时，按指定 SKU。 数据库是在该池中创建的宿主构成 SKU 的服务器。

  ![新建数据库](./media/azure-stack-sql-rp-deploy/newsqldb.png)

  >[!NOTE]
  > 数据库大小必须至少为 64 MB。 可以增加使用设置。

4. 填写登录设置：**数据库登录名**，和**密码**。 这些设置是为你的访问的只在此数据库中创建的 SQL 身份验证凭据。 登录用户名称必须是全局唯一的。 创建新的登录设置，或选择一个现有。 你可以重复使用其他数据库使用相同 SKU 的登录设置。

    ![创建新的数据库登录名](./media/azure-stack-sql-rp-deploy/create-new-login.png)


5. 提交表单并等待部署完成。

    在生成边栏选项卡，请注意"连接字符串"字段。 可以在 Azure 堆栈中使用该字符串中的任何应用程序需要访问 SQL Server （例如，web 应用程序）。

    ![检索连接字符串](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="delete-sql-databases"></a>删除 SQL 数据库
在门户中，

>[!NOTE]
>
>当从 RP 删除 SQL AlwaysOn 数据库时，它将从主服务器和 AlwaysOn 可用性组已成功删除但设计 SQL 可用性组通过将数据库置于还原每个副本中的状态并不删除数据库，除非触发。 如果未删除数据库，辅助副本将转到 Not synchronizing 状态。 将新的数据库重新添加到可用性组具有相同通过 RP 仍然正常工作。 请参阅![中删除辅助数据库](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/remove-a-secondary-database-from-an-availability-group-sql-server)

## <a name="manage-database-credentials"></a>管理数据库凭据
你可以更新数据库凭据 （登录名设置）。

## <a name="verify-sql-alwayson-databases"></a>验证 SQL AlwaysOn 数据库
AlwaysOn 数据库应显示为已同步并且可在所有实例和可用性组中。 故障转移后，数据库应无缝连接。 SQL Server Management Studio 可用于验证数据库处于正在同步状态：

![验证 AlwaysOn](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)
