---
title: 在虚拟网络中预配池
description: 如何在 Azure 虚拟网络中创建 Batch 池，以便计算节点可以安全地与网络（例如文件服务器）中的其他 VM 通信。
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: seodec18
ms.openlocfilehash: 22344b5466b0388921a481b1adf41495d66dbfe0
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86115405"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>在虚拟网络中创建 Azure Batch 池

创建 Azure Batch 池时，可以在指定的 [Azure 虚拟 网络](../virtual-network/virtual-networks-overview.md) (VNet) 的子网中预配该池。 本文介绍了如何在 VNet 中设置 Batch 池。

## <a name="why-use-a-vnet"></a>为何使用 VNet？

池中的计算节点可以相互进行通信，例如运行多实例任务，而无需单独的 VNet。 但是，默认情况下，池中的节点不能与池中的虚拟机（例如许可证服务器或文件服务器）进行通信。

若要允许计算节点与其他虚拟机或本地网络进行安全通信，可以在 Azure VNet 的子网中预配该池。

## <a name="prerequisites"></a>先决条件

- “身份验证”。 若要使用 Azure VNet，Batch 客户端 API 必须使用 Azure Active Directory (AD) 身份验证。 有关 Azure AD 的 Azure Batch 支持，请参阅[使用 Active Directory 对 Batch 服务解决方案进行身份验证](batch-aad-auth.md)。

- **一个 Azure VNet**。 参阅以下部分，了解 VNet 要求和配置。 若要提前准备具有一个或多个子网的 VNet，可以使用 Azure 门户、Azure PowerShell、Azure 命令行接口 (CLI) 或其他方法。
  - 若要创建基于 Azure 资源管理器的 VNet，请参阅[创建虚拟网络](../virtual-network/manage-virtual-network.md#create-a-virtual-network)。 建议为新部署使用基于资源管理器的 VNet，并且仅支持使用虚拟机配置的池。
  - 若要创建经典 VNet，请参阅 [Create a virtual network (classic) with multiple subnets](../virtual-network/create-virtual-network-classic.md)（创建具有多个子网的虚拟网络（经典））。 只有使用云服务配置的池支持经典 VNet。

## <a name="vnet-requirements"></a>VNet 要求

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-azure-portal"></a>在 Azure 门户中创建具有 VNet 的池

在创建 VNet 并将一个子网分配给它后，可以使用该 VNet 创建 Batch 池。 请按照下列步骤在 Azure 门户中创建池： 

1. 导航到 Azure 门户中的批处理帐户。 此帐户必须与要使用的 VNet 所在的资源组位于同一订阅和区域中。
2. 在左侧的“设置”窗口中，选择“池”菜单项。
3. 在 "**池**" 窗口中，选择 "**添加**"。
4. 在“添加池”窗口中，从“映像类型”下拉列表中选择要使用的选项。 
5. 为自定义映像选择正确的“发布服务器/产品/SKU”。
6. 指定剩余所需设置，包括“节点大小”、“目标专用节点”和“低优先级节点”，以及任何所需的可选设置。  
7. 在“虚拟网络”中，选择要使用的虚拟网络和子网。

   ![使用虚拟网络添加池](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>用户定义的用于强制隧道的路由

你的组织中的要求可能会将来自子网的 internet 绑定流量重定向（强制）返回到本地位置进行检查和日志记录。 此外，你可能已在 VNet 中为子网启用强制隧道。

若要确保池中的节点在启用强制隧道的 VNet 中正常工作，必须为该子网添加以下[用户定义的路由](../virtual-network/virtual-networks-udr-overview.md)（UDR）：

- Batch 服务需要与节点通信，以便进行计划任务。 若要启用此通信，请在你的 Batch 帐户所在的区域中为 Batch 服务使用的每个 IP 地址添加一个 UDR。 若要获取 Batch 服务的 IP 地址列表，请参阅[本地服务标记](../virtual-network/service-tags-overview.md)。

- 确保到 Azure 存储的出站流量（具体而言， `<account>.table.core.windows.net` `<account>.queue.core.windows.net` `<account>.blob.core.windows.net` 本地网络不会阻止形式为、和的 url）。

添加 UDR 时，请为每个相关 Batch IP 地址前缀定义路由，并将“下一个跃点类型”设置为“Internet” 。

![用户定义的路由](./media/batch-virtual-network/user-defined-route.png)

> [!WARNING]
> Batch 服务 IP 地址随时可能会更改。 若要防止 IP 地址更改，请创建一个进程来自动刷新 Batch 服务 IP 地址，并在路由表中使它们保持最新。

## <a name="next-steps"></a>后续步骤

- 了解 [Batch 服务工作流和主要资源](batch-service-workflow-features.md)，例如池、节点、作业和任务。
- 了解如何[在 Azure 门户中创建用户定义的路由](../virtual-network/tutorial-create-route-table-portal.md)。
