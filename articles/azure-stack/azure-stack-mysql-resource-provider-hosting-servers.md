---
title: Azure Stack 上的 MySQL 宿主服务器 | Microsoft Docs
description: 如何添加 MySQL 实例以通过 MySQL 适配器资源提供程序进行预配
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
ms.date: 07/02/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: bccc2dcad8e326cd29cfe031a95a7c2d0cf5ec7f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38302306"
---
# <a name="add-hosting-servers-for-the-mysql-resource-provider"></a>为 MySQL 资源提供程序添加托管服务器

可以在中托管的虚拟机 (VM) 上的 MySQL 实例[Azure Stack](azure-stack-poc.md)，或只要 MySQL 资源提供程序可以连接到的实例在 Azure Stack 环境外部的 VM 上。

MySQL 版本 5.6 和 5.7 8.0 可供使用的宿主服务器。 MySQL RP 不支持 caching_sha2_password 身份验证;下一版本中添加的文件。 MySQL 8.0 服务器必须配置为使用 mysql_native_password。 此外支持 MariaDB。

## <a name="connect-to-a-mysql-hosting-server"></a>连接到 MySQL 宿主服务器

确保已准备好拥有管理特权的帐户的凭据。 若要添加宿主服务器，请按照下列步骤：

1. 登录到 Azure Stack 运营商门户为服务管理员。
2. 选择“更多服务”。
3. 选择“管理资源” > “MySQL 宿主服务器” > “+添加”。 这将打开**添加 MySQL 宿主服务器**对话框中，以下屏幕截图中所示。

   ![配置宿主服务器](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. 提供 MySQL 服务器实例的连接详细信息。

   * 有关**MySQL 宿主服务器名称**，提供完全限定的域名 (FQDN) 或有效的 IPv4 地址。 不要使用简短的 VM 名称。
   * 未提供默认 MySQL 实例，因此您必须指定**大小的宿主服务器以 gb 为单位**。 输入大小接近数据库服务器的容量。
   * 保留默认设置，对于**订阅**。
   * 有关**资源组**，创建一个新的活动，或使用现有组。

   > [!NOTE]
   > 如果租户和管理 Azure 资源管理器可以访问 MySQL 实例，则可以将其控制的资源提供程序。 但是，MySQL 实例**必须**到资源提供程序以独占方式分配。

5. 选择**Sku**以打开**创建 SKU**对话框。

   ![创建 MySQL SKU](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   SKU**名称**应反映的 SKU 属性，以便用户可以将其数据库部署到适当的 SKU。

6. 选择**确定**创建 SKU。
> [!NOTE]
> SKU 最长可能需要在一小时后才显示在门户中。 在部署并运行 SKU 之前，无法创建数据库。

7. 下**添加 MySQL 宿主服务器**，选择**创建**。

添加服务器时，将它们分配到新的或现有的 SKU 以区分服务产品。 例如，您可以提供更高的数据库和自动备份的 MySQL enterprise 实例。 你可以在组织中保留不同的部门此高性能服务器。

## <a name="security-considerations-for-mysql"></a>有关 MySQL 的安全注意事项

以下信息适用于 RP 和 MySQL 宿主服务器：

* 请确保所有宿主服务器都配置为使用 TLS 1.2 的通信。 请参阅[配置为使用加密的连接的 MySQL](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html)。
* 采用[透明数据加密](https://dev.mysql.com/doc/mysql-secure-deployment-guide/5.7/en/secure-deployment-data-encryption.html)。
* MySQL RP 不支持 caching_sha2_password 身份验证。

## <a name="increase-backend-database-capacity"></a>增加后端数据库容量

可以通过部署 Azure Stack 门户中的更多 MySQL 服务器增加后端数据库容量。 将这些服务器添加到新的或现有的 SKU。 如果将服务器添加到现有 SKU，请确保服务器特征的在 SKU 中的其他服务器相同。

## <a name="make-mysql-database-servers-available-to-your-users"></a>向用户提供 MySQL 数据库服务器

创建计划和产品/服务以向用户提供 MySQL 数据库服务器。 向计划添加 Microsoft.MySqlAdapter 服务并创建新的配额。 MySQL 不允许的数据库大小限制。

## <a name="next-steps"></a>后续步骤

[创建 MySQL 数据库](azure-stack-mysql-resource-provider-databases.md)