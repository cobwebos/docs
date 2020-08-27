---
title: 将本地环境与私有云对等互连
description: 在此 Azure VMware 解决方案教程中，你将在 Azure VMware 解决方案中创建与私有云的 ExpressRoute Global Reach 对等互连。
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: db3f5988cb8c07d9b6e80f500ac6aff8f96dfded
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750452"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>教程：将本地环境与私有云对等互连

ExpressRoute Global Reach 会将本地环境连接到私有云。 ExpressRoute Global Reach 连接是在私有云 ExpressRoute 线路和本地环境的现有 ExpressRoute 连接之间建立的。  我们提供了关于使用 Azure CLI 和 PowerShell 配置 ExpressRoute Global Reach 的说明，并已通过特定详细信息和示例扩展了 [CLI 命令](../expressroute/expressroute-howto-set-global-reach-cli.md)，以便帮助你在本地环境和 Azure VMware 解决方案私有云之间配置 ExpressRoute Global Reach 对等互连。   

通过 ExpressRoute Global Reach 在两个 ExpressRoute 线路之间启用连接之前，请查看有关如何[在不同的 Azure 订阅之间启用连接](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)的文档。  [配置 Azure 到私有云网络](tutorial-configure-networking.md)时使用的 ExpressRoute 线路要求，在使用 Global Reach 与 ExpressRoute 网关或其他 ExpressRoute 线路对等互连时创建并使用授权密钥。 你已使用了 ExpressRoute 线路的授权密钥，接下来你会再创建一个授权密钥，以便与本地 ExpressRoute 线路对等互连。

> [!TIP]
> 在这些说明的上下文中，本地 ExpressRoute 线路是线路 1，私有云 ExpressRoute 线路位于其他的订阅中并被标记为线路 2。  

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 利用现有的说明来管理 ExpressRoute 线路和 ExpressRoute Global Reach 对等互连
> * 为线路 2（私有云 ExpressRoute 线路）创建授权密钥
> * 在线路 1 所在订阅的 Cloud Shell 中使用 Azure CLI，以启用本地到私有云 ExpressRoute Global Reach 对等互连

## <a name="prerequisites"></a>先决条件

本教程的先决条件如下：
- 具有与 Azure 虚拟网络 (VNet) 中的 ExpressRoute 网关对等互连的 ExpressRoute 线路（从对等互连过程的角度而言，这是线路 2）的私有云。
- 用于将本地环境连接到 Azure 的正常运行的 ExpressRoute 独立线路 – 从对等互连过程的角度而言，这是线路 1。
- 用于 ExpressRoute Global Reach 对等互连的 /29 非重叠[网络地址块](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings)。

## <a name="create-an-expressroute-authorization-key-in-the-azure-vmware-solution-private-cloud"></a>在 Azure VMware 解决方案私有云中创建 ExpressRoute 授权密钥

1. 从私有云“管理”下的“概述”中，选择“连接 > ExpressRoute > 请求授权密钥” 。

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="选择“连接”>“ExpressRoute”>“请求授权密钥”以启动新请求。":::

2. 输入授权密钥的名称，并选择“创建”。 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="单击“创建”以新建授权密钥。":::

   创建后，新密钥会出现在私有云的授权密钥列表中。 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="确认新授权密钥出现在私有云的密钥列表中。":::

3. 记下此授权密钥、ExpressRoute ID 以及 /29 地址块。 在下一步中，你将使用它们来完成对等互连。 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>使用授权密钥将私有云与本地对等互连

现已为 ExpressRoute 线路创建授权密钥，接下来即可以将其与本地 ExpressRoute 线路对等互连。  通过在 Cloud Shell 中使用 Azure CLI 并使用私有云 ExpressRoute 线路的资源 ID 和授权密钥（已在上一步中创建），从本地 ExpressRoute 线路完成对等互连。

> [!TIP]  
> 为保证 Azure CLI 命令输出的简洁性，这些指令可以[使用 `–query` 参数来执行 JMESPath 查询以便仅显示所需的结果](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest)。


1. 使用本地 ExpressRoute 线路所在的同一订阅登录到 Azure 门户，并打开 Cloud Shell。 将 Shell 保留为 Bash。
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="登录到 Azure 门户并打开 Cloud Shell。":::
 
2. 在命令行中，输入 Azure CLI 命令，以使用特定信息、资源 ID、授权密钥和 /29 CIDR 网络块创建对等互连。 

   下面是你将使用的命令的示例，以及指示成功对等互连的输出。 示例命令基于[“在位于不同 Azure 订阅的 ExpressRoute 线路之间启用连接”中的步骤 3](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions) 所使用的命令。

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="在 Cloud Shell 中使用 Azure CLI 命令创建 ExpressRoute Global Reach 对等互连。":::
 
   现在，你应该能够通过 ExpressRoute Global Reach 对等互连从本地环境连接到私有云。

> [!TIP]
> 可以按照[禁用本地网络之间的连接](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks)说明删除刚刚创建的对等互连。


## <a name="next-steps"></a>后续步骤

如果计划使用混合云扩展 (HCX) 将 VM 工作负荷迁移到私有云，请按照[安装 HCX 以使用 Azure VMware 解决方案](hybrid-cloud-extension-installation.md)这一流程操作。


<!-- LINKS - external-->

<!-- LINKS - internal -->
