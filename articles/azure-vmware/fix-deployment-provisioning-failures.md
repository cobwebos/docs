---
title: 获取有关 Azure VMware 解决方案部署或预配失败的帮助
description: 如何从 Azure VMware 解决方案私有云获取所需信息，以便为 Azure VMware 解决方案部署或预配故障提供服务请求。
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 63d5440a9e2b15463e465e1d32762889508feca1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88752236"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>获取有关 Azure VMware 解决方案部署或预配失败的帮助

本文介绍如何通过在 Azure 门户中打开服务请求 (SR) ，来帮助你在私有云上部署 Azure VMware 解决方案或预配失败。 但首先，您需要在 Azure 门户中收集一些关键信息。 在大多数情况下，你需要：

- 失败部署的相关 ID () 
- 当尝试使用私有云 ExpressRoute 线路缩放或对现有私有云进行缩放时，ExpressRoute 线路 ID (并且失败) 

## <a name="collect-the-correlation-id"></a>收集相关 ID
 
让我们先看一下相关 ID。 当你在 Azure) 中创建私有云 (或任何资源时，将生成关联的关联 ID。 每个 Azure 资源管理器部署还会生成一个唯一的相关性 ID。 此 ID 可实现更快的 SR 创建和解决。 
 
下面是失败的私有云部署的输出示例，其中突出显示了相关 ID。

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="具有相关 ID 的私有云部署失败。":::

复制并保存要包含在服务请求中的相关 ID。 有关详细信息，请参阅本文末尾的 [创建支持请求](#create-your-support-request) 。

如果在预验证阶段中发生故障，则在部署私有云之前，不会生成相关 ID。 在这种情况下，只需提供在创建 Azure VMware 解决方案私有云时使用的信息，其中包括：

- 位置
- 资源组
- 资源名称
 
### <a name="collect-a-summary-of-errors"></a>收集错误摘要

任何错误的详细信息也有助于解决问题。 在上面的屏幕中，选择 "单击此处 (突出显示的 **详细信息**) 并打开错误摘要，如以下屏幕截图所示。
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="具有相关 ID 的私有云部署失败。":::

同样，复制并保存此摘要，并将其保存到 SR 中。
 
### <a name="retrieve-past-deployments"></a>检索过去的部署

通过选择 "通知" 图标，可以在访问的部署活动日志中搜索，以检索过去的部署，包括失败的部署。

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="具有相关 ID 的私有云部署失败。" 中的 "更多事件"**。

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="具有相关 ID 的私有云部署失败。":::

然后搜索资源的名称，或搜索在创建资源时使用的其他唯一信息，以查找失败的部署及其相关 ID。 以下示例显示 (pc03) 的私有云资源上的搜索结果。
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="具有相关 ID 的私有云部署失败。" 选项卡，然后查找 correlationId。 Copy 和 include in。 
 
## <a name="collect-the-expressroute-id-uri"></a>收集 ExpressRoute ID (URI) 
 
也许你已有一个私有云，尝试使用私有云 ExpressRoute 线路来扩展 it 或对等节点时遇到失败。 在这种情况下，可以在创建 SR 时使用私有云的 ExpressRoute ID 来识别它。

在门户中查看私有云时，请选择 " **连接" > ExpressRoute** ，并将 **ExpressRoute ID** 复制到剪贴板。
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="具有相关 ID 的私有云部署失败。" [收集错误摘要](#collect-a-summary-of-errors)"。

## <a name="create-your-support-request"></a>创建支持请求

有关创建支持请求的一般指南，请参阅 [如何创建 Azure 支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md)。 

下面是有关创建适用于 Azure VMware 解决方案部署或预配失败的 SR 的其他指导。

1. 选择 " **帮助** " 图标，然后选择 " **+ 新建支持请求**"。

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="具有相关 ID 的私有云部署失败。" 来创建 SR。
