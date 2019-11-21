---
title: 快速入门：在 IoT 中心启用适用于 IoT 的 Azure 安全中心服务
description: 在本快速入门中，了解如何在 IoT 中心启用适用于 IoT 的 Azure 安全中心服务。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 641ba4c8d3b0e54132c19a493e1e4bf17bb28e13
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904111"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>快速入门：在 IoT 中心启用适用于 IoT 的 Azure 安全中心服务

本文介绍如何在现有 IoT 中心启用适用于 IoT 的 Azure 安全中心服务。 如果当前没有 IoT 中心，请参阅[使用 Azure 门户创建 IoT 中心](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal)以开始操作。 

> [!NOTE]
> 适用于 IoT 的 Azure 安全中心目前仅支持标准层 IoT 中心。


## <a name="prerequisites-for-enabling-the-service"></a>启用服务的先决条件

- Log Analytics 工作区
  - 默认情况下，适用于 IoT 的 Azure 安全中心将两种类型的信息存储在 Log Analytics 工作区中：**安全警报**和**建议**。 
  - 可以选择添加另一信息类型（**原始事件**）的存储。 请注意，在 Log Analytics 中存储**原始事件**会带来额外的存储成本。 
- IoT 中心（标准层）
- 满足所有[服务先决条件](service-prerequisites.md) 

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>在 IoT 中心启用适用于 IoT 的 Azure 安全中心 

若要在 IoT 中心启用安全性，请执行以下操作： 

1. 在 Azure 门户中打开你的 **IoT 中心**。 
1. 在“安全性”  菜单下，单击“保护 IoT 解决方案”  。    


祝贺你！ 你已完成在 IoT 中心启用适用于 IoT 的 Azure 安全中心。 

### <a name="geolocation-and-ip-address-handling"></a>地理位置和 IP 地址处理

为了确保 IoT 解决方案的安全，系统会默认收集并存储 IoT 设备、IoT Edge 和 IoT 中心的传入和传出连接的 IP 地址。 该信息对于检测来自可疑 IP 源的异常连接很重要。 例如，尝试从已知僵尸网络的 IP 源或所在地理位置外部的 IP 源建立连接时，就是这种情况。 适用于 IoT 的 Azure 安全中心服务允许用户灵活地随时启用和禁用对 IP 地址数据的收集。 

若要启用或禁用对 IP 地址数据的收集，请执行以下操作： 

1. 打开 IoT 中心，然后从“安全性”菜单中选择“概览”。   
2. 选择“设置”屏幕，  ，根据需要修改地理位置和/或 IP 处理设置。

### <a name="log-analytics-creation"></a>创建 Log Analytics

启用适用于 IoT 的 Azure 安全中心以后，将会创建默认的 Azure Log Analytics 工作区，用于存储 IoT 设置、IoT Edge 和 IoT 中心的原始安全事件、警报和建议。 每个客户每月引入 Azure Log Analytics 服务的前五 (5) GB 数据免费。 引入 Azure Log Analytics 工作区的每 GB 数据均可免费保存 31 天。 了解有关 [Log Analytics](https://azure.microsoft.com/pricing/details/monitor/) 定价的详细信息。

若要更改 Log Analytics 的工作区配置，请执行以下操作：

1. 打开 IoT 中心，然后从“安全性”菜单中选择“概览”。   
2. 选择“设置”屏幕，  ，根据需要修改 Log Analytics 设置的工作区配置。

### <a name="customize-your-iot-security-solution"></a>自定义 IoT 安全解决方案
默认情况下，启用适用于 IoT 的 Azure 安全中心解决方案时，会自动保护 Azure 订阅下的所有 IoT 中心。 

若要在特定 IoT 中心启用或禁用适用于 IoT 的 Azure 安全中心服务，请执行以下操作： 

1. 打开 IoT 中心，然后从“安全性”菜单中选择“概览”。   
2. 选择“设置”屏幕，  ，根据需要修改 Azure 订阅中任何 IoT 中心的安全设置。


## <a name="next-steps"></a>后续步骤

转到下一篇文章，了解如何配置解决方案...

> [!div class="nextstepaction"]
> [配置解决方案](quickstart-configure-your-solution.md)
