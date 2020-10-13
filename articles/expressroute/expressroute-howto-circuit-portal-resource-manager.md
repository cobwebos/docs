---
title: 快速入门：创建和修改 ExpressRoute 线路 - Azure 门户
description: 本快速入门介绍如何使用 Azure 门户创建、预配、验证、更新、删除和取消预配 ExpressRoute 线路。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: duau
ms.openlocfilehash: dbb28726d1c2eaf887ced818284d4b1e0298f8db
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767806"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit"></a>快速入门：创建和修改 ExpressRoute 线路

本快速入门介绍如何使用 Azure 门户和 Azure 资源管理器部署模型创建 ExpressRoute 线路。 还可以检查线路状态、更新、删除或取消预配线路。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 在开始配置之前，请查看[先决条件](expressroute-prerequisites.md)和[工作流](expressroute-workflows.md)。
* 为了更好地了解这些步骤，可以在开始之前[观看视频](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)。

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>创建和预配 ExpressRoute 线路

### <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

从浏览器导航到 [Azure 门户](https://portal.azure.com)并使用 Azure 帐户登录。

### <a name="create-a-new-expressroute-circuit"></a>创建新的 ExpressRoute 线路

> [!IMPORTANT]
> 从发布服务密钥的那一刻起，将对 ExpressRoute 线路进行计费。 确保连接服务提供商准备好预配线路后就执行此操作。

可以通过选择创建新资源的选项来创建 ExpressRoute 线路。 

1. 在 Azure 门户菜单中，选择“创建资源”。 选择“网络” > “ExpressRoute”，如下图所示：

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/create-expressroute-circuit-menu.png" alt-text="创建 ExpressRoute 线路":::

2. 选择“ExpressRoute”即可看到“创建 ExpressRoute”页。 为线路提供“资源组”、“区域”和“名称”。 然后选择“下一步:配置 >”。

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-basic.png" alt-text="创建 ExpressRoute 线路":::

3. 在此页上填写相应值时，请务必指定正确的 SKU 层（“本地”、“标准”或“高级”）和数据计量计费模型（“不限流量”或“按流量计费”）。

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-configuration.png" alt-text="创建 ExpressRoute 线路":::
    
    * **端口类型**确定你是连接到服务提供商还是直接在对等互连位置连接到 Microsoft 的全球网络。
    * **新建或从经典线路中导入**确定是要创建新线路还是要将经典线路迁移到 Azure 资源管理器。
    * **提供商**指你将向其请求提供服务的 Internet 服务提供商。
    * **对等互连位置**是与 Microsoft 建立对等互连的实际位置。

    > [!IMPORTANT]
    > “对等互连位置”指明了与 Microsoft 建立对等互连的[实际位置](expressroute-locations.md)。 此位置与“Location”属性**没有**关系，后者指的是 Azure 网络资源提供商所在的地理位置。 尽管两者之间没有关系，但最好是选择地理上与线路对等互连位置靠近的网络资源提供商。

    * **SKU** 确定是启用 ExpressRoute 本地版、ExpressRoute 标准版还是 ExpressRoute 高级版加载项。 可以指定“本地”以获取本地 SKU，指定“标准”以获取标准 SKU，或指定“高级”以获取高级版加载项  。 可以更改 SKU 以启用高级版加载项。
    > [!IMPORTANT]
    > 不能将 SKU 从“标准”/“高级”更改为“本地” 。
    
    * **计费模型**确定计费类型。 可以指定“Metered”**** 以获取数据流量套餐，指定“Unlimited”**** 以获取无限制流量套餐。 可以将计费类型从“按流量计费”更改为“不限流量” 。

    > [!IMPORTANT]
    > 你无法将类型从“不限流量”更改为“按流量计费” 。

    * **允许经典操作**将允许经典虚拟网络链接到线路。

### <a name="view-the-circuits-and-properties"></a>查看线路和属性

**查看所有线路**

在左侧菜单中选择“所有服务”>“网络”>“ExpressRoute 线路”，即可查看创建的所有线路。

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-menu.png" alt-text="创建 ExpressRoute 线路":::

在订阅中创建的所有 Expressroute 线路都将在此处显示。

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-list.png" alt-text="创建 ExpressRoute 线路":::

**查看属性**

可以通过选择线路来查看线路属性。 在线路的“概述”**** 页上，服务密钥显示在“服务密钥”字段中。 请查看线路服务密钥，并向服务提供商提供服务密钥，以便完成预配流程。 服务密钥为线路专属。

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview.png" alt-text="创建 ExpressRoute 线路":::

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>将服务密钥发送给连接服务提供商进行预配

在此页上，“提供商状态”指明了服务提供商端的当前预配状态。 “线路状态”指明了 Microsoft 端的状态。 有关线路预配状态的详细信息，请参阅[工作流](expressroute-workflows.md#expressroute-circuit-provisioning-states)一文。

创建新的 ExpressRoute 线路时，线路将为以下状态：

提供程序状态：未预配<BR>
线路状态：**已启用**

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview-provisioning-state.png" alt-text="创建 ExpressRoute 线路":::

当连接服务提供商正在为你启用线路时，线路将更改为以下状态：

提供程序状态：**预配**<BR>
线路状态：**已启用**

若要使用 ExpressRoute 线路，该线路必须处于以下状态：

提供程序状态：已预配<BR>
线路状态：**已启用**

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>定期检查线路密钥的状态

选择感兴趣的线路即可查看其属性。 选中“提供商状态”****，确保在继续之前其已转为“已预配”****。

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png" alt-text="创建 ExpressRoute 线路":::

### <a name="create-your-routing-configuration"></a>创建路由配置

有关分步说明，请参阅 [ExpressRoute 线路路由配置](expressroute-howto-routing-portal-resource-manager.md)一文，了解如何创建和修改线路对等互连。

> [!IMPORTANT]
> 这些说明仅适用于由提供第 2 层连接服务的服务提供商创建的线路。 如果服务提供商提供第 3 层托管服务（通常是 IP VPN，如 MPLS），则连接服务提供商会配置和管理路由。

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>将虚拟网络链接到 ExpressRoute 线路

接下来，将虚拟网络链接到 ExpressRoute 线路。 使用 Resource Manager 部署模式时，请参阅[将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)一文。

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>获取 ExpressRoute 线路的状态

可以选择线路并查看“概述”页，从而查看线路状态。

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>修改 ExpressRoute 线路

可以在不影响连接的情况下修改 ExpressRoute 线路的某些属性。 可以修改“配置”**** 页上的“带宽”、“SKU”、“计费模型”和“允许经典操作”。 若要了解限制，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。

可以在不停机的情况下执行以下任务：

* 为 ExpressRoute 线路启用或禁用 ExpressRoute Premium 加载项。

> [!IMPORTANT]
  > 不支持将 SKU 从“标准”/“高级”更改为“本地” 。

* 增加 ExpressRoute 线路的带宽，前提是端口上有可用容量。

  > [!IMPORTANT]
  > 不支持对线路的带宽进行降级。

* 将计量套餐从“按流量计费”更改为“不限流量”。

  > [!IMPORTANT]
  > 不支持将数据流量套餐从“不限流量”更改为“按流量计费”。

* 可以启用和禁用允许经典操作**。
  > [!IMPORTANT]
  > 如果现有端口上的容量不足，可能需要重新创建 ExpressRoute 线路。 如果该位置没有额外的可用容量，则不能升级线路。
  >
  > 尽管你可无缝升级带宽，但是无法在不中断的情况下降低 ExpressRoute 线路的带宽。 带宽降级需要取消对 ExpressRoute 线路的预配，并重新预配新的 ExpressRoute 线路。
  >
  > 如果要使用的资源超出了标准线路所允许的范围，可能无法禁用 Premium 加载项。

若要修改 ExpressRoute 线路，请选择“配置”。

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-configuration.png" alt-text="创建 ExpressRoute 线路":::

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>取消预配 ExpressRoute 线路

如果 ExpressRoute 线路服务提供商预配状态为“正在预配”或“已预配”，则必须与服务提供商合作，在他们一端取消预配线路。 在服务提供商完成取消设置线路并通知我们之前，我们会继续保留资源并向你收费。

> [!NOTE]
>* 在取消预配前，必须取消所有虚拟网络与 ExpressRoute 线路的链接。 如果此操作失败，请检查是否有虚拟网络链接到了该线路。
>* 如果服务提供商已取消设置线路（服务提供商预配状态设置为“未预配”），可以删除线路。 这样就会停止对线路的计费。


## <a name="clean-up-resources"></a><a name="cleanup"></a>清理资源

可以选择“删除”图标来删除 ExpressRoute 线路。 

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-delete.png" alt-text="创建 ExpressRoute 线路":::

## <a name="next-steps"></a>后续步骤

创建线路后，请继续执行下一步：

> [!div class="nextstepaction"]
> [创建和修改 ExpressRoute 线路的路由](expressroute-howto-routing-portal-resource-manager.md)
