---
title: 在 Azure 逻辑应用中管理集成服务环境
description: 在适用于 Azure 逻辑应用的 integration service 环境 (ISE) 中查看网络运行状况和管理逻辑应用、连接、自定义连接器和集成帐户
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 8f10e3d3fd7c67d1e803e8f85c9918c91bb81d59
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517457"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>在 Azure 逻辑应用中管理集成服务环境 (ISE)

若要检查[integration service 环境 (ise)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)的网络运行状况, 并管理 ISE 中存在的逻辑应用、连接、集成帐户和自定义连接器, 请按照本主题中的步骤进行操作。

## <a name="view-your-ise"></a>查看 ISE

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在门户的搜索框中, 输入 "integration service 环境", 然后选择 " **Integration Service 环境**"。

   ![查找 integration service 环境](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. 从结果列表中, 选择你的 integration service 环境。

   ![选择集成服务环境](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. 转到下一节, 查找 ISE 中的逻辑应用、连接、连接器或集成帐户。

<a name="check-network-health"></a>

## <a name="check-network-health"></a>检查网络运行状况

在 ISE 菜单上的 "**设置**" 下, 选择 "**网络运行状况**"。 此窗格显示你的子网的运行状况状态以及其他服务的出站依赖关系。

![检查网络运行状况](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>管理逻辑应用

1. 在 ISE 菜单上的 "**设置**" 下, 选择 "**逻辑应用**"。

   ![查找逻辑应用](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. 若要在不再需要时从 ISE 中删除逻辑应用, 请选择这些逻辑应用, 然后选择 "**删除**"。

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>管理 API 连接

1. 若要查看在 ISE 中运行的逻辑应用所创建的 API 连接, 请在 ISE 菜单上的 "**设置**" 下, 选择 " **API 连接**"。

   ![查找 API 连接](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. 若要在不再需要时从 ISE 中删除连接, 请选择这些连接, 然后选择 "**删除**"。

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>管理自定义连接器

1. 若要查看在 ISE 中创建的自定义连接器, 请在 ISE 菜单上的 "**设置**" 下, 选择 "**自定义连接器**"。

   ![查找自定义连接器](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. 若要在不再需要时从 ISE 中删除自定义连接器, 请选择这些连接器, 然后选择 "**删除**"。

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>管理集成帐户

1. 在 ISE 菜单上的 "**设置**" 下, 选择 "**集成帐户**"。

   ![查找集成帐户](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. 若要在不再需要时从 ISE 中删除集成帐户, 请选择这些集成帐户, 然后选择 "**删除**"。

## <a name="next-steps"></a>后续步骤

* 了解如何[从独立的逻辑应用连接到 Azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
