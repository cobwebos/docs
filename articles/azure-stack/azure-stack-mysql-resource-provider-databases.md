---
title: 在 Azure Stack 上使用 MySQL Adapter RP 提供的数据库 | Microsoft Docs
description: 如何创建和管理使用 MySQL 适配器资源提供程序预配的 MySQL 数据库
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
ms.date: 06/26/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 77dca29b0c60726f0a072dd662aba0d12730502a
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413682"
---
# <a name="create-mysql-databases"></a>创建 MySQL 数据库

可以在用户门户中创建和管理自助服务数据库。 Azure Stack 用户需要一个包含套餐的订阅，该套餐应包含 MySQL 数据库服务。

## <a name="test-your-deployment-by-creating-a-mysql-database"></a>创建 MySQL 数据库以测试部署

1. 登录到 Azure Stack 用户门户。
2. 选择“+ 新建” > “数据 + 存储” > “MySQL 数据库” > “添加”。
3. 在“创建 MySQL 数据库”下输入“数据库名称”，并根据需要为环境配置其他设置。

    ![创建 MySQL 测试数据库](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. 在“创建数据库”下，选择“SKU”。 在“选择 MySQL SKU”下，为数据库选取 SKU。

    ![选择 MySQL SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-sku.png)

    >[!Note]
    >向 Azure Stack 添加宿主服务器时，系统会为它们分配 SKU。 将在 SKU 的宿主服务器池中创建数据库。

5. 在“登录名”下选择“配置所需的设置”。
6. 在“选择登录名”下，可以选择现有登录名，或者选择“+ 创建新登录名”，以便设置新登录名。  输入**数据库登录**名和**密码**，然后选择“确定”。

    ![新建数据库用户名](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >MySQL 5.7 中的数据库登录名长度不可超过 32 个字符。 在早期版本中不可超过 16 个字符。

7. 选择“创建”，完成数据库的设置。

部署数据库以后，请记下“概要”下的“连接字符串”。 可以将此字符串用于任何需要访问 MySQL 数据库的应用程序。

![获取 MySQL 数据库的连接字符串](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

## <a name="update-the-administrative-password"></a>更新管理密码

若要修改密码，可以在 MySQL 服务器实例上更改密码。

1. 选择“管理资源” > “MySQL 宿主服务器”。 选择宿主服务器。
2. 在“设置”下，选择“密码”。
3. 在“密码”下输入新密码，然后选择“保存”。

![更新管理密码](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>后续步骤

[更新 MySQL 资源提供程序](azure-stack-mysql-resource-provider-update.md)
