---
title: 管理区域冗余高可用性-Azure 门户-Azure Database for MySQL 灵活服务器
description: 本文介绍如何通过 Azure 门户在 Azure Database for MySQL 灵活的服务器中启用或禁用区域冗余高可用性。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: 09cd7428519cbf84c785efa16b61b9507a3c0b94
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934776"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-preview"></a>在 Azure Database for MySQL 灵活的服务器 (预览版中管理区域冗余高可用性) 

本文介绍如何在灵活的服务器中启用或禁用区域冗余高可用性配置。

高可用性功能设置在不同区域中物理分隔主副本和备用副本。 有关更多详细信息，请参阅 [高可用性概念文档](./concepts/../concepts-high-availability.md)。 

> [!IMPORTANT]
> 你只能在创建灵活的服务器期间启用区域冗余高可用性。

本页提供如何启用或禁用高可用性的指南。 此操作不会更改其他设置，包括 VNET 配置、防火墙设置和备份保留期。 同样，禁用高可用性是一个联机操作，并且不会影响应用程序的连接和操作。

> [!IMPORTANT]
> 区域冗余高可用性在一组有限的区域中提供：东南亚、WestUS 2、西欧和美国东部。  

## <a name="enable-high-availability-during-server-creation"></a>在服务器创建过程中启用高可用性

本部分详细介绍了 HA 相关的字段。 创建灵活的服务器时，可以按照以下步骤部署高可用性。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择 "灵活服务器"，然后单击 " **创建**"。  有关如何填写 **订阅**、 **资源组**、 **服务器名称**、 **区域**和其他字段等详细信息的详细信息，请参阅服务器创建的操作方法文档。

2.  单击可用性选项中的 " **区域冗余高可用性** " 复选框。

3.  如果要更改默认计算和存储，请单击 "  **配置服务器**"。

4.  如果选中 "高可用性" 选项，则可突增层将不可供选择。 你可以选择 " **常规用途** " 或 " **内存优化** " 计算层。

    > [!IMPORTANT]
    > 仅支持 " ***常规用途*** " 和 " ***内存优化*** " 定价层的区域冗余高可用性。

5.  从下拉列表中选择所选的 **计算大小** 。

6.  使用滑动条选择 GiB 中的 **存储大小** ，并选择7天到35天之间的 **备份保留期** 。   

## <a name="disable-high-availability"></a>禁用高可用性

请按照以下步骤来禁用已配置了区域冗余的灵活服务器的高可用性。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择现有 Azure Database for MySQL 灵活服务器。

2.  在 "灵活服务器" 页上，单击前面板中的 " **高可用性** " 以打开 "高可用性" 页。

3.  单击 "**区域冗余高可用性**" 复选框以禁用该选项，然后单击 " **保存**   " 以保存更改。

4.  此时将显示一个确认对话框，你可以在其中确认禁用 HA。

5.  单击 " **禁用 HA** " 按钮，禁用高可用性。

6.  将显示一条通知，指出正在进行高可用性部署。

## <a name="next-steps"></a>后续步骤

-   了解 [业务连续性](./concepts-business-continuity.md)
-   了解 [区域冗余高可用性](./concepts-high-availability.md)
