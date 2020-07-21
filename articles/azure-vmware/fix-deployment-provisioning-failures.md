---
title: 获取有关 Azure VMware 解决方案部署或预配失败的帮助
description: 如何从 Azure VMware 解决方案（AVS）私有云获取所需信息，以将服务请求用于 AVS 部署或预配失败。
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 05c9dee088b37c37cdcdee7e745cdcd2222b63e0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86514571"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>获取有关 Azure VMware 解决方案部署或预配失败的帮助

本文介绍如何通过在 Azure 门户中打开服务请求（SR）来帮助你在私有云上部署 Azure VMware 解决方案（AVS）或预配失败。 但首先，您需要在 Azure 门户中收集一些关键信息。 在大多数情况下，你需要：

- 相关 ID （失败的部署）
- ExpressRoute 线路 ID （尝试使用私有云 ExpressRoute 线路扩展或对现有私有云进行扩展时，它会失败）

## <a name="collect-the-correlation-id"></a>收集相关 ID
 
让我们先看一下相关 ID。 当你创建私有云（或 Azure 中的任何资源）时，将生成关联的关联 ID。 每个 Azure 资源管理器部署还会生成一个唯一的相关性 ID。 此 ID 可实现更快的 SR 创建和解决。 
 
下面是失败的私有云部署的输出示例，其中突出显示了相关 ID。

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="具有相关 ID 的私有云部署失败。":::

复制并保存要包含在服务请求中的相关 ID。 有关详细信息，请参阅本文末尾的[创建支持请求](#create-your-support-request)。

如果在预验证阶段中发生故障，则在部署私有云之前，不会生成相关 ID。 在这种情况下，你可以简单地提供创建 AVS 私有云时使用的信息，包括：

- 位置
- 资源组
- 资源名称
 
### <a name="collect-a-summary-of-errors"></a>收集错误摘要

任何错误的详细信息也有助于解决问题。 在上一个屏幕中，选择 "**单击此处查看详细信息**（突出显示）"，此时将打开 "错误摘要"，如以下屏幕截图所示。
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="错误的摘要。":::

同样，复制并保存此摘要，并将其保存到 SR 中。
 
### <a name="retrieve-past-deployments"></a>检索过去的部署

通过选择 "通知" 图标，可以在访问的部署活动日志中搜索，以检索过去的部署，包括失败的部署。

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="打开通知。":::

在 "通知" 中，选择 **"活动日志" 中的 "更多事件"**。

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="链接：活动日志中的更多事件。":::

然后搜索资源的名称，或搜索在创建资源时使用的其他唯一信息，以查找失败的部署及其相关 ID。 以下示例显示了私有云资源（pc03）上的搜索结果。
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="查找过去失败的 AVS 部署。":::
 
选择失败的部署的操作名称将打开一个窗口，其中包含详细信息。 选择 "JSON" 选项卡，然后查找 correlationId。 Copy 和 include in。 
 
## <a name="collect-the-expressroute-id-uri"></a>收集 ExpressRoute ID （URI）
 
也许你已有一个私有云，尝试使用私有云 ExpressRoute 线路来扩展 it 或对等节点时遇到失败。 在这种情况下，可以在创建 SR 时使用私有云的 ExpressRoute ID 来识别它。

在门户中查看私有云时，请选择 "**连接" > ExpressRoute** ，并将**ExpressRoute ID**复制到剪贴板。
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="收集 ExpressRoute ID。"::: 
 
将 ExpressRoute ID 粘贴到新支持请求中的相应字段。 有关详细信息，请参阅下一节[创建支持请求](#create-your-support-request)。
 
> [!NOTE]
> 有时，预验证检查在部署之前可能会失败，并且唯一可用的信息将是错误和/或失败消息。 这对于一些故障（例如与配额相关的问题）非常有用，在支持请求中包含这些消息很重要。 若要收集这些内容，请参阅前面的 "[收集错误摘要](#collect-a-summary-of-errors)"。

## <a name="create-your-support-request"></a>创建支持请求

有关创建支持请求的一般指南，请参阅[如何创建 Azure 支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md)。 

下面是针对 AVS 部署或预配失败创建 SR 的其他指导。

1. 选择 "**帮助**" 图标，然后选择 " **+ 新建支持请求**"。

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="收集 ExpressRoute ID。":::

2. 填写所有必填字段，然后在 "**基本**信息" 选项卡上：

    - 对于 "**问题类型**"，请选择 "**配置" 和 "安装问题**"。

    - 对于**问题子类型**，请选择 "**预配私有云**"。

3. 在 "**详细信息**" 选项卡上：

    - 填写所有必填字段。

    - 将相关 ID 或 ExpressRoute ID 粘贴到提供的特定字段中。 如果看不到这些字段的特定字段，可将其粘贴到 "**提供有关该问题的详细信息**" 下的文本框中。

    - 将任何错误详细信息（包括您复制的错误摘要）粘贴到 "**提供有关该问题的详细信息**" 下的文本框中。

4. 查看并选择 "**创建**" 来创建 SR。
