---
title: 使用提供的 MySQL 适配器 RP AzureStack 上的数据库 |Microsoft 文档
description: 使用 MySQL 适配器资源提供程序如何创建和管理 MySQL 数据库设置
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
ms.openlocfilehash: 0a900d75315fd0015633c036877faef84c48d65b
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031817"
---
# <a name="create-mysql-databases"></a>创建 MySQL 数据库

你可以创建和管理自助服务用户门户中的数据库。 Azure 堆栈用户需要通过提供，它包含 MySQL 数据库服务的订阅。

## <a name="test-your-deployment-by-creating-a-mysql-database"></a>通过创建 MySQL 数据库来测试你的部署

1. 登录到 Azure 堆栈用户门户。
2. 选择 **+ 新** > **数据 + 存储** > **MySQL 数据库** > **添加**。
3. 下**创建 MySQL 数据库**，输入数据库名称，然后根据需要为你的环境中配置其他设置。

    ![创建 MySQL 测试数据库](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. 下**Create Database**，选择**SKU**。 下**选择 MySQL SKU**，选取你的数据库的 SKU。

    ![选择一种 MySQL 的 SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

    >[!Note]
    >当宿主服务器添加到 Azure 堆栈时，按指定 SKU。 数据库创建的宿主 SKU 中的服务器池中。

5. 下**登录**，选择***配置所需设置***。
6. 下**选择一个登录名**，可以选择现有登录名，也可以选择 **+ 创建一个新的登录名**设置新的登录名。  输入**数据库登录名**名称和**密码**，然后选择**确定**。

    ![新建数据库用户名](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >数据库登录名的长度不能超过在 MySQL 5.7 32 个字符。 在早期版本中不可超过 16 个字符。

7. 选择**创建**来完成设置数据库。

部署数据库后，需要注意的**连接字符串**下**Essentials**。 在所有应用程序需要访问 MySQL 数据库，可以使用此字符串。

![获取 MySQL 数据库的连接字符串](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

## <a name="update-the-administrative-password"></a>更新管理密码

可以通过更改 MySQL server 实例上修改密码。

1. 选择“管理资源” > “MySQL 宿主服务器”。 选择宿主服务器。
2. 下**设置**，选择**密码**。
3. 下**密码**，输入新密码，然后选择**保存**。

![更新管理密码](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>后续步骤

[维护 MySQL 资源提供程序](azure-stack-mysql-resource-provider-maintain.md)