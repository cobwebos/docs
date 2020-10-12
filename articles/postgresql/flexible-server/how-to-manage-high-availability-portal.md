---
title: 管理区域冗余高可用性-Azure 门户 Azure Database for PostgreSQL-灵活服务器
description: 本文介绍如何通过 Azure 门户在 Azure Database for PostgreSQL 灵活的服务器中启用或禁用区域冗余高可用性。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: fc1bca1265139a438fad86bfce770026866d9a2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933339"
---
# <a name="manage-zone-redundant-high-availability-in-flexible-server"></a>管理灵活服务器中的区域冗余高可用性

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

本文介绍如何在灵活的服务器中启用或禁用区域冗余高可用性配置。

高可用性功能设置在不同区域中物理分隔主副本和备用副本。 有关更多详细信息，请参阅 [高可用性概念文档](./concepts-high-availability.md)。 你可以选择在创建灵活的服务器时或在创建后启用高可用性。 本页提供如何启用或禁用高可用性的指南。 此操作不会更改其他设置，包括 VNET 配置、防火墙设置和备份保留期。 同样，启用和禁用高可用性是一个联机操作，并且不会影响应用程序的连接和操作。

## <a name="pre-requisites"></a>先决条件

区域冗余高可用性仅适用于支持多个区域的区域。 

## <a name="enable-high-availability-during-server-creation"></a>在服务器创建过程中启用高可用性

本部分详细介绍了 HA 相关的字段。 创建灵活的服务器时，可以按照以下步骤部署高可用性。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择 "灵活服务器"，然后单击 "创建"。  有关如何填写 **订阅**、 **资源组**、 **服务器名称**、 **区域**和其他字段等详细信息的详细信息，请参阅服务器创建的操作方法文档。
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/subscription-region.png" alt-text="查看订阅和区域&quot;:::

2.  选择 **可用性区域**。 如果要在数据库所在的同一可用性区域中归置应用程序以降低延迟，这会很有用。 如果希望灵活服务器部署在任何可用性区域上，请选择 " **无首选项** "。
    ![AZ 选择 ]() :::image type="content" source="./media/how-to-manage-high-availability-portal/zone-selection.png" alt-text="查看订阅和区域&quot;:::

2.  选择 **可用性区域**。 如果要在数据库所在的同一可用性区域中归置应用程序以降低延迟，这会很有用。 如果希望灵活服务器部署在任何可用性区域上，请选择 " 复选框。

    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-checkbox.png" alt-text="查看订阅和区域&quot;:::

2.  选择 **可用性区域**。 如果要在数据库所在的同一可用性区域中归置应用程序以降低延迟，这会很有用。 如果希望灵活服务器部署在任何可用性区域上，请选择 "  **配置服务器**"。
 
    :::image type="content" source="./media/how-to-manage-high-availability-portal/configure-server.png" alt-text="查看订阅和区域&quot;:::

2.  选择 **可用性区域**。 如果要在数据库所在的同一可用性区域中归置应用程序以降低延迟，这会很有用。 如果希望灵活服务器部署在任何可用性区域上，请选择 " 计算层。 然后，可以从下拉列表中选择所选的 **计算大小** 。

    :::image type="content" source="./media/how-to-manage-high-availability-portal/select-compute.png" alt-text="查看订阅和区域&quot;:::

2.  选择 **可用性区域**。 如果要在数据库所在的同一可用性区域中归置应用程序以降低延迟，这会很有用。 如果希望灵活服务器部署在任何可用性区域上，请选择 ":::  


6.  使用滑动条选择 GiB 中的 **存储大小** ，并选择7天到35天之间的 **备份保留期** 。
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/storage-backup.png" alt-text="查看订阅和区域&quot;:::

2.  选择 **可用性区域**。 如果要在数据库所在的同一可用性区域中归置应用程序以降低延迟，这会很有用。 如果希望灵活服务器部署在任何可用性区域上，请选择 "::: 

7. 单击 **“保存”** 。 

## <a name="enable-high-availability-post-server-creation"></a>启用后期服务器创建的高可用性

按照以下步骤为现有的灵活服务器启用高可用性。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择现有的 PostgreSQL 灵活服务器。

2.  在 "灵活服务器" 页上，单击左侧面板中的 " **高可用性** " 以打开 "高可用性" 页。
   
     :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="查看订阅和区域&quot;:::

2.  选择 **可用性区域**。 如果要在数据库所在的同一可用性区域中归置应用程序以降低延迟，这会很有用。 如果希望灵活服务器部署在任何可用性区域上，请选择 "   以保存更改。

     :::image type="content" source="./media/how-to-manage-high-availability-portal/enable-high-availability.png" alt-text="查看订阅和区域&quot;:::

2.  选择 **可用性区域**。 如果要在数据库所在的同一可用性区域中归置应用程序以降低延迟，这会很有用。 如果希望灵活服务器部署在任何可用性区域上，请选择 " 按钮以启用高可用性。

6.  将显示一条通知，指出正在进行高可用性部署。

## <a name="disable-high-availability"></a>禁用高可用性

请按照以下步骤来禁用已配置了区域冗余的灵活服务器的高可用性。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择现有的 Azure Database for PostgreSQL 灵活服务器。

2.  在 "灵活服务器" 页上，单击前面板中的 " **高可用性** " 以打开 "高可用性" 页。
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="查看订阅和区域&quot;:::

2.  选择 **可用性区域**。 如果要在数据库所在的同一可用性区域中归置应用程序以降低延迟，这会很有用。 如果希望灵活服务器部署在任何可用性区域上，请选择 " 以保存更改。

     :::image type="content" source="./media/how-to-manage-high-availability-portal/disable-high-availability.png" alt-text="查看订阅和区域&quot;:::

2.  选择 **可用性区域**。 如果要在数据库所在的同一可用性区域中归置应用程序以降低延迟，这会很有用。 如果希望灵活服务器部署在任何可用性区域上，请选择 " 按钮，禁用高可用性。

6.  将显示一条通知，指出正在进行高可用性部署。

## <a name="next-steps"></a>后续步骤

-   了解 [业务连续性](./concepts-business-continuity.md)
-   了解 [区域冗余高可用性](./concepts-high-availability.md)
