---
title: 'Azure 对等互连服务：创建 '
description: 本教程介绍如何注册 Azure 对等互连服务和前缀。
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/2/2020
ms.author: derekol
Customer intent: With Azure Peering service enhancing the customer connectivity to Microsoft cloud services .
ms.openlocfilehash: f04ae0d2e541be0a7984e944298fa8d7b47fd126
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "84870570"
---
# <a name="tutorial-create-a-peering-service-connection"></a>教程：创建对等互连服务连接

本教程演示如何创建对等互连服务资源，以及如何配置对等互连服务连接。 

1. 若要注册对等互连服务连接，请选择“创建资源” ****  >“对等互连服务” **** 。

 
    ![注册对等互连服务](./media/peering-service-portal/peering-servicecreate.png)

2. 在“创建对等互连服务连接”页面上的“基本信息”选项卡上输入以下详细信息。
 
3. 选择订阅以及与订阅关联的资源组。

    ![注册对等互连服务基本信息选项卡](./media/peering-service-portal/peering-servicebasics.png)

4. 输入对等互连服务实例应注册到的“名称”。

5. 现在，选择页面底部的“下一步: 配置”按钮。 “配置”页面随即出现。
## <a name="configure-the-peering-service-connection"></a>配置对等互连服务连接

1. 在“配置”页面上，从“对等互连服务位置”下拉列表中选择相同位置，从而选择必须启用对等互连服务的位置。

1. 通过从“对等互连服务提供商”下拉列表中选择提供商名称，来选择必须从中获取对等互连服务的服务提供商。
 
1. 选择“前缀”部分底部的“创建新前缀”，文本框随即出现。 现在，请输入前缀资源的名称以及与服务提供商关联的前缀。

1. 选择“前缀键”并添加提供商（ISP 或 IXP）提供给你的前缀键。 此前缀使 MS 可以验证前缀以及分配 IP 前缀的提供商。

    ![注册对等互连服务配置选项卡](./media/peering-service-portal/peering-serviceconfiguration.png)

1. 在页面左下角选择“审阅 + 创建”按钮。 “审阅 + 创建”页面随即出现，Azure 会验证你的配置。

 1. 当看到显示“验证通过”消息时，选择“创建” 。

> ![注册对等互连服务配置选项卡](./media/peering-service-portal/peering-service-prefix.png)

1. 注册对等互连服务连接之后，对包含的前缀执行其他验证。 可以在资源名称的“前缀”部分下查看验证状态。 如果验证失败，则显示以下错误消息之一：

   - 对等互连服务前缀无效，前缀应为有效格式，仅支持 Ipv4 前缀。
   - 未从对等互连服务提供商收到前缀。
   - 前缀公告没有有效的 BGP 社区，请与对等互连服务提供商联系。
   - 找不到备份路由，请与对等互连服务提供商联系。
   - 收到的前缀的 AS 路径较长，请与对等互连服务提供商联系。
   - 收到的前缀在路径中包含专用 AS，请与对等互连服务提供商联系。

### <a name="add-or-remove-a-prefix"></a>添加或删除前缀

在“前缀”页面上选择“添加前缀”以添加前缀。

选择列出的前缀旁的省略号 (...)，然后选择“删除”选项。

### <a name="delete-a-peering-service-connection"></a>删除对等互连服务连接

在“所有资源”页面上，选中“对等互连服务”的复选框，然后选择页面顶部的“删除”选项。
## <a name="next-steps"></a>后续步骤

- 若要了解对等互连服务连接，请参阅[对等互连服务连接](connection.md)。
- 若要了解对等互连服务连接遥测数据，请参阅[对等互连服务连接遥测数据](connection-telemetry.md)。
- 若要度量遥测，请参阅[度量连接遥测](measure-connection-telemetry.md)。
- 若要使用 Azure PowerShell 注册连接，请参阅[注册对等互连服务连接 - Azure PowerShell](powershell.md)。
- 若要使用 Azure CLI 注册连接，请参阅[注册对等互连服务连接 - Azure CLI](cli.md)。