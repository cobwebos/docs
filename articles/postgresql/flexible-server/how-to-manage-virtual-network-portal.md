---
title: 管理虚拟网络-Azure 门户 Azure Database for PostgreSQL-灵活的服务器
description: 使用 Azure 门户为 Azure Database for PostgreSQL 灵活的服务器创建和管理虚拟网络
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 46d8fe6427b2a3e7811719792ac4bf67ddbcc3c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934755"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>使用 Azure 门户为 Azure Database for PostgreSQL 灵活的服务器创建和管理虚拟网络

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

Azure Database for PostgreSQL 灵活的服务器支持两种类型的相互排斥的网络连接方法来连接到你的灵活服务器。 这两个选项如下：

* 公共访问（允许的 IP 地址）
* 专用访问（VNet 集成）

在本文中，我们将重点介绍如何使用 Azure 门户 ** (VNet 集成) ** 创建 PostgreSQL 服务器。 通过私有访问 (VNet 集成) ，你可以将灵活的服务器部署到你自己的 [Azure 虚拟网络](../../virtual-network/virtual-networks-overview.md)中。 Azure 虚拟网络提供专用的安全网络通信。 通过专用访问，与 PostgreSQL 服务器的连接仅限于虚拟网络。 若要了解详细信息，请参阅 [ (VNet 集成) 的私有访问权限 ](./concepts-networking.md#private-access-vnet-integration)。

在服务器创建过程中，可以将灵活服务器部署到虚拟网络和子网中。 部署灵活服务器后，无法将它移动到其他虚拟网络、子网或公共访问（允许的 IP 地址）。

## <a name="prerequisites"></a>必备条件
若要在虚拟网络中创建灵活的服务器，需要：
- [虚拟网络](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > 虚拟网络和子网应与灵活服务器位于同一区域和订阅中。

-  向**DBforPostgreSQL/flexibleServers**[委托子网](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service)。 这种委托意味着只有 Azure Database for PostgreSQL 灵活的服务器才能使用该子网。 不能在委派子网中使用其他 Azure 资源类型。

## <a name="create-azure-database-for-postgresql---flexible-server-in-an-already-existing-virtual-network"></a>在现有的虚拟网络中创建 Azure Database for PostgreSQL 灵活的服务器

1. 在门户左上角选择“创建资源”  (+)。
2. 选择“数据库” > “用于 PostgreSQL 的 Azure 数据库”。 您还可以在搜索框中输入 **PostgreSQL** 来查找该服务。
3. 选择“灵活服务器”作为部署选项。
4. 填写 " **基本** 信息" 窗体。
5. 请参阅 " **网络** " 选项卡以配置要连接到服务器的方式。
6. 在 " **连接方法**" 中，选择 " **专用访问 (VNet 集成") **。 中转到 " **虚拟网络** "，并选择已存在的 *虚拟网络* 和 *子网* ，作为上述先决条件的一部分创建。
7. 选择“查看 + 创建”，查看你的灵活服务器配置。
8. 选择“创建”以预配服务器。 预配可能需要几分钟时间。

>[!Note]
> 将灵活的服务器部署到虚拟网络和子网后，不能将其移动到公共访问 (允许的 IP 地址) 。
## <a name="next-steps"></a>后续步骤
- [使用 Azure CLI 创建和管理 Azure Database for PostgreSQL 灵活的服务器虚拟网络](./how-to-manage-virtual-network-cli.md)。
- 详细了解 [Azure Database for PostgreSQL-灵活服务器中的网络](./concepts-networking.md)
- 了解 [Azure Database for PostgreSQL 灵活的服务器虚拟网络的](./concepts-networking.md#private-access-vnet-integration)详细信息。