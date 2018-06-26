---
title: MySQL 宿主服务器上 Azure 堆栈 |Microsoft 文档
description: 如何添加用于设置通过 MySQL 适配器资源提供程序的 MySQL 实例
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
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 5522eb1b8b0398aeb6f1b0dd8578b906880b4e89
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938547"
---
# <a name="add-hosting-servers-for-the-mysql-resource-provider"></a>将宿主服务器添加 MySQL 资源提供程序

你可以在中托管虚拟机 (VM) 上的 MySQL 实例[Azure 堆栈](azure-stack-poc.md)，或外部你 Azure 堆栈的环境，只要 MySQL 资源提供程序可以连接到的实例的 VM 上。

## <a name="connect-to-a-mysql-hosting-server"></a>连接到 MySQL 宿主服务器

确保已准备好拥有管理特权的帐户的凭据。 若要添加的宿主服务器，请按照下列步骤：

1. 登录到 Azure 堆栈运算符门户为服务管理员。
2. 选择“更多服务”。
3. 选择“管理资源” > “MySQL 宿主服务器” > “+添加”。 这将打开**添加 MySQL 宿主服务器**对话框中，以下屏幕截图中所示。

   ![配置宿主服务器](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. 提供 MySQL 服务器实例的连接详细信息。

   * 有关**MySQL 宿主服务器名称**，提供的完全限定的域名 (FQDN) 或有效的 IPv4 地址。 不要使用短的 VM 名称。
   * 默认 MySQL 实例未提供，因此你必须指定**大小的宿主服务器以 gb 为单位**。 输入数据库服务器的容量接近的大小。
   * 保留默认设置，为**订阅**。
   * 有关**资源组**，创建一个新的活动，或使用现有组。

   > [!NOTE]
   > 如果可以通过租户和管理 Azure 资源管理器访问 MySQL 实例，则可以将其资源提供程序的控制之下。 但是，MySQL 实例**必须**分配以独占方式与资源提供程序。

5. 选择**Sku**以打开**创建 SKU**对话框。

   ![创建 MySQL SKU](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   SKU**名称**应反映 SKU 的属性，以便用户可以将其数据库部署到适当的 SKU。

   >[!IMPORTANT]
   >特殊字符，包括空格和期间，不支持在**名称**或**层**当 MySQL 资源提供程序创建一种的 SKU。

6. 选择**确定**创建 SKU。
7. 下**添加 MySQL 宿主服务器**，选择**创建**。

添加服务器时，应为他们分配一种新的或现有的 SKU 来区分服务产品。 例如，你可以提供更高的数据库和自动备份的 MySQL enterprise 实例。 你可以在你的组织中保留不同的部门此高性能服务器。

## <a name="increase-backend-database-capacity"></a>增加后端数据库容量

你可以部署在 Azure 堆栈门户中的多个 MySQL 服务器增加后端数据库容量。 将这些服务器添加到新的或现有的 SKU。 如果你将服务器添加到现有的 SKU，请确保服务器特征 SKU 中的其他服务器相同。

## <a name="make-mysql-database-servers-available-to-your-users"></a>让用户能够使用 MySQL 数据库服务器

创建计划，并提供可向用户提供 MySQL 数据库服务器。 将 Microsoft.MySqlAdapter 服务添加到计划，然后添加默认配额，或创建新的配额。

![创建计划和提供的数据库](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="next-steps"></a>后续步骤

[创建 MySQL 数据库](azure-stack-mysql-resource-provider-databases.md)