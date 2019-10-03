---
title: 教程 - 在事件中心启用虚拟网络集成和防火墙 | Microsoft Docs
description: 本教程介绍如何将事件中心与虚拟网络和防火墙集成，以启用安全访问。
services: event-hubs
author: axisc
manager: darosa
ms.author: aschhab
ms.date: 11/28/2018
ms.topic: tutorial
ms.service: event-hubs
ms.custom: mvc
ms.openlocfilehash: 0f7c7e348c154aab1deb10273346a5395599b745
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605858"
---
# <a name="tutorial-enable-virtual-networks-integration-and-firewalls-on-event-hubs-namespace"></a>教程：在事件中心命名空间中启用虚拟网络集成和防火墙

[虚拟网络 (VNet) 服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)可通过直接连接，将 VNet 的虚拟网络专用地址空间和标识扩展到 Azure 服务。 使用终结点可以保护关键的 Azure 服务资源，只允许在客户自己的虚拟网络中对其进行访问。 从 VNet 发往 Azure 服务的流量始终保留在 Microsoft Azure 主干网络中。

使用防火墙可以限制从特定 IP 地址（或 IP 地址范围）对事件中心命名空间的访问

本教程介绍如何使用门户在现有的 Azure 事件中心命名空间中集成虚拟网络服务终结点并设置防火墙（IP 筛选）。

本教程介绍以下操作：
> [!div class="checklist"]
> * 如何将虚拟网络服务终结点与事件中心命名空间集成。
> * 如何在事件中心命名空间中设置防火墙（IP 筛选）。

>[!WARNING]
> 实现虚拟网络集成可以防止其他 Azure 服务与事件中心进行交互。
>
> 启用虚拟网络后不支持第一方集成。
> 不使用虚拟网络的常见 Azure 方案 -
> * Azure 诊断和日志记录
> * Azure 流分析
> * 事件网格集成
> * Web 应用和 Functions 必须在虚拟网络中。
> * IoT 中心路由
> * IoT Device Explorer


> [!IMPORTANT]
> 事件中心的标准  和专用  层支持虚拟网络。 基本层不支持它。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户][]。

## <a name="prerequisites"></a>先决条件

我们将利用现有的事件中心命名空间，因此请确保有可用的事件中心命名空间。 如果没有，请参阅[此教程](./event-hubs-create.md)

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

首先转到 [Azure 门户][Azure portal]，使用 Azure 订阅登录。

## <a name="select-event-hubs-namespace"></a>选择事件中心命名空间

本教程假设已创建一个事件中心命名空间，稍后我们将导航到该命名空间。

## <a name="navigate-to-firewalls-and-virtual-networks-experience"></a>导航到“防火墙和虚拟网络”体验

使用门户左窗格中的导航菜单选择“防火墙和虚拟网络”选项。 

  ![导航到菜单](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-landing-page.png)

  首次访问此页时，必须选中“所有网络”单选按钮。  这意味着，事件中心命名空间允许所有传入的连接。

## <a name="add-virtual-network-service-endpoint"></a>添加虚拟网络服务终结点

若要限制访问，需要集成此事件中心命名空间的虚拟网络服务终结点。

1. 单击页面顶部的“所选网络”单选按钮，以启用页面剩余部分的菜单选项。 
  ![所选网络](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. 在页面的“虚拟网络”部分，选择“+ 添加现有虚拟网络”选项。 此时会滑入一个窗格，让你选择已创建的虚拟网络。
  ![添加现有虚拟网络](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane.png)
3. 从列表中选择虚拟网络，并选择子网。
   ![选择子网](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-with-subnet-query.png)
4. 将虚拟网络添加到列表之前，必须启用服务终结点。 如果未启用服务终结点，门户将提示启用。
  ![选择子网并启用终结点](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-enabling.png)
    > [!NOTE]
    > 如果无法使用 ARM 模板启用服务终结点，可以忽略有关缺少虚拟网络服务终结点的消息。 此功能在门户中不可用。

5. 在所选子网中启用服务终结点之后，可以继续将其添加到允许的虚拟网络列表。
  ![启用终结点后添加子网](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-adding.png)

6. 在顶部功能区中点击“保存”，以便在服务中保存虚拟网络配置。  请等待几分钟，直到门户通知中显示确认消息。

## <a name="add-firewall-for-specified-ip"></a>为指定的 IP 添加防火墙

可以使用防火墙规则，将事件中心命名空间的访问权限限制给有限的 IP 地址范围或特定的 IP 地址。

1. 单击页面顶部的“所选网络”单选按钮，以启用页面剩余部分的菜单选项。 
  ![所选网络](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. 在“防火墙”部分的“地址范围”网格下，可以添加一个或多个特定 IP 地址或 IP 地址范围。  
  ![添加 IP 地址](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall.png)
3. 添加多个 IP 地址（或 IP 地址范围）后，在顶部功能区中点击“保存”，以确保在服务端保存配置。  请等待几分钟，直到门户通知中显示确认消息。
  ![添加 IP 地址并点击“保存”](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall-hitting-save.png)

## <a name="adding-your-current-ip-address-to-the-firewall-rules"></a>将当前 IP 地址添加到防火墙规则

1. 还可以通过选中紧靠在“地址范围”网格上方的“添加客户端 IP 地址(当前 IP 地址)”复选框，快速添加当前的 IP 地址。
  ![添加当前 IP 地址](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save.png)
2. 将当前 IP 地址添加到防火墙规则后，在顶部功能区中点击“保存”，以确保在服务端保存配置。  请等待几分钟，直到门户通知中显示确认消息。
  ![添加当前 IP 地址并点击“保存”](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save-after-saving.png)

## <a name="conclusion"></a>结束语

在本教程中，你已将虚拟网络终结点和防火墙规则与现有的事件中心命名空间相集成。 你已了解：
> [!div class="checklist"]
> * 如何将虚拟网络服务终结点与事件中心命名空间集成。
> * 如何在事件中心命名空间中设置防火墙（IP 筛选）。


[Azure portal]: https://portal.azure.com/
