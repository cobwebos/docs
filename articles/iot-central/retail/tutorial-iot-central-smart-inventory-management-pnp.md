---
title: IoT 智能库存管理教程 | Microsoft Docs
description: 适用于 IoT Central 的智能库存管理应用程序模板教程
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 8360db3e31899dc7ca0a2b502d0036358f0858d6
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615230"
---
# <a name="tutorial-deploy-and-walk-through-a-smart-inventory-management-application-template"></a>教程：部署和演练智能库存管理应用程序模板

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本教程介绍如何开始部署 IoT Central“智能库存管理”应用程序模板  。 你将了解如何部署模板、有哪些现成的内容以及接下来要执行哪些操作。

本教程介绍如何执行下列操作： 
* 创建智能库存管理应用程序 
* 演练应用程序 

## <a name="prerequisites"></a>先决条件
* 无需满足特定的先决条件，就可以部署此应用
* 建议使用 Azure 订阅，但不使用也可以

## <a name="create-smart-inventory-management-application-template"></a>创建智能库存管理应用程序模板

可以使用以下步骤创建应用程序
1. 导航到 Azure IoT Central 应用程序管理器网站。 从左侧导航栏中选择“生成”，然后单击“零售”选项卡   。

> [!div class="mx-imgBorder"]
> ![智能库存管理仪表板](./media/tutorial-iot-central-smart-inventory-management/iotc_retail_homepage.png)

2. 选择“零售”选项卡，然后在“智能库存管理”下选择“创建应用”   ****

3. “创建应用”将打开“新建应用程序”窗体，并按如下所示填写所需的详细信息  。
   **应用程序名称**：可以使用默认的建议名称，也可以输入易记的应用程序名称。
   **URL**：可以使用建议的默认 URL，也可以输入唯一且容易记住的 URL。 接下来，如果你已有一个 Azure 订阅，则建议使用默认设置。否则，你可以开始使用七天免费试用，并选择在免费试用到期之前的任何时间内转换为即用即付。
   **计费信息**：必须提供目录、Azure 订阅和区域详细信息才能配置资源。
   **创建**：选择页面底部的“创建”以部署应用程序。

> [!div class="mx-imgBorder"]
> ![智能库存管理仪表板](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_app_create.png)

## <a name="walk-through-the-application"></a>演练应用程序 

### <a name="dashboard"></a>仪表板 
成功部署应用模板后，默认仪表板是一个以智能库存管理操作员为中心的门户。 Northwind Trader 是一家虚构的智能库存提供商，使用低耗电蓝牙 (BLE) 管理仓库并使用射频识别 (RFID) 管理零售商店。 在此仪表板中，你将看到两个不同的网关，它们提供有关库存的遥测数据以及可执行的相关命令、作业和操作。 此仪表板已预先配置为展示关键的库存管理设备操作活动。
仪表板在逻辑上区分两个不同的网关设备管理操作， 
   * 仓库在托盘上部署了固定的 BLE 网关和 BLE 标记，以在大型设备上监测和跟踪库存
   * 零售商店通过在每个商品级别使用固定的 RFID 网关和 RFID 标签来实现监测和跟踪商店中的库存
   * 查看网关的位置、状态和相关详细信息 

> [!div class="mx-imgBorder"]
> ![智能库存管理仪表板](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard1.png)

   * 可以轻松地跟踪网关、活动和未知标记的总数。
   * 可以执行设备管理操作，例如更新固件、禁用传感器、启用传感器、更新传感器阈值、更新遥测间隔时间和更新设备服务协定
   * 网关设备可以通过完整或增量扫描来执行按需库存管理。

> [!div class="mx-imgBorder"]
> ![智能库存管理仪表板](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard2.png)

## <a name="device-template"></a>设备模板
单击“设备模板”选项卡，你将看到网关功能模型。 功能模型是围绕两个不同的接口（“网关遥测与属性”以及“网关命令”）构建的  

**网关遥测与属性** - 此接口表示与传感器、位置和设备信息，以及设备孪生属性功能（例如网关阈值和更新间隔时间）有关的所有遥测。

> [!div class="mx-imgBorder"]
> ![智能库存管理仪表板](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate1.png)


**网关命令** - 此接口组织了所有网关命令功能

> [!div class="mx-imgBorder"]
> ![智能库存管理仪表板](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate2.png)

## <a name="rules"></a>规则
选择“规则”选项卡以查看此应用程序模板中存在的两个不同规则。 这些规则配置为通过电子邮件将通知发送给操作员以进行进一步调查。

**离线网关**：如果网关长时间未向云报告，则触发此规则。 低电量模式、断开连接和设备运行状况不良都可能使网关不响应。

**未知标记**：跟踪与资产关联的每个 RFID 和 BLE 标记至关重要。 如果网关检测到过多未知标记，则表明标记源应用程序存在同步问题。

> [!div class="mx-imgBorder"]
> ![智能库存管理仪表板](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_rules.png)

## <a name="jobs"></a>作业
选择“作业”选项卡以查看此应用程序模板中存在的五个不同的作业：可以使用作业功能来执行解决方案范围的操作。 此处，库存管理作业将使用设备命令和孪生功能执行以下任务：
   * 在整个网关中禁用读取器
   * 修改网关之间的遥测阈值 
   * 在整个解决方案中执行按需库存扫描。

> [!div class="mx-imgBorder"]
> ![智能库存管理仪表板](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_jobs.png)

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，请访问“管理” > “应用程序设置”并单击“删除”，以删除应用程序模板    。

> [!div class="mx-imgBorder"]
> ![智能库存管理仪表板](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_cleanup.png)

## <a name="next-steps"></a>后续步骤
* 详细了解智能库存管理[智能库存管理的概念](./architecture-smart-inventory-management-pnp.md)
* 详细了解其他 [IoT Central 零售模板](./overview-iot-central-retail-pnp.md)
* 请参阅 [IoT Central 概述](../core/overview-iot-central-pnp.md)，详细了解 IoT Central
