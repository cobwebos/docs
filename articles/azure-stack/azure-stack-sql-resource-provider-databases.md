---
title: 在 Azure Stack 上使用 SQL Adapter RP 提供的数据库 | Microsoft Docs
description: 如何创建和管理使用 SQL 适配器资源提供程序预配的 SQL 数据库
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
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: b9f92b4d85e17bc848d82be413df1d0dad7c8548
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294932"
---
# <a name="create-sql-databases"></a>创建 SQL 数据库
通过用户门户提供了自助服务数据库。 Azure 堆栈用户需要包含提议，其中包含 SQL 数据库服务的订阅。

1. 登录到 [Azure Stack](azure-stack-poc.md) 用户门户（服务管理员还可以使用管理门户）。

2. 单击“+ 新建”&gt;“数据 + 存储”&gt;“SQL Server 数据库”&gt;“添加”。

3. 在表单中填写数据库详细信息，包括**数据库名称**、**最大大小**，并根据需要更改其他参数。 系统会要求你为数据库选取 SKU。 添加宿主服务器时，将为它们分配 SKU。 将在构成 SKU 的宿主服务器池中创建数据库。

  ![新建数据库](./media/azure-stack-sql-rp-deploy/newsqldb.png)

  >[!NOTE]
  > 数据库大小必须至少为 64 MB。 可以使用设置增加该大小。

4. 填写登录设置：**数据库登录名**和**密码**。 这些设置是仅为访问此数据库创建的 SQL 身份验证凭据。 登录用户名必须全局唯一。 创建新的登录设置，或选择现有登录设置。 可以对使用同一 SKU 的其他数据库重用登录设置。

    ![新建数据库用户名](./media/azure-stack-sql-rp-deploy/create-new-login.png)


5. 提交表单并等待部署完成。

    在显示的边栏选项卡中，请注意“连接字符串”字段。 可以在 Azure Stack 中需要访问 SQL Server 的任何应用程序（例如，Web 应用）中使用该字符串。

    ![检索连接字符串](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="delete-sql-alwayson-databases"></a>删除 SQL AlwaysOn 数据库
从资源提供程序中删除 SQL AlwaysOn 数据库时，会成功从主服务器和 AlwaysOn 可用性组中删除该数据库，但根据设计，SQL AG 会将每个副本中的数据库置于还原中状态，并且不会删除数据库（除非触发删除操作）。 如果未删除数据库，次要副本将转为“未进行同步”状态。 通过 RP 以相同方式重新将新数据库添加到 AG 仍能正常工作。

## <a name="verify-sql-alwayson-databases"></a>验证 SQL AlwaysOn 数据库
AlwaysOn 数据库应显示为已进行同步，并且在所有实例和可用性组中可用。 故障转移后，数据库应该会无缝连接。 可以使用 SQL Server Management Studio 验证数据库是否正在进行同步：

![验证 AlwaysOn](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)


## <a name="next-steps"></a>后续步骤

[维护 SQL Server 资源提供程序](azure-stack-sql-resource-provider-maintain.md)
