---
title: 如何管理 DHCP
description: 本文介绍如何在 Azure VMWare 解决方案（AVS）中管理 DHCP
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: ccf28c94e1991681c238f51847fe228313abe29e
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740442"
---
# <a name="how-to-manage-dhcp-in-azure-vmware-solution-avs-preview"></a>如何在 Azure VMWare 解决方案（AVS）预览版中管理 DHCP

NSX 为私有云提供配置 DHCP 的能力。 如果你计划使用 NSX-T 来托管你的 DHCP 服务器，请参阅[创建 dhcp 服务器](#create-dhcp-server)。 否则，如果在网络中有第三方外部 DHCP 服务器，并且想要将请求中继到该 DHCP 服务器，请参阅[创建 dhcp 中继服务](#create-dhcp-relay-service)。

## <a name="create-dhcp-server"></a>创建 DHCP 服务器

使用以下步骤在 NSX-T 上配置 DHCP 服务器。

从 NSX manager 中，导航到 "**网络**" 选项卡，然后选择 " **IP 管理**" 下的**DHCP** 。 选择 "**添加服务器**" 按钮。 然后提供服务器名称和服务器 IP 地址。 完成后，选择 "**保存**"。

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="添加 DHCP 服务器" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>将 DHCP 服务器连接到第1层网关。

选择**第1层网**关，选择网关并选择 "**编辑**"

:::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="选择要使用的网关" border="true":::

通过选择 "**无 IP 分配集**" 添加子网

:::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="添加子网" border="true":::

在下一个屏幕上，从 "**类型**" 下拉列表中选择 " **DHCP 本地服务器**"。 对于**DHCP 服务器**，选择 "**默认 DHCP** "，然后选择 "**保存**"。

:::image type="content" source="./media/manage-dhcp/set-ip-address-management.png" alt-text="选择 dhcp 服务器的选项" border="true":::

在 "**第1层网关**" 窗口中，选择 "**保存**"。 在下一个屏幕上，你会看到**更改已保存**，请选择 "**关闭编辑**" 完成操作。

### <a name="add-a-network-segment"></a>添加网络段

创建 DHCP 服务器后，需要向其添加网络段。

在 "NSX-T" 中选择 "**网络**" 选项卡，然后选择 "**连接**" 下的**段**。 选择 "**添加段**"。 命名段并连接到第1层网关。 接下来，选择 "**设置子网**" 以配置新的子网。 

:::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="添加新的网络段" border="true":::

在 "**设置子网**" 窗口中，选择 "**添加子网**"。 输入网关 IP 地址和 DHCP 范围，选择 "**添加**"，然后单击 "**应用**"

:::image type="content" source="./media/manage-dhcp/add-subnet-segment.png" alt-text="添加网络段" border="true":::

完成后，选择 "**保存**" 以完成添加网络段。

:::image type="content" source="./media/manage-dhcp/segments-complete.png" alt-text="段完成" border="true":::

## <a name="create-dhcp-relay-service"></a>创建 DHCP 中继服务

在 NXT 的 "T" 窗口中，选择 "**网络**" 选项卡，然后在 " **IP 管理**" 下选择**DHCP**。 选择 "**添加服务器**"。 为 "**服务器类型**" 选择 "DHCP 中继"，并输入中继服务器的服务器名称和 IP 地址。 选择“保存”  以保存更改。

:::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="创建 dhcp 中继服务器" border="true":::

在 "**连接**" 下选择**第1层网关**。 选择第1层网关上的垂直省略号，然后选择 "**编辑**"。

:::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="编辑第1层网关" border="true":::

选择 "**没有 Ip 分配集**"，以定义 ip 地址分配。

:::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="编辑 ip 地址分配" border="true":::

在对话框中，为 "**类型**" 选择 " **DHCP 中继服务器**"。 在 " **Dhcp 中继**" 下拉列表中，选择你的 DHCP 中继服务器。 完成后，选择 "**保存**"

:::image type="content" source="./media/manage-dhcp/set-ip-address-management-relay.png" alt-text="设置 ip 地址管理" border="true":::

在段上指定 DHCP 范围 IP：

> [!NOTE]
> 此配置是在 DHCP 客户端段上实现 DHCP 中继功能所必需的。 

在 "**连接**" 下，选择 "**段**"。 选择垂直省略号，然后选择 "**编辑**"。 相反，如果要添加新段，可以选择 "**添加段**" 来创建新段。

:::image type="content" source="./media/manage-dhcp/edit-segments.png" alt-text="编辑网络子网" border="true":::

添加有关段的详细信息。 选择 " **子**网" 下的值，或**设置**子网来添加或修改子网。

:::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="网段" border="true":::

选择垂直省略号，然后选择 "**编辑**"。 如果需要创建新的子网，请选择 "**添加子网**" 来创建网关并配置 DHCP 范围。 提供 IP 池的范围并选择 "**应用**"，然后选择 "**保存**"

:::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="编辑子网" border="true":::

现在，将 DHCP 服务器池分配给段。

:::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="分配给分段的 DHCP 服务器池" border="true":::
